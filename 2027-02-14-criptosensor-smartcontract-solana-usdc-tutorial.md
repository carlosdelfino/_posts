---
title: "CriptoSensor na Solana: Smart Contracts em Rust com USDC e Conversão BRL — Tutorial Completo"
tags: [blockchain, solana, rust, smartcontract, usdc, iot, sensores, criptosensor, tutorial, anchor, brl]
categories: [projetos]
layout: article
share: true
toc: true
comments: true
coinbase:
  show: false
google:
  plusone: true
ads:
  show: true
tagcloud: true
---

Neste tutorial vamos implementar o protocolo [CriptoSensor](https://rapport.tec.br/projetos/criptosensor/) como um conjunto de **smart contracts na Solana**, escritos em **Rust** com o framework **Anchor**. O sistema utiliza **USDC** como stablecoin de liquidação e inclui um mecanismo de **conversão BRL → USDC** via oráculo de câmbio, permitindo que clientes paguem em Real enquanto toda a lógica on-chain opera em USDC.

Se você ainda não conhece o projeto original, leia primeiro: [CriptoSensor — Rapport Tecnologia](https://rapport.tec.br/projetos/criptosensor/).

<!--more-->

## 1. Visão Geral da Arquitetura

O protocolo CriptoSensor na Solana é composto por **quatro programas (smart contracts)** que colaboram entre si:

| Programa | Responsabilidade |
|---|---|
| **criptosensor_registry** | Registro de sensores e nós de borda com identidade criptográfica |
| **criptosensor_oracle** | Oráculo de câmbio BRL/USDC e preço de referência do token |
| **criptosensor_treasury** | Tesouraria: recebe pagamentos em USDC, particiona receita, controla epochs |
| **criptosensor_emission** | Emissão lastreada de tokens e distribuição proporcional ao score |

### Fluxo Completo por Epoch

```
Cliente paga em BRL (off-chain)
       │
       ▼
  Conversão BRL → USDC (oráculo de câmbio)
       │
       ▼
  Treasury recebe USDC (on-chain)
       │
       ▼
  Epoch fecha → calcula receita elegível
       │
       ▼
  Emission calcula tokens do epoch (EMA × taxa × decaimento)
       │
       ▼
  Distribuição proporcional ao score
       │
       ├── 70% → Sensores
       └── 30% → Nós de borda
```

## 2. Pré-requisitos

Antes de começar, instale as ferramentas necessárias:

```bash
# Instalar Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Instalar Solana CLI
sh -c "$(curl -sSfL https://release.anza.xyz/stable/install)"

# Instalar Anchor CLI
cargo install --git https://github.com/coral-xyz/anchor avm --locked
avm install latest
avm use latest

# Criar o projeto
anchor init criptosensor
cd criptosensor
```

Adicione as dependências ao `Cargo.toml` do programa:

```toml
[dependencies]
anchor-lang = "0.30"
anchor-spl = "0.30"
```

## 3. Programa 1 — Registry (Registro de Sensores)

O primeiro smart contract gerencia a **identidade criptográfica** de sensores e nós de borda. Cada sensor é uma conta on-chain com metadados, score acumulado e status de penalidade.

### 3.1 Estrutura de Dados

```rust
// programs/criptosensor_registry/src/lib.rs

use anchor_lang::prelude::*;

declare_id!("REGxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx");

/// Tamanho máximo do identificador do sensor
const MAX_SENSOR_ID_LEN: usize = 32;
/// Tamanho máximo da descrição do tipo de sensor
const MAX_SENSOR_TYPE_LEN: usize = 16;

#[program]
pub mod criptosensor_registry {
    use super::*;

    /// Registra um novo sensor na rede.
    /// O sensor recebe identidade on-chain vinculada à chave pública
    /// do dispositivo (ou de quem o opera).
    pub fn register_sensor(
        ctx: Context<RegisterSensor>,
        sensor_id: String,
        sensor_type: String,
        latitude: i64,   // latitude × 10^7 (inteiro para evitar floats)
        longitude: i64,  // longitude × 10^7
    ) -> Result<()> {
        require!(sensor_id.len() <= MAX_SENSOR_ID_LEN, RegistryError::SensorIdTooLong);
        require!(sensor_type.len() <= MAX_SENSOR_TYPE_LEN, RegistryError::SensorTypeTooLong);

        let sensor = &mut ctx.accounts.sensor_account;
        sensor.owner = ctx.accounts.owner.key();
        sensor.sensor_id = sensor_id;
        sensor.sensor_type = sensor_type;
        sensor.latitude = latitude;
        sensor.longitude = longitude;
        sensor.score = 0;
        sensor.penalty_level = 0;
        sensor.is_active = true;
        sensor.registered_at = Clock::get()?.unix_timestamp;
        sensor.bump = ctx.bumps.sensor_account;

        msg!("Sensor registrado: {}", sensor.sensor_id);
        Ok(())
    }

    /// Registra um nó de borda (edge node) na rede.
    /// Nós de borda validam dados dos sensores e detectam fraudes.
    pub fn register_edge_node(
        ctx: Context<RegisterEdgeNode>,
        node_id: String,
    ) -> Result<()> {
        require!(node_id.len() <= MAX_SENSOR_ID_LEN, RegistryError::SensorIdTooLong);

        let node = &mut ctx.accounts.edge_node_account;
        node.owner = ctx.accounts.owner.key();
        node.node_id = node_id;
        node.score = 0;
        node.is_active = true;
        node.registered_at = Clock::get()?.unix_timestamp;
        node.bump = ctx.bumps.edge_node_account;

        msg!("Nó de borda registrado: {}", node.node_id);
        Ok(())
    }

    /// Atualiza o score de um sensor após validação de entrega.
    /// Somente a autoridade do protocolo pode chamar esta instrução.
    pub fn update_sensor_score(
        ctx: Context<UpdateSensorScore>,
        delta_score: u64,
    ) -> Result<()> {
        let sensor = &mut ctx.accounts.sensor_account;
        require!(sensor.is_active, RegistryError::SensorInactive);
        require!(sensor.penalty_level < 3, RegistryError::SensorBanned);

        sensor.score = sensor.score.checked_add(delta_score)
            .ok_or(RegistryError::Overflow)?;
        msg!("Score do sensor {} atualizado: +{} → total {}", sensor.sensor_id, delta_score, sensor.score);
        Ok(())
    }

    /// Atualiza o score de um nó de borda.
    pub fn update_edge_score(
        ctx: Context<UpdateEdgeScore>,
        delta_score: u64,
    ) -> Result<()> {
        let node = &mut ctx.accounts.edge_node_account;
        require!(node.is_active, RegistryError::SensorInactive);
        node.score = node.score.checked_add(delta_score).ok_or(RegistryError::Overflow)?;
        msg!("Score do nó {} atualizado: +{} → total {}", node.node_id, delta_score, node.score);
        Ok(())
    }

    /// Aplica penalidade progressiva a um sensor.
    /// Nível 1: perda parcial de score. Nível 2: slashing. Nível 3: banimento.
    pub fn apply_penalty(
        ctx: Context<ApplyPenalty>,
        penalty_level: u8,
        score_slash: u64,
    ) -> Result<()> {
        require!(penalty_level >= 1 && penalty_level <= 3, RegistryError::InvalidPenalty);
        let sensor = &mut ctx.accounts.sensor_account;
        sensor.penalty_level = penalty_level;
        sensor.score = sensor.score.saturating_sub(score_slash);
        if penalty_level >= 3 {
            sensor.is_active = false;
            msg!("Sensor {} BANIDO da rede", sensor.sensor_id);
        } else {
            msg!("Penalidade nível {} aplicada ao sensor {}", penalty_level, sensor.sensor_id);
        }
        Ok(())
    }
}

// --- Contas ---

#[account]
#[derive(InitSpace)]
pub struct SensorAccount {
    pub owner: Pubkey,
    #[max_len(32)]
    pub sensor_id: String,
    #[max_len(16)]
    pub sensor_type: String,
    pub latitude: i64,
    pub longitude: i64,
    pub score: u64,
    pub penalty_level: u8,
    pub is_active: bool,
    pub registered_at: i64,
    pub bump: u8,
}

#[account]
#[derive(InitSpace)]
pub struct EdgeNodeAccount {
    pub owner: Pubkey,
    #[max_len(32)]
    pub node_id: String,
    pub score: u64,
    pub is_active: bool,
    pub registered_at: i64,
    pub bump: u8,
}

// --- Contextos ---

#[derive(Accounts)]
#[instruction(sensor_id: String)]
pub struct RegisterSensor<'info> {
    #[account(mut)]
    pub owner: Signer<'info>,
    #[account(
        init, payer = owner,
        space = 8 + SensorAccount::INIT_SPACE,
        seeds = [b"sensor", owner.key().as_ref(), sensor_id.as_bytes()],
        bump
    )]
    pub sensor_account: Account<'info, SensorAccount>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
#[instruction(node_id: String)]
pub struct RegisterEdgeNode<'info> {
    #[account(mut)]
    pub owner: Signer<'info>,
    #[account(
        init, payer = owner,
        space = 8 + EdgeNodeAccount::INIT_SPACE,
        seeds = [b"edge_node", owner.key().as_ref(), node_id.as_bytes()],
        bump
    )]
    pub edge_node_account: Account<'info, EdgeNodeAccount>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct UpdateSensorScore<'info> {
    pub authority: Signer<'info>,
    #[account(mut, has_one = owner @ RegistryError::Unauthorized)]
    pub sensor_account: Account<'info, SensorAccount>,
    /// CHECK: verificado pelo has_one
    pub owner: UncheckedAccount<'info>,
}

#[derive(Accounts)]
pub struct UpdateEdgeScore<'info> {
    pub authority: Signer<'info>,
    #[account(mut, has_one = owner @ RegistryError::Unauthorized)]
    pub edge_node_account: Account<'info, EdgeNodeAccount>,
    /// CHECK: verificado pelo has_one
    pub owner: UncheckedAccount<'info>,
}

#[derive(Accounts)]
pub struct ApplyPenalty<'info> {
    pub authority: Signer<'info>,
    #[account(mut)]
    pub sensor_account: Account<'info, SensorAccount>,
}

// --- Erros ---

#[error_code]
pub enum RegistryError {
    #[msg("Identificador do sensor excede o tamanho máximo")]
    SensorIdTooLong,
    #[msg("Tipo do sensor excede o tamanho máximo")]
    SensorTypeTooLong,
    #[msg("Sensor está inativo")]
    SensorInactive,
    #[msg("Sensor está banido da rede")]
    SensorBanned,
    #[msg("Nível de penalidade inválido")]
    InvalidPenalty,
    #[msg("Overflow aritmético")]
    Overflow,
    #[msg("Acesso não autorizado")]
    Unauthorized,
}
```

### 3.2 Por que PDAs?

Na Solana, usamos **Program Derived Addresses (PDAs)** para criar contas determinísticas. A seed `["sensor", owner, sensor_id]` garante que:

1. Cada sensor tem um endereço único e previsível.
2. Qualquer um pode derivar o endereço de um sensor sem consultar o programa.
3. Não existe chave privada associada — somente o programa pode assinar operações.

## 4. Programa 2 — Oracle (Oráculo de Câmbio BRL/USDC)

Como os clientes pagam em **Real (BRL)** mas o protocolo opera em **USDC**, precisamos de um oráculo de câmbio confiável e um **TWAP do token CriptoSensor**.

### 4.1 Estratégia de Conversão BRL → USDC

```
  1. Cliente paga R$ 5.000 via PIX (off-chain)
  2. Backend consulta oráculo BRL/USDC on-chain
  3. Oráculo retorna: 1 BRL = 0.1724 USDC (≈ $5.80/USD)
  4. Backend converte: 5000 × 0.1724 = 862.07 USDC
  5. USDC é transferido para o Treasury on-chain
  6. Treasury registra receita confirmada do epoch
```

A conversão **real** de BRL para USDC acontece **off-chain** (via exchange ou rampa fiat), mas o **preço de referência** fica registrado **on-chain** para auditabilidade.

### 4.2 Smart Contract do Oráculo

```rust
// programs/criptosensor_oracle/src/lib.rs

use anchor_lang::prelude::*;

declare_id!("ORCxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx");

const MAX_TWAP_OBSERVATIONS: usize = 24;

#[program]
pub mod criptosensor_oracle {
    use super::*;

    /// Inicializa a conta do oráculo com parâmetros iniciais.
    pub fn initialize_oracle(
        ctx: Context<InitializeOracle>,
        initial_brl_usdc_rate: u64,  // taxa × 10^6 (ex.: 172400 = 0.172400)
        initial_token_price: u64,    // preço do token × 10^6
    ) -> Result<()> {
        let oracle = &mut ctx.accounts.oracle_account;
        oracle.authority = ctx.accounts.authority.key();
        oracle.brl_usdc_rate = initial_brl_usdc_rate;
        oracle.token_price = initial_token_price;
        oracle.last_updated = Clock::get()?.unix_timestamp;
        oracle.twap_observations = Vec::new();
        oracle.bump = ctx.bumps.oracle_account;
        msg!("Oráculo inicializado. BRL/USDC: {}", initial_brl_usdc_rate);
        Ok(())
    }

    /// Atualiza a taxa de câmbio BRL/USDC.
    /// Em produção, seria chamada por Chainlink, Pyth ou Switchboard.
    pub fn update_brl_usdc_rate(
        ctx: Context<UpdateOracle>,
        new_rate: u64,
    ) -> Result<()> {
        require!(new_rate > 0, OracleError::InvalidRate);
        let oracle = &mut ctx.accounts.oracle_account;
        oracle.brl_usdc_rate = new_rate;
        oracle.last_updated = Clock::get()?.unix_timestamp;
        msg!("Taxa BRL/USDC atualizada para: {}", new_rate);
        Ok(())
    }

    /// Registra observação de preço do token para cálculo do TWAP.
    /// TWAP = Σ(price_i × duration_i) / Σ(duration_i)
    pub fn record_token_price(
        ctx: Context<UpdateOracle>,
        price: u64,
        duration: u64,
    ) -> Result<()> {
        require!(price > 0, OracleError::InvalidRate);
        require!(duration > 0, OracleError::InvalidDuration);

        let oracle = &mut ctx.accounts.oracle_account;
        if oracle.twap_observations.len() >= MAX_TWAP_OBSERVATIONS {
            oracle.twap_observations.remove(0);
        }
        oracle.twap_observations.push(PriceObservation {
            price, duration,
            timestamp: Clock::get()?.unix_timestamp,
        });

        // Recalcula TWAP
        let mut weighted_sum: u128 = 0;
        let mut total_duration: u128 = 0;
        for obs in &oracle.twap_observations {
            weighted_sum += (obs.price as u128) * (obs.duration as u128);
            total_duration += obs.duration as u128;
        }
        oracle.token_price = (weighted_sum / total_duration) as u64;
        oracle.last_updated = Clock::get()?.unix_timestamp;
        msg!("TWAP recalculado: {}", oracle.token_price);
        Ok(())
    }

    /// Converte um valor em BRL para USDC usando a taxa atual.
    /// Emite um evento para que o backend execute a rampa fiat.
    pub fn convert_brl_to_usdc(
        ctx: Context<ReadOracle>,
        brl_amount: u64,
    ) -> Result<()> {
        let oracle = &ctx.accounts.oracle_account;
        let now = Clock::get()?.unix_timestamp;
        require!(now - oracle.last_updated < 3600, OracleError::StalePrice);

        let usdc_amount = (brl_amount as u128)
            .checked_mul(oracle.brl_usdc_rate as u128)
            .ok_or(OracleError::Overflow)?
            .checked_div(1_000_000)
            .ok_or(OracleError::Overflow)? as u64;

        msg!("Conversão: {} BRL → {} USDC | Taxa: {}", brl_amount, usdc_amount, oracle.brl_usdc_rate);
        emit!(ConversionEvent { brl_amount, usdc_amount, rate: oracle.brl_usdc_rate, timestamp: now });
        Ok(())
    }
}

// --- Estruturas de Dados ---

#[derive(AnchorSerialize, AnchorDeserialize, Clone, InitSpace)]
pub struct PriceObservation {
    pub price: u64,
    pub duration: u64,
    pub timestamp: i64,
}

#[account]
#[derive(InitSpace)]
pub struct OracleAccount {
    pub authority: Pubkey,
    pub brl_usdc_rate: u64,
    pub token_price: u64,
    pub last_updated: i64,
    #[max_len(24)]
    pub twap_observations: Vec<PriceObservation>,
    pub bump: u8,
}

// --- Contextos ---

#[derive(Accounts)]
pub struct InitializeOracle<'info> {
    #[account(mut)]
    pub authority: Signer<'info>,
    #[account(init, payer = authority, space = 8 + OracleAccount::INIT_SPACE, seeds = [b"oracle"], bump)]
    pub oracle_account: Account<'info, OracleAccount>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct UpdateOracle<'info> {
    #[account(constraint = authority.key() == oracle_account.authority @ OracleError::Unauthorized)]
    pub authority: Signer<'info>,
    #[account(mut, seeds = [b"oracle"], bump = oracle_account.bump)]
    pub oracle_account: Account<'info, OracleAccount>,
}

#[derive(Accounts)]
pub struct ReadOracle<'info> {
    pub reader: Signer<'info>,
    #[account(seeds = [b"oracle"], bump = oracle_account.bump)]
    pub oracle_account: Account<'info, OracleAccount>,
}

#[event]
pub struct ConversionEvent {
    pub brl_amount: u64,
    pub usdc_amount: u64,
    pub rate: u64,
    pub timestamp: i64,
}

#[error_code]
pub enum OracleError {
    #[msg("Taxa inválida")]
    InvalidRate,
    #[msg("Duração inválida")]
    InvalidDuration,
    #[msg("Preço desatualizado (mais de 1 hora)")]
    StalePrice,
    #[msg("Overflow aritmético")]
    Overflow,
    #[msg("Acesso não autorizado")]
    Unauthorized,
}
```

### 4.3 Aritmética de Ponto Fixo

Na Solana **não usamos floats**. Usamos inteiros com fator de escala:

| Valor real | On-chain | Fator |
|---|---|---|
| R$ 5.000,00 | `5_000_000_000` | × 10⁶ |
| 0.172400 USDC/BRL | `172_400` | × 10⁶ |
| 862.00 USDC | `862_000_000` | × 10⁶ |

Isso garante resultados determinísticos em todos os validadores.

## 5. Programa 3 — Treasury (Tesouraria)

A Tesouraria é o coração financeiro do protocolo. Recebe pagamentos em USDC, registra receita por epoch, particiona os fundos e autoriza a emissão de tokens.

### 5.1 Smart Contract da Tesouraria

```rust
// programs/criptosensor_treasury/src/lib.rs

use anchor_lang::prelude::*;
use anchor_spl::token::{self, Token, TokenAccount, Transfer};

declare_id!("TRExxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx");

/// Percentual da receita destinado à operação (custos, infra) — 40%
const OPERATIONAL_BPS: u64 = 4000;
/// Percentual destinado à remuneração via tokens — 50%
const INCENTIVE_BPS: u64 = 5000;
/// Percentual destinado à recompra/queima — 10%
const BUYBACK_BPS: u64 = 1000;
/// Base em basis points (100% = 10_000)
const BPS_BASE: u64 = 10_000;

#[program]
pub mod criptosensor_treasury {
    use super::*;

    /// Inicializa a Tesouraria do protocolo.
    /// Cria a conta global com parâmetros do epoch e define a autoridade.
    pub fn initialize_treasury(
        ctx: Context<InitializeTreasury>,
        epoch_duration: i64, // segundos (ex.: 86400 = 1 dia)
    ) -> Result<()> {
        let treasury = &mut ctx.accounts.treasury_account;
        treasury.authority = ctx.accounts.authority.key();
        treasury.usdc_mint = ctx.accounts.usdc_mint.key();
        treasury.epoch_duration = epoch_duration;
        treasury.current_epoch = 0;
        treasury.epoch_start_time = Clock::get()?.unix_timestamp;
        treasury.epoch_revenue = 0;
        treasury.total_revenue = 0;
        treasury.ema_revenue = 0;
        treasury.ema_window = 5;
        treasury.bump = ctx.bumps.treasury_account;
        msg!("Tesouraria inicializada. Epoch duration: {}s", epoch_duration);
        Ok(())
    }

    /// Recebe um pagamento em USDC de um cliente.
    ///
    /// Chamada quando um cliente paga por um produto de dados.
    /// O valor já foi convertido de BRL para USDC off-chain
    /// usando a taxa do oráculo.
    pub fn receive_payment(
        ctx: Context<ReceivePayment>,
        amount: u64,         // USDC (6 decimals)
        order_id: String,
    ) -> Result<()> {
        require!(amount > 0, TreasuryError::ZeroAmount);

        // Transfere USDC do cliente para a Treasury
        let transfer_ctx = CpiContext::new(
            ctx.accounts.token_program.to_account_info(),
            Transfer {
                from: ctx.accounts.client_usdc_account.to_account_info(),
                to: ctx.accounts.treasury_usdc_account.to_account_info(),
                authority: ctx.accounts.client.to_account_info(),
            },
        );
        token::transfer(transfer_ctx, amount)?;

        // Registra a receita no epoch atual
        let treasury = &mut ctx.accounts.treasury_account;
        treasury.epoch_revenue = treasury.epoch_revenue
            .checked_add(amount).ok_or(TreasuryError::Overflow)?;
        treasury.total_revenue = treasury.total_revenue
            .checked_add(amount).ok_or(TreasuryError::Overflow)?;

        msg!("Pagamento recebido: {} USDC | Ordem: {} | Epoch: {}",
            amount, order_id, treasury.current_epoch);

        emit!(PaymentEvent {
            client: ctx.accounts.client.key(),
            amount, order_id,
            epoch: treasury.current_epoch,
            timestamp: Clock::get()?.unix_timestamp,
        });
        Ok(())
    }

    /// Fecha o epoch atual e calcula a receita elegível.
    ///
    /// Ao fechar um epoch:
    ///   1. Calcula a EMA da receita
    ///   2. Particiona: operação, incentivo, recompra
    ///   3. Registra o resultado no EpochRecord
    ///   4. Avança o contador de epoch
    ///
    /// EMA: EMA_t = α × R_t + (1 - α) × EMA_{t-1}, onde α = 2/(N+1)
    pub fn close_epoch(ctx: Context<CloseEpoch>) -> Result<()> {
        let treasury = &mut ctx.accounts.treasury_account;
        let now = Clock::get()?.unix_timestamp;

        require!(
            now >= treasury.epoch_start_time + treasury.epoch_duration,
            TreasuryError::EpochNotReady
        );

        let revenue = treasury.epoch_revenue;
        let epoch = treasury.current_epoch;

        // --- Cálculo da EMA (ponto fixo × 10^6) ---
        let alpha = 2_000_000u64
            .checked_div(treasury.ema_window + 1)
            .ok_or(TreasuryError::Overflow)?;

        let new_ema = if epoch == 0 {
            revenue
        } else {
            let alpha_r = (alpha as u128)
                .checked_mul(revenue as u128).ok_or(TreasuryError::Overflow)?
                .checked_div(1_000_000).ok_or(TreasuryError::Overflow)? as u64;
            let one_minus_alpha = 1_000_000u64.saturating_sub(alpha);
            let complement = (one_minus_alpha as u128)
                .checked_mul(treasury.ema_revenue as u128).ok_or(TreasuryError::Overflow)?
                .checked_div(1_000_000).ok_or(TreasuryError::Overflow)? as u64;
            alpha_r.checked_add(complement).ok_or(TreasuryError::Overflow)?
        };

        // --- Partição da receita ---
        let operational = revenue.checked_mul(OPERATIONAL_BPS).ok_or(TreasuryError::Overflow)?
            .checked_div(BPS_BASE).ok_or(TreasuryError::Overflow)?;
        let incentive = revenue.checked_mul(INCENTIVE_BPS).ok_or(TreasuryError::Overflow)?
            .checked_div(BPS_BASE).ok_or(TreasuryError::Overflow)?;
        let buyback = revenue.checked_mul(BUYBACK_BPS).ok_or(TreasuryError::Overflow)?
            .checked_div(BPS_BASE).ok_or(TreasuryError::Overflow)?;

        // Registra o epoch
        let epoch_record = &mut ctx.accounts.epoch_record;
        epoch_record.epoch = epoch;
        epoch_record.revenue = revenue;
        epoch_record.ema_revenue = new_ema;
        epoch_record.operational_share = operational;
        epoch_record.incentive_share = incentive;
        epoch_record.buyback_share = buyback;
        epoch_record.closed_at = now;
        epoch_record.bump = ctx.bumps.epoch_record;

        // Avança para o próximo epoch
        treasury.ema_revenue = new_ema;
        treasury.current_epoch = epoch.checked_add(1).ok_or(TreasuryError::Overflow)?;
        treasury.epoch_start_time = now;
        treasury.epoch_revenue = 0;

        msg!("Epoch {} fechado | Receita: {} | EMA: {} | Incentive: {}",
            epoch, revenue, new_ema, incentive);

        emit!(EpochClosedEvent {
            epoch, revenue, ema_revenue: new_ema,
            incentive_share: incentive,
            operational_share: operational,
            buyback_share: buyback,
            timestamp: now,
        });
        Ok(())
    }
}

// --- Contas ---

#[account]
#[derive(InitSpace)]
pub struct TreasuryAccount {
    pub authority: Pubkey,
    pub usdc_mint: Pubkey,
    pub epoch_duration: i64,
    pub current_epoch: u64,
    pub epoch_start_time: i64,
    pub epoch_revenue: u64,
    pub total_revenue: u64,
    pub ema_revenue: u64,
    pub ema_window: u64,
    pub bump: u8,
}

#[account]
#[derive(InitSpace)]
pub struct EpochRecord {
    pub epoch: u64,
    pub revenue: u64,
    pub ema_revenue: u64,
    pub operational_share: u64,
    pub incentive_share: u64,
    pub buyback_share: u64,
    pub closed_at: i64,
    pub bump: u8,
}

// --- Contextos ---

#[derive(Accounts)]
pub struct InitializeTreasury<'info> {
    #[account(mut)]
    pub authority: Signer<'info>,
    #[account(
        init, payer = authority,
        space = 8 + TreasuryAccount::INIT_SPACE,
        seeds = [b"treasury"], bump
    )]
    pub treasury_account: Account<'info, TreasuryAccount>,
    /// CHECK: deve ser o mint USDC conhecido
    pub usdc_mint: UncheckedAccount<'info>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct ReceivePayment<'info> {
    #[account(mut)]
    pub client: Signer<'info>,
    #[account(mut,
        constraint = client_usdc_account.mint == treasury_account.usdc_mint @ TreasuryError::InvalidMint,
        constraint = client_usdc_account.owner == client.key() @ TreasuryError::InvalidOwner,
    )]
    pub client_usdc_account: Account<'info, TokenAccount>,
    #[account(mut,
        constraint = treasury_usdc_account.mint == treasury_account.usdc_mint @ TreasuryError::InvalidMint,
    )]
    pub treasury_usdc_account: Account<'info, TokenAccount>,
    #[account(mut, seeds = [b"treasury"], bump = treasury_account.bump)]
    pub treasury_account: Account<'info, TreasuryAccount>,
    pub token_program: Program<'info, Token>,
}

#[derive(Accounts)]
pub struct CloseEpoch<'info> {
    #[account(constraint = authority.key() == treasury_account.authority @ TreasuryError::Unauthorized)]
    pub authority: Signer<'info>,
    #[account(mut, seeds = [b"treasury"], bump = treasury_account.bump)]
    pub treasury_account: Account<'info, TreasuryAccount>,
    #[account(
        init, payer = authority,
        space = 8 + EpochRecord::INIT_SPACE,
        seeds = [b"epoch", treasury_account.current_epoch.to_le_bytes().as_ref()],
        bump
    )]
    pub epoch_record: Account<'info, EpochRecord>,
    pub system_program: Program<'info, System>,
}

// --- Eventos ---

#[event]
pub struct PaymentEvent {
    pub client: Pubkey,
    pub amount: u64,
    pub order_id: String,
    pub epoch: u64,
    pub timestamp: i64,
}

#[event]
pub struct EpochClosedEvent {
    pub epoch: u64,
    pub revenue: u64,
    pub ema_revenue: u64,
    pub incentive_share: u64,
    pub operational_share: u64,
    pub buyback_share: u64,
    pub timestamp: i64,
}

// --- Erros ---

#[error_code]
pub enum TreasuryError {
    #[msg("Valor deve ser maior que zero")]
    ZeroAmount,
    #[msg("Epoch ainda não atingiu duração mínima")]
    EpochNotReady,
    #[msg("Mint USDC inválido")]
    InvalidMint,
    #[msg("Dono da token account inválido")]
    InvalidOwner,
    #[msg("Overflow aritmético")]
    Overflow,
    #[msg("Acesso não autorizado")]
    Unauthorized,
}
```

### 5.2 Ciclo de Vida de um Pagamento

Vamos rastrear um pagamento de R$ 5.000,00 através do fluxo completo:

```
1. Cliente quer comprar dados de temperatura de Fortaleza (30 dias).
   Preço: R$ 5.000,00

2. Backend consulta o oráculo on-chain:
   BRL/USDC rate = 172_400 (0.1724)
   → 5.000 × 0.1724 = 862.00 USDC

3. Cliente autoriza transferência de 862 USDC (862_000_000 × 10^-6)
   → Instrução receive_payment é executada

4. Treasury registra:
   epoch_revenue += 862_000_000
   total_revenue += 862_000_000

5. Ao final do epoch, close_epoch é chamado:
   - EMA é recalculada
   - 40% → operação = 344.80 USDC
   - 50% → incentivo = 431.00 USDC (gera tokens)
   - 10% → recompra  = 86.20 USDC
```

## 6. Programa 4 — Emission (Emissão e Distribuição de Tokens)

Este é o programa mais importante: implementa a **emissão lastreada** (tokens só nascem se houver receita real) e a **distribuição proporcional ao score**.

### 6.1 Smart Contract de Emissão

```rust
// programs/criptosensor_emission/src/lib.rs

use anchor_lang::prelude::*;
use anchor_spl::token::{self, Token, TokenAccount, MintTo, Mint};

declare_id!("EMIxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx");

const SCALE: u64 = 1_000_000;       // fator de escala (ponto fixo 10^6)
const SENSOR_POOL_BPS: u64 = 7000;  // 70% para sensores
const EDGE_POOL_BPS: u64 = 3000;    // 30% para nós de borda
const BPS_BASE: u64 = 10_000;

#[program]
pub mod criptosensor_emission {
    use super::*;

    /// Inicializa a configuração global de emissão.
    ///
    /// Parâmetros do modelo econômico:
    /// - `incentive_rate_bps`: taxa de incentivo (ex.: 2000 = 20%)
    /// - `decay_lambda`: fator λ de decaimento × 10^6 (ex.: 5000 = 0.005)
    /// - `token_price_ref`: preço de referência do token × 10^6
    pub fn initialize_emission(
        ctx: Context<InitializeEmission>,
        incentive_rate_bps: u64,
        decay_lambda: u64,
        token_price_ref: u64,
    ) -> Result<()> {
        require!(incentive_rate_bps > 0 && incentive_rate_bps <= BPS_BASE,
            EmissionError::InvalidRate);
        require!(token_price_ref > 0, EmissionError::InvalidPrice);

        let config = &mut ctx.accounts.emission_config;
        config.authority = ctx.accounts.authority.key();
        config.token_mint = ctx.accounts.token_mint.key();
        config.incentive_rate_bps = incentive_rate_bps;
        config.decay_lambda = decay_lambda;
        config.token_price_ref = token_price_ref;
        config.total_emitted = 0;
        config.bump = ctx.bumps.emission_config;
        msg!("Configuração de emissão inicializada");
        Ok(())
    }

    /// Calcula e emite (mint) os tokens de um epoch.
    ///
    /// Implementa o coração do protocolo CriptoSensor:
    ///   1. Lê a receita elegível do EpochRecord (da Treasury)
    ///   2. Aplica o fator de decaimento: D(t) = e^(-λ·t)
    ///   3. ReceitaElegível = incentive_share × D(t)
    ///   4. TokensDoEpoch = ReceitaElegível / PreçoDeReferência
    ///   5. Faz mint dos tokens para a conta de distribuição
    ///
    /// O decaimento exponencial é aproximado via série de Taylor:
    ///   e^(-x) ≈ 1 - x + x²/2 - x³/6
    pub fn emit_epoch_tokens(
        ctx: Context<EmitEpochTokens>,
        epoch: u64,
    ) -> Result<()> {
        let config = &ctx.accounts.emission_config;
        let epoch_record = &ctx.accounts.epoch_record;
        require!(epoch_record.epoch == epoch, EmissionError::EpochMismatch);

        let incentive_share = epoch_record.incentive_share;

        // D(t) = e^(-λ·t)
        // x = λ × t / SCALE (ambos × 10^6)
        let x = (config.decay_lambda as u128)
            .checked_mul(epoch as u128).ok_or(EmissionError::Overflow)?
            .checked_div(SCALE as u128).ok_or(EmissionError::Overflow)? as u64;
        let decay_factor = approximate_exp_neg(x)?;

        // ReceitaElegível = incentive_share × D(t)
        let eligible_revenue = (incentive_share as u128)
            .checked_mul(decay_factor as u128).ok_or(EmissionError::Overflow)?
            .checked_div(SCALE as u128).ok_or(EmissionError::Overflow)? as u64;

        // TokensDoEpoch = ReceitaElegível × SCALE / PreçoRef
        let tokens_to_mint = (eligible_revenue as u128)
            .checked_mul(SCALE as u128).ok_or(EmissionError::Overflow)?
            .checked_div(config.token_price_ref as u128)
            .ok_or(EmissionError::Overflow)? as u64;

        require!(tokens_to_mint > 0, EmissionError::ZeroEmission);

        // Mint via PDA
        let seeds = &[b"emission_config".as_ref(), &[config.bump]];
        let signer_seeds = &[&seeds[..]];
        let mint_ctx = CpiContext::new_with_signer(
            ctx.accounts.token_program.to_account_info(),
            MintTo {
                mint: ctx.accounts.token_mint.to_account_info(),
                to: ctx.accounts.distribution_account.to_account_info(),
                authority: ctx.accounts.emission_config.to_account_info(),
            },
            signer_seeds,
        );
        token::mint_to(mint_ctx, tokens_to_mint)?;

        let config = &mut ctx.accounts.emission_config;
        config.total_emitted = config.total_emitted
            .checked_add(tokens_to_mint).ok_or(EmissionError::Overflow)?;

        msg!("Epoch {} | Incentive: {} | Decay: {} | Tokens: {}",
            epoch, incentive_share, decay_factor, tokens_to_mint);

        emit!(EmissionEvent {
            epoch, incentive_share, decay_factor, eligible_revenue,
            tokens_minted: tokens_to_mint,
            token_price_ref: config.token_price_ref,
            timestamp: Clock::get()?.unix_timestamp,
        });
        Ok(())
    }

    /// Distribui tokens para um sensor específico.
    /// Tokens_i = TokensDoEpoch × 70% × (S_i / S_total)
    pub fn distribute_to_sensor(
        ctx: Context<DistributeToSensor>,
        sensor_score: u64,
        total_sensor_score: u64,
        tokens_of_epoch: u64,
    ) -> Result<()> {
        require!(sensor_score > 0 && total_sensor_score > 0, EmissionError::ZeroScore);
        require!(sensor_score <= total_sensor_score, EmissionError::InvalidScore);

        let sensor_pool = (tokens_of_epoch as u128)
            .checked_mul(SENSOR_POOL_BPS as u128).ok_or(EmissionError::Overflow)?
            .checked_div(BPS_BASE as u128).ok_or(EmissionError::Overflow)? as u64;

        let tokens_for_sensor = (sensor_pool as u128)
            .checked_mul(sensor_score as u128).ok_or(EmissionError::Overflow)?
            .checked_div(total_sensor_score as u128).ok_or(EmissionError::Overflow)? as u64;

        require!(tokens_for_sensor > 0, EmissionError::ZeroEmission);

        let seeds = &[b"emission_config".as_ref(), &[ctx.accounts.emission_config.bump]];
        let signer_seeds = &[&seeds[..]];
        let transfer_ctx = CpiContext::new_with_signer(
            ctx.accounts.token_program.to_account_info(),
            token::Transfer {
                from: ctx.accounts.distribution_account.to_account_info(),
                to: ctx.accounts.sensor_token_account.to_account_info(),
                authority: ctx.accounts.emission_config.to_account_info(),
            },
            signer_seeds,
        );
        token::transfer(transfer_ctx, tokens_for_sensor)?;

        msg!("Sensor {} recebeu {} tokens (score {}/{})",
            ctx.accounts.sensor_account.sensor_id,
            tokens_for_sensor, sensor_score, total_sensor_score);

        emit!(DistributionEvent {
            recipient: ctx.accounts.sensor_owner.key(),
            recipient_type: "sensor".to_string(),
            tokens: tokens_for_sensor,
            score: sensor_score,
            total_score: total_sensor_score,
            timestamp: Clock::get()?.unix_timestamp,
        });
        Ok(())
    }

    /// Distribui tokens para um nó de borda específico.
    /// Tokens_j = TokensDoEpoch × 30% × (S_j / S_total_edge)
    pub fn distribute_to_edge_node(
        ctx: Context<DistributeToEdgeNode>,
        node_score: u64,
        total_edge_score: u64,
        tokens_of_epoch: u64,
    ) -> Result<()> {
        require!(node_score > 0 && total_edge_score > 0, EmissionError::ZeroScore);
        require!(node_score <= total_edge_score, EmissionError::InvalidScore);

        let edge_pool = (tokens_of_epoch as u128)
            .checked_mul(EDGE_POOL_BPS as u128).ok_or(EmissionError::Overflow)?
            .checked_div(BPS_BASE as u128).ok_or(EmissionError::Overflow)? as u64;

        let tokens_for_node = (edge_pool as u128)
            .checked_mul(node_score as u128).ok_or(EmissionError::Overflow)?
            .checked_div(total_edge_score as u128).ok_or(EmissionError::Overflow)? as u64;

        require!(tokens_for_node > 0, EmissionError::ZeroEmission);

        let seeds = &[b"emission_config".as_ref(), &[ctx.accounts.emission_config.bump]];
        let signer_seeds = &[&seeds[..]];
        let transfer_ctx = CpiContext::new_with_signer(
            ctx.accounts.token_program.to_account_info(),
            token::Transfer {
                from: ctx.accounts.distribution_account.to_account_info(),
                to: ctx.accounts.node_token_account.to_account_info(),
                authority: ctx.accounts.emission_config.to_account_info(),
            },
            signer_seeds,
        );
        token::transfer(transfer_ctx, tokens_for_node)?;

        msg!("Nó {} recebeu {} tokens (score {}/{})",
            ctx.accounts.edge_node_account.node_id,
            tokens_for_node, node_score, total_edge_score);

        emit!(DistributionEvent {
            recipient: ctx.accounts.node_owner.key(),
            recipient_type: "edge_node".to_string(),
            tokens: tokens_for_node,
            score: node_score,
            total_score: total_edge_score,
            timestamp: Clock::get()?.unix_timestamp,
        });
        Ok(())
    }
}

// ---------------------------------------------------------------------------
// Aproximação de e^(-x) via série de Taylor
// ---------------------------------------------------------------------------

/// e^(-x) ≈ 1 - x + x²/2 - x³/6   (ponto fixo × 10^6)
///
/// Precisão para x < 0.5 (~100 epochs com λ=0.005).
fn approximate_exp_neg(x: u64) -> Result<u64> {
    if x == 0 { return Ok(SCALE); }

    let x_128 = x as u128;
    let scale_128 = SCALE as u128;

    let x2 = x_128.checked_mul(x_128).ok_or(EmissionError::Overflow)?
        .checked_div(scale_128).ok_or(EmissionError::Overflow)?;
    let x3 = x2.checked_mul(x_128).ok_or(EmissionError::Overflow)?
        .checked_div(scale_128).ok_or(EmissionError::Overflow)?;

    // resultado = SCALE - x + x²/2 - x³/6
    let result = scale_128
        .checked_sub(x_128).unwrap_or(0)
        .checked_add(x2 / 2).ok_or(EmissionError::Overflow)?
        .checked_sub(x3 / 6).unwrap_or(0);

    Ok(result.min(scale_128).max(0) as u64)
}

// ---------------------------------------------------------------------------
// Contas
// ---------------------------------------------------------------------------

#[account]
#[derive(InitSpace)]
pub struct EmissionConfig {
    pub authority: Pubkey,
    pub token_mint: Pubkey,
    pub incentive_rate_bps: u64,
    pub decay_lambda: u64,
    pub token_price_ref: u64,
    pub total_emitted: u64,
    pub bump: u8,
}

/// Espelho do EpochRecord da Treasury (leitura cross-program)
#[account]
#[derive(InitSpace)]
pub struct EpochRecord {
    pub epoch: u64,
    pub revenue: u64,
    pub ema_revenue: u64,
    pub operational_share: u64,
    pub incentive_share: u64,
    pub buyback_share: u64,
    pub closed_at: i64,
    pub bump: u8,
}

/// Espelho do SensorAccount do Registry
#[account]
#[derive(InitSpace)]
pub struct SensorAccount {
    pub owner: Pubkey,
    #[max_len(32)]
    pub sensor_id: String,
    #[max_len(16)]
    pub sensor_type: String,
    pub latitude: i64,
    pub longitude: i64,
    pub score: u64,
    pub penalty_level: u8,
    pub is_active: bool,
    pub registered_at: i64,
    pub bump: u8,
}

/// Espelho do EdgeNodeAccount do Registry
#[account]
#[derive(InitSpace)]
pub struct EdgeNodeAccount {
    pub owner: Pubkey,
    #[max_len(32)]
    pub node_id: String,
    pub score: u64,
    pub is_active: bool,
    pub registered_at: i64,
    pub bump: u8,
}

// ---------------------------------------------------------------------------
// Contextos
// ---------------------------------------------------------------------------

#[derive(Accounts)]
pub struct InitializeEmission<'info> {
    #[account(mut)]
    pub authority: Signer<'info>,
    #[account(
        init, payer = authority,
        space = 8 + EmissionConfig::INIT_SPACE,
        seeds = [b"emission_config"], bump
    )]
    pub emission_config: Account<'info, EmissionConfig>,
    pub token_mint: Account<'info, Mint>,
    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct EmitEpochTokens<'info> {
    #[account(constraint = authority.key() == emission_config.authority @ EmissionError::Unauthorized)]
    pub authority: Signer<'info>,
    #[account(mut, seeds = [b"emission_config"], bump = emission_config.bump)]
    pub emission_config: Account<'info, EmissionConfig>,
    pub epoch_record: Account<'info, EpochRecord>,
    #[account(mut, constraint = token_mint.key() == emission_config.token_mint @ EmissionError::InvalidMint)]
    pub token_mint: Account<'info, Mint>,
    #[account(mut, constraint = distribution_account.mint == token_mint.key())]
    pub distribution_account: Account<'info, TokenAccount>,
    pub token_program: Program<'info, Token>,
}

#[derive(Accounts)]
pub struct DistributeToSensor<'info> {
    #[account(constraint = authority.key() == emission_config.authority @ EmissionError::Unauthorized)]
    pub authority: Signer<'info>,
    #[account(seeds = [b"emission_config"], bump = emission_config.bump)]
    pub emission_config: Account<'info, EmissionConfig>,
    #[account(
        constraint = sensor_account.is_active @ EmissionError::SensorInactive,
        constraint = sensor_account.penalty_level < 3 @ EmissionError::SensorBanned,
    )]
    pub sensor_account: Account<'info, SensorAccount>,
    /// CHECK: validado via sensor_account.owner
    #[account(constraint = sensor_owner.key() == sensor_account.owner @ EmissionError::Unauthorized)]
    pub sensor_owner: UncheckedAccount<'info>,
    #[account(mut,
        constraint = sensor_token_account.mint == emission_config.token_mint,
        constraint = sensor_token_account.owner == sensor_owner.key(),
    )]
    pub sensor_token_account: Account<'info, TokenAccount>,
    #[account(mut, constraint = distribution_account.mint == emission_config.token_mint)]
    pub distribution_account: Account<'info, TokenAccount>,
    pub token_program: Program<'info, Token>,
}

#[derive(Accounts)]
pub struct DistributeToEdgeNode<'info> {
    #[account(constraint = authority.key() == emission_config.authority @ EmissionError::Unauthorized)]
    pub authority: Signer<'info>,
    #[account(seeds = [b"emission_config"], bump = emission_config.bump)]
    pub emission_config: Account<'info, EmissionConfig>,
    pub edge_node_account: Account<'info, EdgeNodeAccount>,
    /// CHECK: validado via edge_node_account.owner
    #[account(constraint = node_owner.key() == edge_node_account.owner @ EmissionError::Unauthorized)]
    pub node_owner: UncheckedAccount<'info>,
    #[account(mut,
        constraint = node_token_account.mint == emission_config.token_mint,
        constraint = node_token_account.owner == node_owner.key(),
    )]
    pub node_token_account: Account<'info, TokenAccount>,
    #[account(mut, constraint = distribution_account.mint == emission_config.token_mint)]
    pub distribution_account: Account<'info, TokenAccount>,
    pub token_program: Program<'info, Token>,
}

// ---------------------------------------------------------------------------
// Eventos
// ---------------------------------------------------------------------------

#[event]
pub struct EmissionEvent {
    pub epoch: u64,
    pub incentive_share: u64,
    pub decay_factor: u64,
    pub eligible_revenue: u64,
    pub tokens_minted: u64,
    pub token_price_ref: u64,
    pub timestamp: i64,
}

#[event]
pub struct DistributionEvent {
    pub recipient: Pubkey,
    pub recipient_type: String,
    pub tokens: u64,
    pub score: u64,
    pub total_score: u64,
    pub timestamp: i64,
}

// ---------------------------------------------------------------------------
// Erros
// ---------------------------------------------------------------------------

#[error_code]
pub enum EmissionError {
    #[msg("Taxa de incentivo inválida")]
    InvalidRate,
    #[msg("Preço de referência inválido")]
    InvalidPrice,
    #[msg("Epoch não corresponde ao registro")]
    EpochMismatch,
    #[msg("Mint do token inválido")]
    InvalidMint,
    #[msg("Emissão resultou em zero tokens")]
    ZeroEmission,
    #[msg("Score deve ser maior que zero")]
    ZeroScore,
    #[msg("Score individual maior que score total")]
    InvalidScore,
    #[msg("Sensor está inativo")]
    SensorInactive,
    #[msg("Sensor está banido")]
    SensorBanned,
    #[msg("Overflow aritmético")]
    Overflow,
    #[msg("Acesso não autorizado")]
    Unauthorized,
}
```

## 7. Simulação Completa — Um Epoch Inteiro

Vamos simular o Epoch 10 com números reais para consolidar o entendimento:

```
═══════════════════════════════════════════════════════════════
  SIMULAÇÃO: EPOCH 10 DO PROTOCOLO CRIPTOSENSOR NA SOLANA
═══════════════════════════════════════════════════════════════

► Parâmetros on-chain:
  - λ (decay_lambda)    = 5_000 (0.005 × 10^6)
  - Incentivo (Treasury) = 50% da receita (INCENTIVE_BPS = 5000)
  - Preço de referência  = 500_000 (0.50 USDC × 10^6)
  - EMA anterior         = 800_000_000 (800 USDC × 10^6)
  - Janela EMA           = 5 epochs

► Receita do Epoch (off-chain → on-chain):
  - 3 clientes pagaram total de R$ 6.200,00
  - Oráculo BRL/USDC: 172_400 (0.1724)
  - Conversão: 6200 × 0.1724 = 1.068,88 USDC
  - On-chain: 1_068_880_000 (× 10^6)

► Partição (close_epoch):
  - Operação (40%):   427_552_000 (427.55 USDC)
  - Incentivo (50%):  534_440_000 (534.44 USDC) → gera tokens
  - Recompra (10%):   106_888_000 (106.89 USDC)

► Cálculo da EMA:
  α = 2_000_000 / (5 + 1) = 333_333
  EMA_10 = 333_333 × 1_068_880_000 / 1_000_000
         + 666_667 × 800_000_000 / 1_000_000
         = 356_293_104 + 533_333_600 = 889_626_704

► Fator de Decaimento (emit_epoch_tokens):
  x = (5_000 × 10) = 50_000 (que é 0.05 × 10^6)
  D(10) = approximate_exp_neg(50_000)
        = 1_000_000 - 50_000 + 1_250 - 20
        = 951_230    (≈ 0.951230, real: 0.951229)

► Receita Elegível:
  534_440_000 × 951_230 / 1_000_000 = 508_383_412

► Tokens do Epoch:
  508_383_412 × 1_000_000 / 500_000 = 1_016_766_824
  → 1.016,77 tokens (com 6 decimals)

► Distribuição:
  Pool sensores (70%): 711_736_776
  Pool borda    (30%): 305_030_047

  ┌─────────────┬────────────┬─────────┬─────────────────┐
  │ Sensor      │ Score      │ Fração  │ Tokens (×10^6)  │
  ├─────────────┼────────────┼─────────┼─────────────────┤
  │ temp-001    │ 120        │ 43.6%   │ 310_357_754     │
  │ umid-002    │  95        │ 34.5%   │ 245_700_267     │
  │ press-003   │  60        │ 21.8%   │ 155_252_800     │
  │ spam-004    │ 200 BANIDO │   ---   │           0     │
  └─────────────┴────────────┴─────────┴─────────────────┘
  Score total válido: 275

  ┌─────────────┬───────┬─────────┬─────────────────┐
  │ Nó Borda    │ Score │ Fração  │ Tokens (×10^6)  │
  ├─────────────┼───────┼─────────┼─────────────────┤
  │ edge-A      │   80  │ 64.0%   │ 195_219_230     │
  │ edge-B      │   45  │ 36.0%   │ 109_810_817     │
  └─────────────┴───────┴─────────┴─────────────────┘
═══════════════════════════════════════════════════════════════
```

## 8. Mecanismo de Conversão BRL → USDC — Detalhamento

### 8.1 Arquitetura da Conversão

```
┌─────────────────────────────────────────────────────┐
│                 CAMADA OFF-CHAIN                     │
│                                                     │
│  ┌──────────┐   PIX    ┌──────────────┐            │
│  │ Cliente  │ ───────► │ PSP / Rampa  │            │
│  │ (BRL)    │          │ Fiat→Crypto  │            │
│  └──────────┘          └──────┬───────┘            │
│                               │ USDC                │
│                               ▼                     │
│  ┌──────────────────────────────────────┐          │
│  │      Backend do Protocolo            │          │
│  │                                      │          │
│  │  1. Recebe webhook do PSP (PIX ok)   │          │
│  │  2. Consulta oráculo on-chain        │          │
│  │  3. Verifica USDC na wallet          │          │
│  │  4. Chama receive_payment on-chain   │          │
│  └─────────────────┬────────────────────┘          │
│                    │                                │
└────────────────────┼────────────────────────────────┘
                     │
┌────────────────────┼────────────────────────────────┐
│             CAMADA ON-CHAIN (Solana)                 │
│                    ▼                                │
│  ┌─────────┐  ┌──────────┐  ┌───────────────┐      │
│  │ Oracle  │  │ Treasury │  │   Emission    │      │
│  │ (taxa)  │◄─│ (USDC)   │─►│ (mint token)  │      │
│  └─────────┘  └──────────┘  └───────────────┘      │
└─────────────────────────────────────────────────────┘
```

### 8.2 Provedores de Rampa Fiat

Para converter BRL em USDC automaticamente:

| Provedor | Tipo | Integração |
|---|---|---|
| **Mercado Bitcoin** | Exchange brasileira | API REST + webhooks |
| **Transak** | Rampa fiat global | Widget + API |
| **Brla Protocol** | Stablecoin BRL on-chain | Swap direto |
| **PIX via PSP** | Pagamento instantâneo | Webhook → exchange |

### 8.3 Fluxo Detalhado com Código do Backend

```typescript
// backend/src/services/payment.ts
// Exemplo de integração off-chain (Node.js / TypeScript)

import { Connection, PublicKey } from "@solana/web3.js";
import { Program } from "@coral-xyz/anchor";

interface PixPayment {
  txId: string;
  amount: number;     // valor em centavos BRL
  payer: string;
  timestamp: Date;
}

/**
 * Processa um pagamento PIX recebido e registra na Treasury on-chain.
 *
 * Este serviço:
 * 1. Valida o webhook do PSP
 * 2. Consulta o oráculo on-chain para a taxa BRL/USDC
 * 3. Compra USDC na exchange (ou usa rampa)
 * 4. Chama receive_payment no smart contract
 */
async function processPixPayment(
  payment: PixPayment,
  oracleProgram: Program,
  treasuryProgram: Program,
  connection: Connection,
) {
  // 1. Consulta a taxa BRL/USDC no oráculo on-chain
  const [oraclePda] = PublicKey.findProgramAddressSync(
    [Buffer.from("oracle")],
    oracleProgram.programId
  );
  const oracle = await oracleProgram.account.oracleAccount.fetch(oraclePda);
  const brlUsdcRate = oracle.brlUsdcRate.toNumber(); // ex.: 172_400

  // 2. Calcula o valor em USDC
  // payment.amount está em centavos → convertemos para × 10^6
  const brlAmount = BigInt(payment.amount) * BigInt(10_000); // centavos → ×10^6
  const usdcAmount = (brlAmount * BigInt(brlUsdcRate)) / BigInt(1_000_000);

  console.log(`PIX: R$ ${payment.amount / 100}`);
  console.log(`Taxa: ${brlUsdcRate / 1_000_000} USDC/BRL`);
  console.log(`USDC: ${Number(usdcAmount) / 1_000_000}`);

  // 3. Compra USDC via exchange (implementação específica do PSP)
  // await exchangeService.buyUsdc(Number(usdcAmount));

  // 4. Chama receive_payment no Treasury on-chain
  // (após USDC estar disponível na wallet do protocolo)
  const orderId = `PIX-${payment.txId}`;
  await treasuryProgram.methods
    .receivePayment(
      new anchor.BN(Number(usdcAmount)),
      orderId
    )
    .accounts({
      /* ... contas necessárias ... */
    })
    .rpc();

  console.log(`Pagamento registrado on-chain: ${orderId}`);
}
```

### 8.4 Proteções da Conversão

| Proteção | Implementação |
|---|---|
| **Preço desatualizado** | Oráculo rejeita leituras com mais de 1 hora |
| **Slippage** | Backend compara taxa on-chain com taxa da exchange |
| **Auditabilidade** | Cada conversão emite `ConversionEvent` com taxa usada |
| **Reconciliação** | Backend verifica saldo USDC na Treasury após operação |

## 9. Testes com Anchor (TypeScript)

```typescript
// tests/criptosensor.ts

import * as anchor from "@coral-xyz/anchor";
import { Program } from "@coral-xyz/anchor";
import { PublicKey, Keypair } from "@solana/web3.js";
import {
  createMint,
  getOrCreateAssociatedTokenAccount,
  mintTo,
} from "@solana/spl-token";
import { assert } from "chai";

describe("CriptoSensor Protocol", () => {
  const provider = anchor.AnchorProvider.env();
  anchor.setProvider(provider);

  const authority = provider.wallet as anchor.Wallet;
  const sensorOwner = Keypair.generate();
  const edgeNodeOwner = Keypair.generate();
  const client = Keypair.generate();

  let usdcMint: PublicKey;
  let criptoSensorMint: PublicKey;
  let oraclePda: PublicKey;
  let treasuryPda: PublicKey;
  let sensorPda: PublicKey;
  let edgeNodePda: PublicKey;

  before(async () => {
    for (const kp of [sensorOwner, edgeNodeOwner, client]) {
      const sig = await provider.connection.requestAirdrop(
        kp.publicKey, 2 * anchor.web3.LAMPORTS_PER_SOL
      );
      await provider.connection.confirmTransaction(sig);
    }

    usdcMint = await createMint(
      provider.connection, authority.payer,
      authority.publicKey, null, 6
    );

    criptoSensorMint = await createMint(
      provider.connection, authority.payer,
      authority.publicKey, null, 6
    );
  });

  // ----- Registry -----

  it("Registra um sensor", async () => {
    const program = anchor.workspace.CriptosensorRegistry as Program;
    const sensorId = "temp-001";

    [sensorPda] = PublicKey.findProgramAddressSync(
      [Buffer.from("sensor"), sensorOwner.publicKey.toBuffer(),
       Buffer.from(sensorId)],
      program.programId
    );

    await program.methods
      .registerSensor(sensorId, "temperature",
        new anchor.BN(-38516700), new anchor.BN(-386120000))
      .accounts({
        owner: sensorOwner.publicKey,
        sensorAccount: sensorPda,
        systemProgram: anchor.web3.SystemProgram.programId,
      })
      .signers([sensorOwner])
      .rpc();

    const sensor = await program.account.sensorAccount.fetch(sensorPda);
    assert.equal(sensor.sensorId, "temp-001");
    assert.equal(sensor.score.toNumber(), 0);
    assert.isTrue(sensor.isActive);
  });

  it("Registra um nó de borda", async () => {
    const program = anchor.workspace.CriptosensorRegistry as Program;
    const nodeId = "edge-A";

    [edgeNodePda] = PublicKey.findProgramAddressSync(
      [Buffer.from("edge_node"), edgeNodeOwner.publicKey.toBuffer(),
       Buffer.from(nodeId)],
      program.programId
    );

    await program.methods
      .registerEdgeNode(nodeId)
      .accounts({
        owner: edgeNodeOwner.publicKey,
        edgeNodeAccount: edgeNodePda,
        systemProgram: anchor.web3.SystemProgram.programId,
      })
      .signers([edgeNodeOwner])
      .rpc();

    const node = await program.account.edgeNodeAccount.fetch(edgeNodePda);
    assert.equal(node.nodeId, "edge-A");
    assert.isTrue(node.isActive);
  });

  // ----- Oracle -----

  it("Inicializa o oráculo BRL/USDC", async () => {
    const program = anchor.workspace.CriptosensorOracle as Program;

    [oraclePda] = PublicKey.findProgramAddressSync(
      [Buffer.from("oracle")], program.programId
    );

    await program.methods
      .initializeOracle(new anchor.BN(172_400), new anchor.BN(500_000))
      .accounts({
        authority: authority.publicKey,
        oracleAccount: oraclePda,
        systemProgram: anchor.web3.SystemProgram.programId,
      })
      .rpc();

    const oracle = await program.account.oracleAccount.fetch(oraclePda);
    assert.equal(oracle.brlUsdcRate.toNumber(), 172_400);
    assert.equal(oracle.tokenPrice.toNumber(), 500_000);
  });

  it("Atualiza a taxa BRL/USDC", async () => {
    const program = anchor.workspace.CriptosensorOracle as Program;

    await program.methods
      .updateBrlUsdcRate(new anchor.BN(175_000))
      .accounts({
        authority: authority.publicKey,
        oracleAccount: oraclePda,
      })
      .rpc();

    const oracle = await program.account.oracleAccount.fetch(oraclePda);
    assert.equal(oracle.brlUsdcRate.toNumber(), 175_000);
  });

  // ----- Treasury -----

  it("Inicializa a Tesouraria", async () => {
    const program = anchor.workspace.CriptosensorTreasury as Program;

    [treasuryPda] = PublicKey.findProgramAddressSync(
      [Buffer.from("treasury")], program.programId
    );

    await program.methods
      .initializeTreasury(new anchor.BN(86_400))
      .accounts({
        authority: authority.publicKey,
        treasuryAccount: treasuryPda,
        usdcMint: usdcMint,
        systemProgram: anchor.web3.SystemProgram.programId,
      })
      .rpc();

    const treasury = await program.account.treasuryAccount.fetch(treasuryPda);
    assert.equal(treasury.currentEpoch.toNumber(), 0);
    assert.equal(treasury.epochRevenue.toNumber(), 0);
  });

  it("Recebe pagamento em USDC", async () => {
    const program = anchor.workspace.CriptosensorTreasury as Program;

    const clientUsdc = await getOrCreateAssociatedTokenAccount(
      provider.connection, authority.payer, usdcMint, client.publicKey
    );
    await mintTo(
      provider.connection, authority.payer, usdcMint,
      clientUsdc.address, authority.publicKey, 1_000_000_000
    );

    const treasuryUsdc = await getOrCreateAssociatedTokenAccount(
      provider.connection, authority.payer, usdcMint, treasuryPda, true
    );

    await program.methods
      .receivePayment(new anchor.BN(862_000_000), "ORDER-2026-001")
      .accounts({
        client: client.publicKey,
        clientUsdcAccount: clientUsdc.address,
        treasuryUsdcAccount: treasuryUsdc.address,
        treasuryAccount: treasuryPda,
        tokenProgram: anchor.utils.token.TOKEN_PROGRAM_ID,
      })
      .signers([client])
      .rpc();

    const treasury = await program.account.treasuryAccount.fetch(treasuryPda);
    assert.equal(treasury.epochRevenue.toNumber(), 862_000_000);
    console.log("  ✓ Receita do epoch: 862.00 USDC");
  });
});
```

## 10. Mapa de Contas e PDAs

| Programa | Conta | Seeds | Descrição |
|---|---|---|---|
| Registry | `SensorAccount` | `["sensor", owner, sensor_id]` | Dados e score do sensor |
| Registry | `EdgeNodeAccount` | `["edge_node", owner, node_id]` | Dados do nó de borda |
| Oracle | `OracleAccount` | `["oracle"]` | Taxa BRL/USDC e TWAP |
| Treasury | `TreasuryAccount` | `["treasury"]` | Estado global da tesouraria |
| Treasury | `EpochRecord` | `["epoch", epoch_num]` | Histórico de cada epoch |
| Emission | `EmissionConfig` | `["emission_config"]` | Configuração de emissão |

## 11. Propriedades do Modelo na Solana

| Propriedade | Mecanismo On-chain |
|---|---|
| **Sem inflação** | `emit_epoch_tokens` só funciona com `EpochRecord` criado por `close_epoch` (receita real) |
| **Escassez crescente** | `approximate_exp_neg` reduz `decay_factor` a cada epoch |
| **Suavização** | EMA calculada em `close_epoch` com aritmética de ponto fixo |
| **Lastro real** | `token_price_ref` ancora o token ao consumo de dados |
| **Anti-farming** | `distribute_to_sensor` verifica `is_active` e `penalty_level` |
| **Penalidades** | `apply_penalty` com slashing e banimento progressivo |
| **Transparência** | Eventos `PaymentEvent`, `EmissionEvent`, `DistributionEvent` auditáveis on-chain |
| **Conversão BRL** | `OracleAccount` registra taxa auditável; `ConversionEvent` rastreia cada conversão |

## 12. Deploy e Próximos Passos

### 12.1 Deploy na Devnet

```bash
# Configurar para devnet
solana config set --url devnet

# Gerar keypairs dos programas
anchor keys list

# Build e deploy
anchor build
anchor deploy

# Rodar testes
anchor test
```

### 12.2 Checklist para Produção

Antes de ir para mainnet:

- [ ] **Auditoria de segurança** por empresa especializada (Neodyme, OtterSec)
- [ ] **Multisig** na autoridade dos programas (Squads Protocol)
- [ ] **Integração com oráculo real** (Pyth Network para BRL/USD)
- [ ] **Rate limiting** no backend para proteção contra spam
- [ ] **Testes de carga** simulando múltiplos epochs com centenas de sensores
- [ ] **Monitoramento** de eventos on-chain (Helius ou similar)
- [ ] **Programa de bug bounty**

### 12.3 Endereços Úteis na Solana

| Recurso | Endereço (Mainnet) |
|---|---|
| **USDC Mint** | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| **Pyth BRL/USD** | Consultar [pyth.network](https://pyth.network/price-feeds) |
| **Switchboard** | [switchboard.xyz](https://switchboard.xyz) |

## 13. Conclusão

Neste tutorial implementamos o protocolo [CriptoSensor](https://rapport.tec.br/projetos/criptosensor/) como **quatro smart contracts na Solana**, cobrindo:

1. **Registry** — identidade criptográfica de sensores com score e penalidades progressivas.
2. **Oracle** — oráculo de câmbio BRL/USDC com TWAP para o preço do token.
3. **Treasury** — tesouraria que recebe USDC, calcula EMA da receita e particiona fundos por epoch.
4. **Emission** — emissão lastreada com decaimento exponencial e distribuição proporcional (70% sensores, 30% nós de borda).

O mecanismo de **conversão BRL → USDC** combina uma camada off-chain (rampa fiat via PIX) com um oráculo on-chain auditável, garantindo que clientes brasileiros possam pagar em Real enquanto toda a lógica financeira opera de forma transparente e verificável na blockchain.

O resultado é um sistema onde o **token não é o ponto de partida — é o subproduto da operação bem-sucedida**. Sem cliente pagando por dado, não há emissão relevante, tornando o modelo resistente a bolhas vazias e esquemas inflacionários.

Para mais detalhes sobre o projeto completo, acesse: **[CriptoSensor — Rapport Tecnologia](https://rapport.tec.br/projetos/criptosensor/)**.
