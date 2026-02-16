---
title: "Deploy de Smart Contracts na Solana com Anchor e Cargo — Do Teste à Produção"
tags: [blockchain, solana, rust, smartcontract, anchor, cargo, devnet, mainnet, tutorial, deploy]
categories: [programacao]
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

Neste tutorial completo, você vai aprender **passo a passo** como criar, compilar, testar e fazer deploy de um smart contract (programa) na **Solana**, usando **Anchor** como framework e **Cargo** como gerenciador de pacotes Rust. Cobriremos todo o ciclo: do **localhost** ao **devnet** e, por fim, ao **mainnet-beta** (produção).

<!--more-->

## 1. O que é um Programa na Solana?

Na Solana, smart contracts são chamados de **programas**. Diferente do Ethereum, onde o contrato armazena código e estado juntos, na Solana:

- O **programa** é stateless — contém apenas a lógica executável.
- O **estado** é armazenado em **contas** separadas, vinculadas ao programa.
- Programas são compilados para **BPF** (Berkeley Packet Filter) e implantados como bytecode on-chain.

### 1.1 Ferramentas Principais

| Ferramenta | Função |
|---|---|
| **Rust** | Linguagem de programação usada para escrever os programas |
| **Cargo** | Gerenciador de pacotes e build system do Rust |
| **Solana CLI** | Interface de linha de comando para interagir com a rede Solana |
| **Anchor** | Framework que simplifica o desenvolvimento, testes e deploy de programas Solana |
| **AVM** | Gerenciador de versões do Anchor |

### 1.2 Ambientes da Solana

| Ambiente | URL RPC | Propósito |
|---|---|---|
| **Localhost** | `http://localhost:8899` | Validador local para testes rápidos |
| **Devnet** | `https://api.devnet.solana.com` | Rede de teste pública com SOL gratuito |
| **Testnet** | `https://api.testnet.solana.com` | Rede de testes para validadores |
| **Mainnet-Beta** | `https://api.mainnet-beta.solana.com` | Rede de produção com SOL real |

## 2. Instalação do Ambiente

### 2.1 Instalar Rust e Cargo

Cargo vem junto com a instalação do Rust via `rustup`:

```bash
# Linux / macOS
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Confirmar instalação
rustc --version
cargo --version
```

No **Windows**, baixe o instalador em [rustup.rs](https://rustup.rs/) e siga as instruções. Será necessário instalar também o **Visual Studio Build Tools** com o componente "C++ build tools".

### 2.2 Instalar a Solana CLI

```bash
# Linux / macOS
sh -c "$(curl -sSfL https://release.anza.xyz/stable/install)"

# Adicionar ao PATH (já feito automaticamente na maioria dos casos)
export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"

# Verificar
solana --version
```

No **Windows**, use o instalador oficial ou WSL2 (recomendado).

### 2.3 Instalar Anchor via AVM

```bash
# Instalar o AVM (Anchor Version Manager)
cargo install --git https://github.com/coral-xyz/anchor avm --locked

# Instalar a versão mais recente do Anchor
avm install latest
avm use latest

# Verificar
anchor --version
```

### 2.4 Gerar uma Carteira Local

```bash
# Gerar um novo keypair (carteira) para desenvolvimento
solana-keygen new --outfile ~/.config/solana/id.json

# Exibir o endereço público
solana address
```

> **Atenção:** Guarde o arquivo `id.json` em segurança. Em produção, use uma **hardware wallet** ou **multisig**.

## 3. Criar o Projeto com Anchor

```bash
# Criar um novo projeto Anchor
anchor init meu_primeiro_programa
cd meu_primeiro_programa
```

Isso gera a seguinte estrutura:

```
meu_primeiro_programa/
├── Anchor.toml          # Configuração do Anchor (rede, programas, etc.)
├── Cargo.toml           # Workspace Rust
├── package.json         # Dependências JS/TS para testes
├── tsconfig.json        # Configuração TypeScript
├── app/                 # Frontend (opcional)
├── migrations/          # Scripts de migração
│   └── deploy.ts
├── programs/            # Seus programas Solana (smart contracts)
│   └── meu_primeiro_programa/
│       ├── Cargo.toml   # Dependências Rust do programa
│       └── src/
│           └── lib.rs   # Código principal do programa
└── tests/               # Testes de integração
    └── meu_primeiro_programa.ts
```

### 3.1 Entendendo o Anchor.toml

```toml
[features]
seeds = false
skip-lint = false

[programs.localnet]
meu_primeiro_programa = "Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS"

[registry]
url = "https://api.apr.dev"

[provider]
cluster = "Localnet"
wallet = "~/.config/solana/id.json"

[scripts]
test = "anchor test"
```

Os campos mais importantes:

- **`[programs.localnet]`** — define o Program ID para a rede local
- **`[provider].cluster`** — define em qual rede o Anchor opera (`Localnet`, `Devnet`, `Mainnet`)
- **`[provider].wallet`** — caminho para o keypair usado no deploy

### 3.2 Dependências do Programa (Cargo.toml)

O `Cargo.toml` do programa fica em `programs/meu_primeiro_programa/Cargo.toml`:

```toml
[package]
name = "meu-primeiro-programa"
version = "0.1.0"
description = "Meu primeiro smart contract na Solana"
edition = "2021"

[lib]
crate-type = ["cdylib", "lib"]
name = "meu_primeiro_programa"

[features]
no-entrypoint = []
no-idl = []
no-log-ix-name = []
cpi = ["no-entrypoint"]
default = []
idl-build = ["anchor-lang/idl-build"]

[dependencies]
anchor-lang = "0.30"
```

- **`crate-type = ["cdylib", "lib"]`** — necessário para compilar como biblioteca dinâmica (BPF)
- **`anchor-lang`** — framework principal do Anchor
- Se precisar de tokens SPL, adicione: `anchor-spl = "0.30"`

## 4. Escrevendo o Smart Contract

Vamos criar um programa simples de **registro de mensagens** onde qualquer pessoa pode armazenar uma mensagem on-chain vinculada à sua carteira.

### 4.1 Código do Programa

Edite `programs/meu_primeiro_programa/src/lib.rs`:

```rust
use anchor_lang::prelude::*;

declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");

/// Tamanho máximo da mensagem em bytes
const MAX_MSG_LEN: usize = 280;

#[program]
pub mod meu_primeiro_programa {
    use super::*;

    /// Cria uma nova mensagem on-chain vinculada ao autor.
    pub fn criar_mensagem(
        ctx: Context<CriarMensagem>,
        conteudo: String,
    ) -> Result<()> {
        require!(conteudo.len() <= MAX_MSG_LEN, MeuErro::MensagemMuitoLonga);
        require!(!conteudo.is_empty(), MeuErro::MensagemVazia);

        let mensagem = &mut ctx.accounts.mensagem;
        mensagem.autor = ctx.accounts.autor.key();
        mensagem.conteudo = conteudo.clone();
        mensagem.timestamp = Clock::get()?.unix_timestamp;
        mensagem.atualizada = false;
        mensagem.bump = ctx.bumps.mensagem;

        msg!("Mensagem criada por {}: {}", mensagem.autor, conteudo);
        Ok(())
    }

    /// Atualiza o conteúdo de uma mensagem existente.
    /// Somente o autor original pode atualizar.
    pub fn atualizar_mensagem(
        ctx: Context<AtualizarMensagem>,
        novo_conteudo: String,
    ) -> Result<()> {
        require!(novo_conteudo.len() <= MAX_MSG_LEN, MeuErro::MensagemMuitoLonga);
        require!(!novo_conteudo.is_empty(), MeuErro::MensagemVazia);

        let mensagem = &mut ctx.accounts.mensagem;
        mensagem.conteudo = novo_conteudo.clone();
        mensagem.timestamp = Clock::get()?.unix_timestamp;
        mensagem.atualizada = true;

        msg!("Mensagem atualizada: {}", novo_conteudo);
        Ok(())
    }

    /// Remove (fecha) a conta da mensagem, devolvendo o rent ao autor.
    pub fn deletar_mensagem(_ctx: Context<DeletarMensagem>) -> Result<()> {
        msg!("Mensagem deletada");
        Ok(())
    }
}

// ---------------------------------------------------------------------------
// Contas (estado armazenado on-chain)
// ---------------------------------------------------------------------------

#[account]
#[derive(InitSpace)]
pub struct Mensagem {
    /// Chave pública do autor da mensagem
    pub autor: Pubkey,
    /// Conteúdo da mensagem (até 280 caracteres)
    #[max_len(280)]
    pub conteudo: String,
    /// Timestamp Unix da criação/atualização
    pub timestamp: i64,
    /// Indica se a mensagem já foi editada
    pub atualizada: bool,
    /// Bump seed para a PDA
    pub bump: u8,
}

// ---------------------------------------------------------------------------
// Contextos (definição de contas por instrução)
// ---------------------------------------------------------------------------

#[derive(Accounts)]
#[instruction(conteudo: String)]
pub struct CriarMensagem<'info> {
    /// Autor da mensagem (paga o rent)
    #[account(mut)]
    pub autor: Signer<'info>,

    /// Conta da mensagem — criada como PDA
    #[account(
        init,
        payer = autor,
        space = 8 + Mensagem::INIT_SPACE,
        seeds = [b"mensagem", autor.key().as_ref()],
        bump
    )]
    pub mensagem: Account<'info, Mensagem>,

    pub system_program: Program<'info, System>,
}

#[derive(Accounts)]
pub struct AtualizarMensagem<'info> {
    /// Somente o autor original pode atualizar
    #[account(
        constraint = autor.key() == mensagem.autor @ MeuErro::NaoAutorizado
    )]
    pub autor: Signer<'info>,

    #[account(
        mut,
        seeds = [b"mensagem", autor.key().as_ref()],
        bump = mensagem.bump,
    )]
    pub mensagem: Account<'info, Mensagem>,
}

#[derive(Accounts)]
pub struct DeletarMensagem<'info> {
    /// Somente o autor pode deletar; recebe o rent de volta
    #[account(mut)]
    pub autor: Signer<'info>,

    #[account(
        mut,
        close = autor,
        seeds = [b"mensagem", autor.key().as_ref()],
        bump = mensagem.bump,
        constraint = autor.key() == mensagem.autor @ MeuErro::NaoAutorizado,
    )]
    pub mensagem: Account<'info, Mensagem>,
}

// ---------------------------------------------------------------------------
// Erros personalizados
// ---------------------------------------------------------------------------

#[error_code]
pub enum MeuErro {
    #[msg("A mensagem excede o tamanho máximo de 280 caracteres")]
    MensagemMuitoLonga,
    #[msg("A mensagem não pode estar vazia")]
    MensagemVazia,
    #[msg("Somente o autor original pode modificar esta mensagem")]
    NaoAutorizado,
}
```

### 4.2 Conceitos-Chave Explicados

**Program Derived Addresses (PDAs):**
As seeds `["mensagem", autor.key()]` criam um endereço determinístico e único para cada autor. Isso significa:

- Cada carteira pode ter exatamente **uma** mensagem ativa.
- O endereço é previsível — qualquer pessoa pode derivá-lo sabendo o autor.
- Não existe chave privada associada à PDA — somente o programa pode operá-la.

**`init` e `close`:**
- `init` cria a conta e cobra **rent** (depósito de SOL para manter dados on-chain).
- `close` fecha a conta e devolve o rent para o destinatário especificado.

**`InitSpace`:**
O derive macro `InitSpace` calcula automaticamente o espaço necessário para a conta. O `8 +` antes é o **discriminator** do Anchor (identificador único da conta).

## 5. Compilando com Cargo e Anchor

### 5.1 Build do Programa

```bash
# Compilar o programa (usa Cargo internamente)
anchor build
```

O que acontece durante o `anchor build`:

1. **Cargo** compila o código Rust para o target `bpfel-unknown-unknown` (BPF)
2. O Anchor gera o **IDL** (Interface Definition Language) em `target/idl/`
3. O bytecode compilado vai para `target/deploy/`
4. Os **types** TypeScript são gerados em `target/types/`

```
target/
├── deploy/
│   ├── meu_primeiro_programa.so        # Bytecode do programa (BPF)
│   └── meu_primeiro_programa-keypair.json  # Keypair do programa
├── idl/
│   └── meu_primeiro_programa.json      # IDL (interface)
└── types/
    └── meu_primeiro_programa.ts        # Tipos TypeScript
```

### 5.2 Sincronizar o Program ID

Na primeira vez, o Anchor gera um keypair para o programa. Você precisa atualizar o `declare_id!` no código:

```bash
# Listar os Program IDs gerados
anchor keys list

# Saída: meu_primeiro_programa: <NOVO_PROGRAM_ID>
```

Copie o Program ID exibido e atualize:

1. Em `programs/meu_primeiro_programa/src/lib.rs` — no `declare_id!("...")`
2. Em `Anchor.toml` — em `[programs.localnet]`

```bash
# Sincronizar automaticamente (Anchor >= 0.30)
anchor keys sync
```

Depois, recompile:

```bash
anchor build
```

## 6. Testando no Ambiente Local (Localhost)

### 6.1 Configurar para Localhost

```bash
# Configurar a Solana CLI para usar o validador local
solana config set --url localhost
```

Verifique o `Anchor.toml`:

```toml
[provider]
cluster = "Localnet"
wallet = "~/.config/solana/id.json"
```

### 6.2 Escrever os Testes

Edite `tests/meu_primeiro_programa.ts`:

```typescript
import * as anchor from "@coral-xyz/anchor";
import { Program } from "@coral-xyz/anchor";
import { MeuPrimeiroPrograma } from "../target/types/meu_primeiro_programa";
import { PublicKey } from "@solana/web3.js";
import { assert } from "chai";

describe("meu_primeiro_programa", () => {
  const provider = anchor.AnchorProvider.env();
  anchor.setProvider(provider);

  const program = anchor.workspace
    .MeuPrimeiroPrograma as Program<MeuPrimeiroPrograma>;

  const autor = provider.wallet as anchor.Wallet;

  let mensagemPda: PublicKey;

  before(() => {
    // Derivar a PDA da mensagem
    [mensagemPda] = PublicKey.findProgramAddressSync(
      [Buffer.from("mensagem"), autor.publicKey.toBuffer()],
      program.programId
    );
  });

  it("Cria uma mensagem", async () => {
    await program.methods
      .criarMensagem("Olá, Solana!")
      .accounts({
        autor: autor.publicKey,
        mensagem: mensagemPda,
        systemProgram: anchor.web3.SystemProgram.programId,
      })
      .rpc();

    const conta = await program.account.mensagem.fetch(mensagemPda);
    assert.equal(conta.conteudo, "Olá, Solana!");
    assert.equal(conta.autor.toBase58(), autor.publicKey.toBase58());
    assert.isFalse(conta.atualizada);
    console.log("  ✓ Mensagem criada:", conta.conteudo);
    console.log("  ✓ Timestamp:", new Date(conta.timestamp.toNumber() * 1000));
  });

  it("Atualiza a mensagem", async () => {
    await program.methods
      .atualizarMensagem("Mensagem atualizada na Solana!")
      .accounts({
        autor: autor.publicKey,
        mensagem: mensagemPda,
      })
      .rpc();

    const conta = await program.account.mensagem.fetch(mensagemPda);
    assert.equal(conta.conteudo, "Mensagem atualizada na Solana!");
    assert.isTrue(conta.atualizada);
    console.log("  ✓ Mensagem atualizada:", conta.conteudo);
  });

  it("Rejeita mensagem vazia", async () => {
    try {
      await program.methods
        .atualizarMensagem("")
        .accounts({
          autor: autor.publicKey,
          mensagem: mensagemPda,
        })
        .rpc();
      assert.fail("Deveria ter lançado erro");
    } catch (err) {
      assert.include(err.toString(), "MensagemVazia");
      console.log("  ✓ Erro esperado capturado: MensagemVazia");
    }
  });

  it("Rejeita mensagem muito longa", async () => {
    const mensagemLonga = "A".repeat(281);
    try {
      await program.methods
        .atualizarMensagem(mensagemLonga)
        .accounts({
          autor: autor.publicKey,
          mensagem: mensagemPda,
        })
        .rpc();
      assert.fail("Deveria ter lançado erro");
    } catch (err) {
      assert.include(err.toString(), "MensagemMuitoLonga");
      console.log("  ✓ Erro esperado capturado: MensagemMuitoLonga");
    }
  });

  it("Impede atualização por não-autor", async () => {
    const impostor = anchor.web3.Keypair.generate();

    // Airdrop SOL para o impostor
    const sig = await provider.connection.requestAirdrop(
      impostor.publicKey,
      1 * anchor.web3.LAMPORTS_PER_SOL
    );
    await provider.connection.confirmTransaction(sig);

    try {
      await program.methods
        .atualizarMensagem("Tentativa de hack!")
        .accounts({
          autor: impostor.publicKey,
          mensagem: mensagemPda,
        })
        .signers([impostor])
        .rpc();
      assert.fail("Deveria ter lançado erro");
    } catch (err) {
      // A PDA não vai bater porque o impostor tem outra chave
      console.log("  ✓ Impostor bloqueado corretamente");
    }
  });

  it("Deleta a mensagem e devolve o rent", async () => {
    const saldoAntes = await provider.connection.getBalance(autor.publicKey);

    await program.methods
      .deletarMensagem()
      .accounts({
        autor: autor.publicKey,
        mensagem: mensagemPda,
      })
      .rpc();

    const saldoDepois = await provider.connection.getBalance(autor.publicKey);
    assert.isAbove(saldoDepois, saldoAntes);
    console.log(
      "  ✓ Rent devolvido:",
      (saldoDepois - saldoAntes) / anchor.web3.LAMPORTS_PER_SOL,
      "SOL"
    );

    // Verificar que a conta não existe mais
    try {
      await program.account.mensagem.fetch(mensagemPda);
      assert.fail("Conta deveria ter sido fechada");
    } catch (err) {
      console.log("  ✓ Conta fechada com sucesso");
    }
  });
});
```

### 6.3 Rodar os Testes Localmente

```bash
# Instalar dependências JS
npm install

# Rodar testes (inicia o validador local automaticamente)
anchor test
```

O comando `anchor test` faz o seguinte:

1. Inicia um **validador local** (`solana-test-validator`)
2. Faz o **build** do programa
3. Faz o **deploy** no validador local
4. Executa os testes TypeScript com **Mocha**
5. Encerra o validador

Saída esperada:

```
  meu_primeiro_programa
    ✓ Cria uma mensagem
      ✓ Mensagem criada: Olá, Solana!
      ✓ Timestamp: 2026-02-14T18:30:00.000Z
    ✓ Atualiza a mensagem
      ✓ Mensagem atualizada: Mensagem atualizada na Solana!
    ✓ Rejeita mensagem vazia
      ✓ Erro esperado capturado: MensagemVazia
    ✓ Rejeita mensagem muito longa
      ✓ Erro esperado capturado: MensagemMuitoLonga
    ✓ Impede atualização por não-autor
      ✓ Impostor bloqueado corretamente
    ✓ Deleta a mensagem e devolve o rent
      ✓ Rent devolvido: 0.00178 SOL
      ✓ Conta fechada com sucesso

  6 passing (4s)
```

### 6.4 Testando Manualmente com o Validador Local

Você também pode rodar o validador em um terminal separado e interagir manualmente:

```bash
# Terminal 1: iniciar o validador local
solana-test-validator

# Terminal 2: deploy manual
anchor build
anchor deploy

# Terminal 2: verificar o deploy
solana program show <PROGRAM_ID>
```

## 7. Deploy na Devnet (Ambiente de Teste Público)

A **Devnet** é a rede de teste pública da Solana. Funciona como a mainnet, mas o SOL não tem valor real.

### 7.1 Configurar para Devnet

```bash
# Configurar a Solana CLI para devnet
solana config set --url devnet

# Verificar configuração
solana config get
```

Atualize o `Anchor.toml`:

```toml
[programs.devnet]
meu_primeiro_programa = "<SEU_PROGRAM_ID>"

[provider]
cluster = "Devnet"
wallet = "~/.config/solana/id.json"
```

### 7.2 Obter SOL de Teste (Airdrop)

```bash
# Solicitar SOL de teste (máximo 2 SOL por vez)
solana airdrop 2

# Verificar saldo
solana balance
```

Se o airdrop falhar (limite de taxa), use a faucet web: [faucet.solana.com](https://faucet.solana.com/).

### 7.3 Fazer o Deploy

```bash
# Build
anchor build

# Deploy na devnet
anchor deploy --provider.cluster devnet
```

Saída esperada:

```
Deploying cluster: https://api.devnet.solana.com
Upgrade authority: ~/.config/solana/id.json
Deploying program "meu_primeiro_programa"...
Program path: target/deploy/meu_primeiro_programa.so
Program Id: <SEU_PROGRAM_ID>

Deploy success
```

### 7.4 Verificar o Deploy

```bash
# Ver informações do programa
solana program show <SEU_PROGRAM_ID>

# Saída esperada:
# Program Id: <SEU_PROGRAM_ID>
# Owner: BPFLoaderUpgradeab1e11111111111111111111111
# ProgramData Address: <DATA_ADDRESS>
# Authority: <SUA_CARTEIRA>
# Last Deployed In Slot: 123456789
# Data Length: 200000 bytes
# Balance: 1.39 SOL
```

Você também pode verificar no **Solana Explorer**:
`https://explorer.solana.com/address/<SEU_PROGRAM_ID>?cluster=devnet`

### 7.5 Rodar Testes na Devnet

```bash
# Rodar testes apontando para devnet (sem iniciar validador local)
anchor test --skip-local-validator --provider.cluster devnet
```

### 7.6 Atualizar (Upgrade) o Programa

Se precisar corrigir ou adicionar funcionalidades:

```bash
# Faça as alterações no código Rust
# ...

# Recompile
anchor build

# Faça o upgrade (deploy sobre a versão existente)
anchor upgrade target/deploy/meu_primeiro_programa.so \
  --program-id <SEU_PROGRAM_ID> \
  --provider.cluster devnet
```

> **Nota:** O upgrade só é possível enquanto houver uma **upgrade authority** definida. Em produção, essa autoridade geralmente é um **multisig**.

## 8. Deploy na Mainnet-Beta (Produção)

### 8.1 Checklist Pré-Deploy

Antes de fazer deploy na mainnet, verifique cada item:

| # | Item | Detalhe |
|---|---|---|
| 1 | **Testes completos** | Todos os testes passando na devnet |
| 2 | **Auditoria de segurança** | Contrate uma empresa especializada (Neodyme, OtterSec, Halborn) |
| 3 | **Revisão de código** | Revise contra vulnerabilidades comuns (reentrância, overflow, missing signer checks) |
| 4 | **Verificar espaço de contas** | Garanta que `space` é suficiente para todos os campos |
| 5 | **Verificar constraints** | Todo `Account` deve ter constraints de validação adequados |
| 6 | **SOL para deploy** | O deploy consome SOL proporcional ao tamanho do bytecode (~1-3 SOL para programas típicos) |
| 7 | **Backup do keypair** | Salve o keypair do programa (`target/deploy/meu_primeiro_programa-keypair.json`) em local seguro |
| 8 | **Plano de upgrade** | Defina quem tem autoridade para atualizar o programa |

### 8.2 Configurar para Mainnet

```bash
# Configurar a Solana CLI para mainnet
solana config set --url mainnet-beta

# Verificar saldo (precisa de SOL real)
solana balance
```

Atualize o `Anchor.toml`:

```toml
[programs.mainnet]
meu_primeiro_programa = "<SEU_PROGRAM_ID>"

[provider]
cluster = "Mainnet"
wallet = "~/.config/solana/id.json"
```

### 8.3 Deploy com Priority Fees

Na mainnet, transações com **priority fees** têm mais chance de ser incluídas rapidamente:

```bash
# Build final
anchor build

# Deploy na mainnet
anchor deploy --provider.cluster mainnet
```

Para deployments grandes, pode ser necessário usar o Solana CLI diretamente com buffer:

```bash
# Criar buffer de upload (para programas grandes)
solana program write-buffer target/deploy/meu_primeiro_programa.so

# Deploy a partir do buffer
solana program deploy --buffer <BUFFER_ADDRESS> \
  --program-id target/deploy/meu_primeiro_programa-keypair.json
```

### 8.4 Configurar Multisig como Upgrade Authority

Em produção, **nunca** deixe a upgrade authority em uma carteira individual. Use um **multisig**:

```bash
# Instalar Squads CLI (exemplo com Squads Protocol)
npm install -g @sqds/cli

# Transferir a authority para o multisig
solana program set-upgrade-authority <PROGRAM_ID> \
  --new-upgrade-authority <MULTISIG_ADDRESS>
```

Com **Squads Protocol** ([squads.so](https://squads.so)), você cria um multisig onde múltiplas pessoas precisam aprovar cada upgrade do programa.

### 8.5 Tornar o Programa Imutável (Opcional)

Se o programa estiver finalizado e você quiser garantir que **nunca mais** será alterado:

```bash
# CUIDADO: isso é IRREVERSÍVEL
solana program set-upgrade-authority <PROGRAM_ID> --final
```

Após este comando, o programa fica congelado para sempre on-chain. Use apenas quando tiver certeza absoluta.

## 9. Gerenciando Múltiplos Ambientes

### 9.1 Anchor.toml Completo

```toml
[features]
seeds = false
skip-lint = false

[programs.localnet]
meu_primeiro_programa = "<PROGRAM_ID>"

[programs.devnet]
meu_primeiro_programa = "<PROGRAM_ID>"

[programs.mainnet]
meu_primeiro_programa = "<PROGRAM_ID>"

[registry]
url = "https://api.apr.dev"

[provider]
cluster = "Localnet"
wallet = "~/.config/solana/id.json"

[scripts]
test = "anchor test"
```

### 9.2 Script de Deploy por Ambiente

Crie um script para facilitar deploys:

```bash
#!/bin/bash
# scripts/deploy.sh

set -e

ENV=${1:-localnet}

echo "═══════════════════════════════════════════"
echo "  DEPLOY: $ENV"
echo "═══════════════════════════════════════════"

case $ENV in
  localnet)
    solana config set --url localhost
    ;;
  devnet)
    solana config set --url devnet
    solana airdrop 2 2>/dev/null || echo "Airdrop falhou (limite atingido)"
    ;;
  mainnet)
    solana config set --url mainnet-beta
    echo "ATENÇÃO: Deploy em PRODUÇÃO!"
    echo "Saldo atual: $(solana balance)"
    read -p "Confirma? (s/n) " confirm
    [ "$confirm" != "s" ] && exit 1
    ;;
  *)
    echo "Uso: $0 [localnet|devnet|mainnet]"
    exit 1
    ;;
esac

echo "1. Compilando..."
anchor build

echo "2. Program ID:"
anchor keys list

echo "3. Fazendo deploy em $ENV..."
anchor deploy --provider.cluster $ENV

echo "4. Verificando..."
PROGRAM_ID=$(anchor keys list | awk '{print $2}')
solana program show $PROGRAM_ID

echo "═══════════════════════════════════════════"
echo "  DEPLOY CONCLUÍDO: $ENV"
echo "═══════════════════════════════════════════"
```

## 10. Usando Cargo Diretamente

Embora o Anchor abstraia o Cargo, é útil saber usá-lo diretamente:

### 10.1 Comandos Cargo Úteis

```bash
# Verificar se o código compila (mais rápido que build completo)
cargo check
# Executar a partir do diretório do programa:
cd programs/meu_primeiro_programa && cargo check

# Rodar clippy (linter do Rust) para detectar problemas
cargo clippy --all-targets

# Formatar o código
cargo fmt

# Ver a árvore de dependências
cargo tree

# Atualizar dependências
cargo update

# Compilar manualmente para BPF (o que anchor build faz internamente)
cargo build-bpf
```

### 10.2 Adicionar Dependências com Cargo

```bash
# Adicionar anchor-spl para trabalhar com tokens SPL
cd programs/meu_primeiro_programa
cargo add anchor-spl@0.30

# Adicionar dependência de outro programa do workspace
cargo add --path ../outro_programa --features cpi
```

### 10.3 Workspace Cargo com Múltiplos Programas

Se seu projeto tem vários programas, o `Cargo.toml` raiz define um workspace:

```toml
[workspace]
members = [
    "programs/programa_a",
    "programs/programa_b",
    "programs/programa_c",
]
resolver = "2"

[profile.release]
overflow-checks = true
lto = "fat"
codegen-units = 1
```

- **`overflow-checks = true`** — proteção contra overflow aritmético em release
- **`lto = "fat"`** — Link Time Optimization para reduzir o tamanho do bytecode
- **`codegen-units = 1`** — otimização máxima (compilação mais lenta, binário menor)

## 11. Solução de Problemas Comuns

### 11.1 Erros Frequentes

| Erro | Causa | Solução |
|---|---|---|
| `Error: Account not found` | Conta PDA ainda não foi criada | Execute a instrução `init` primeiro |
| `Error: insufficient funds` | Sem SOL para pagar rent/taxa | Faça airdrop (`solana airdrop 2`) ou transfira SOL |
| `Error: custom program error: 0x0` | Constraint falhou no programa | Verifique as validações e constraints das contas |
| `Error: Program failed to complete` | Bug no programa (panic, overflow) | Adicione logs com `msg!()` e revise a lógica |
| `Error: Transaction too large` | Transação excede 1232 bytes | Reduza o número de contas ou use lookup tables |
| `anchor build` falha | Versão incompatível de Rust/Anchor | Verifique `rustc --version` e `anchor --version` |
| `declare_id!` não bate | Program ID desatualizado | Execute `anchor keys sync` e recompile |

### 11.2 Dicas de Debug

```bash
# Ver logs detalhados do programa durante testes
anchor test -- --features "debug"

# Aumentar verbosidade dos logs do validador local
solana-test-validator --log

# Verificar logs de uma transação específica
solana confirm -v <SIGNATURE>

# Ver logs em tempo real no validador local
solana logs
```

No código Rust, use `msg!()` para logging:

```rust
msg!("Debug: valor = {}", minha_variavel);
msg!("Debug: conta = {:?}", ctx.accounts.minha_conta.key());
```

## 12. Boas Práticas de Segurança

### 12.1 Validação de Contas

Sempre valide **todas** as contas recebidas em cada instrução:

```rust
#[derive(Accounts)]
pub struct MinhaInstrucao<'info> {
    // ✅ Verifica que é um signer
    pub autoridade: Signer<'info>,

    // ✅ Verifica owner, seeds e bump
    #[account(
        mut,
        seeds = [b"dados", autoridade.key().as_ref()],
        bump = dados.bump,
        constraint = dados.dono == autoridade.key() @ MeuErro::NaoAutorizado,
    )]
    pub dados: Account<'info, MeusDados>,

    // ✅ Verifica que é o System Program correto
    pub system_program: Program<'info, System>,
}
```

### 12.2 Aritmética Segura

```rust
// ❌ NUNCA faça isso
let resultado = a + b;

// ✅ Use checked arithmetic
let resultado = a.checked_add(b).ok_or(MeuErro::Overflow)?;

// ✅ Ou saturating para casos onde truncar é aceitável
let resultado = a.saturating_sub(b);
```

### 12.3 Gestão de Keypairs

```bash
# Para produção, NUNCA use o keypair padrão
# Gere um keypair dedicado para o programa
solana-keygen new --outfile ./program-keypair.json

# Faça backup em local seguro (offline, cofre, etc.)
# Considere usar hardware wallet para a deploy authority
```

## 13. Resumo do Fluxo Completo

```
┌─────────────────────────────────────────────────┐
│              CICLO DE DESENVOLVIMENTO            │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. ESCREVER          anchor init / editar .rs  │
│         │                                       │
│         ▼                                       │
│  2. COMPILAR          anchor build (usa Cargo)  │
│         │                                       │
│         ▼                                       │
│  3. TESTAR LOCAL      anchor test               │
│         │              └─ solana-test-validator  │
│         ▼              └─ deploy local           │
│                        └─ mocha tests            │
│  4. DEPLOY DEVNET     anchor deploy (devnet)    │
│         │              └─ SOL de teste grátis    │
│         ▼              └─ testes na rede pública │
│                                                 │
│  5. AUDITORIA         revisão de segurança      │
│         │                                       │
│         ▼                                       │
│  6. DEPLOY MAINNET    anchor deploy (mainnet)   │
│         │              └─ SOL real necessário    │
│         ▼              └─ multisig authority     │
│                                                 │
│  7. MONITORAR         logs, explorer, alertas   │
│                                                 │
└─────────────────────────────────────────────────┘
```

## 14. Recursos e Referências

| Recurso | Link |
|---|---|
| **Documentação Solana** | [docs.solana.com](https://docs.solana.com) |
| **Documentação Anchor** | [anchor-lang.com](https://www.anchor-lang.com) |
| **Solana Cookbook** | [solanacookbook.com](https://solanacookbook.com) |
| **Solana Explorer** | [explorer.solana.com](https://explorer.solana.com) |
| **Solana Playground** | [beta.solpg.io](https://beta.solpg.io) — IDE online para testar sem instalar nada |
| **Cargo Book** | [doc.rust-lang.org/cargo](https://doc.rust-lang.org/cargo/) |
| **Squads (Multisig)** | [squads.so](https://squads.so) |
| **Faucet Devnet** | [faucet.solana.com](https://faucet.solana.com) |

## 15. Conclusão

Neste tutorial cobrimos o ciclo completo de desenvolvimento de smart contracts na Solana:

1. **Instalação** — Rust, Cargo, Solana CLI e Anchor.
2. **Criação do projeto** — estrutura Anchor com Cargo workspace.
3. **Escrita do programa** — lógica em Rust com PDAs, constraints e erros personalizados.
4. **Compilação** — `anchor build` usando Cargo para compilar para BPF.
5. **Testes locais** — `anchor test` com validador local e testes TypeScript.
6. **Deploy na Devnet** — rede de teste com SOL gratuito para validação.
7. **Deploy na Mainnet** — produção com SOL real, auditoria e multisig.

O Anchor abstrai grande parte da complexidade, mas é essencial entender o que Cargo faz por trás: compilação, gestão de dependências, otimização do bytecode e organização do workspace.

Comece sempre no **localhost**, evolua para a **devnet** quando os testes locais passarem, e só vá para a **mainnet** após auditoria e revisão completa. A blockchain é imutável — erros em produção não têm "rollback".
