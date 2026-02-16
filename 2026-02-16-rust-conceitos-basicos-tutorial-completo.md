---
title: "Rust — Conceitos Básicos para Smart Contracts: Tutorial Completo para Iniciantes"
tags: [rust, tutorial, programação, smartcontract, solana, blockchain, linguagem, iniciante]
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

Se você pretende desenvolver **smart contracts na Solana** (ou qualquer projeto de sistemas de alta performance), precisa dominar os fundamentos de **Rust**. Este tutorial ensina os conceitos essenciais da linguagem usando exemplos práticos e progressivos, preparando você para acompanhar tutoriais avançados como o [CriptoSensor na Solana](/projetos/2027/02/14/criptosensor-smartcontract-solana-usdc-tutorial/).

<!--more-->

## 1. Instalação e Primeiro Programa

```bash
# Instalar Rust via rustup (Linux/macOS/WSL)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Verificar a instalação
rustc --version
cargo --version

# Criar um novo projeto
cargo new rust_basico
cd rust_basico
```

O arquivo `src/main.rs` já vem com o clássico "Hello, world!":

```rust
fn main() {
    println!("Olá, Rust!");
}
```

Para compilar e executar:

```bash
cargo run
```

> **Nota:** `println!` não é uma função — é uma **macro** (note o `!`). Macros são expandidas em tempo de compilação e permitem sintaxe que funções comuns não suportam, como formatação com `{}`. Veremos mais sobre macros na seção 16.

## 2. Variáveis e Mutabilidade

Em Rust, variáveis são **imutáveis por padrão**. Isso evita bugs causados por modificações acidentais.

```rust
fn main() {
    let x = 10;        // imutável
    // x = 20;         // ❌ ERRO: cannot assign twice to immutable variable

    let mut y = 10;    // mutável (precisa de `mut`)
    y = 20;            // ✅ OK
    println!("y = {}", y);
}
```

### 2.1 Shadowing

Você pode redeclarar uma variável com `let`, mesmo mudando o tipo:

```rust
fn main() {
    let valor = "42";           // &str
    let valor = valor.len();    // agora é usize — shadowing
    println!("Tamanho: {}", valor);
}
```

### 2.2 Por que isso importa em Smart Contracts?

Em contratos como o CriptoSensor, mutabilidade é controlada explicitamente:

```rust
// Somente com &mut podemos modificar o estado da conta
let sensor = &mut ctx.accounts.sensor_account;
sensor.score = 0;       // ✅ permitido porque sensor é &mut
sensor.is_active = true;
```

Sem o `mut`, o compilador impede a modificação — um mecanismo de segurança crucial para smart contracts.

## 3. Tipos Primitivos

Rust é **fortemente tipado**. Conheça os tipos que aparecem frequentemente em smart contracts:

### 3.1 Inteiros

| Tipo | Tamanho | Faixa | Uso típico |
|---|---|---|---|
| `u8` | 8 bits | 0 a 255 | Flags, níveis de penalidade |
| `u64` | 64 bits | 0 a ~18×10¹⁸ | Valores monetários, scores |
| `i64` | 64 bits | -9×10¹⁸ a 9×10¹⁸ | Timestamps, coordenadas |
| `u128` | 128 bits | 0 a ~3.4×10³⁸ | Cálculos intermediários |
| `usize` | Depende da plataforma | — | Índices, tamanhos |

```rust
fn main() {
    let penalty_level: u8 = 2;
    let score: u64 = 150_000;          // underscores para legibilidade
    let timestamp: i64 = 1_700_000_000;
    let calculo: u128 = 999_999_999_999;
    let tamanho: usize = 32;

    println!("Penalty: {}, Score: {}", penalty_level, score);
}
```

### 3.2 Booleanos e Strings

```rust
fn main() {
    // Booleano
    let is_active: bool = true;

    // String (heap-allocated, mutável, owned)
    let sensor_id: String = String::from("temp-001");

    // &str (referência a string, imutável)
    let tipo: &str = "temperature";

    println!("Sensor {} ({}) ativo: {}", sensor_id, tipo, is_active);
}
```

### 3.3 String vs &str

Esta distinção é fundamental em Rust:

| Aspecto | `String` | `&str` |
|---|---|---|
| **Alocação** | Heap (dinâmica) | Referência (emprestada) |
| **Ownership** | Tem dono | Emprestada |
| **Mutabilidade** | Pode crescer/shrink | Imutável |
| **Em structs** | ✅ Comum | Requer lifetimes |

```rust
fn main() {
    let owned: String = "hello".to_string();  // String → owned
    let borrowed: &str = &owned;              // &str → emprestada
    let literal: &str = "world";              // &str → literal (estático)

    // Convertendo
    let s1: String = literal.to_string();
    let s2: String = String::from(literal);
    let bytes: &[u8] = literal.as_bytes();    // &str → &[u8]

    println!("{} {} {:?}", owned, literal, bytes);
}
```

Em smart contracts, campos de structs usam `String` porque a struct é dona do dado:

```rust
pub struct SensorAccount {
    pub sensor_id: String,      // String porque a conta é dona do dado
    pub sensor_type: String,
}
```

## 4. Constantes

Constantes são sempre imutáveis e devem ter tipo explícito:

```rust
/// Tamanho máximo do identificador do sensor
const MAX_SENSOR_ID_LEN: usize = 32;
/// Tamanho máximo da descrição do tipo de sensor
const MAX_SENSOR_TYPE_LEN: usize = 16;
/// Fator de escala para aritmética de ponto fixo
const SCALE: u64 = 1_000_000;
/// Base em basis points (100% = 10_000)
const BPS_BASE: u64 = 10_000;

fn main() {
    println!("Escala: {}, BPS: {}", SCALE, BPS_BASE);
}
```

Diferenças entre `const` e `let`:

- **`const`**: avaliada em tempo de compilação, sempre imutável, tipo obrigatório, escopo global ou local.
- **`let`**: avaliada em tempo de execução, pode ser `mut`, tipo inferido, escopo local.

## 5. Funções

### 5.1 Sintaxe Básica

```rust
/// Soma dois valores u64 e retorna o resultado.
fn somar(a: u64, b: u64) -> u64 {
    a + b   // sem ponto-e-vírgula = expressão de retorno
}

/// Função sem retorno (retorna implicitamente `()`)
fn imprimir_score(nome: &str, score: u64) {
    println!("Score de {}: {}", nome, score);
}

fn main() {
    let total = somar(100, 50);
    imprimir_score("temp-001", total);
}
```

### 5.2 Retornando Result

Em smart contracts, funções retornam `Result<()>` para indicar sucesso ou erro:

```rust
fn validar_sensor_id(sensor_id: &str) -> Result<(), String> {
    if sensor_id.len() > 32 {
        Err("Identificador do sensor excede o tamanho máximo".to_string())
    } else if sensor_id.is_empty() {
        Err("Identificador não pode ser vazio".to_string())
    } else {
        Ok(())
    }
}

fn main() {
    match validar_sensor_id("temp-001") {
        Ok(()) => println!("Sensor válido!"),
        Err(msg) => println!("Erro: {}", msg),
    }
}
```

### 5.3 Visibilidade com `pub`

```rust
// Sem `pub` → privada ao módulo
fn funcao_interna() {}

// Com `pub` → acessível fora do módulo
pub fn funcao_publica() {}
```

Em smart contracts, as instruções (funções que o usuário pode chamar) são `pub fn`:

```rust
pub fn register_sensor(
    ctx: Context<RegisterSensor>,
    sensor_id: String,
    sensor_type: String,
    latitude: i64,
    longitude: i64,
) -> Result<()> {
    // ... implementação
    Ok(())
}
```

## 6. Structs (Estruturas de Dados)

Structs são o equivalente a classes (sem herança) em Rust. São a principal forma de organizar dados.

### 6.1 Definição e Instanciação

```rust
struct SensorAccount {
    owner: String,
    sensor_id: String,
    sensor_type: String,
    latitude: i64,
    longitude: i64,
    score: u64,
    penalty_level: u8,
    is_active: bool,
    registered_at: i64,
}

fn main() {
    let sensor = SensorAccount {
        owner: "Alice".to_string(),
        sensor_id: "temp-001".to_string(),
        sensor_type: "temperature".to_string(),
        latitude: -38_516_700,
        longitude: -386_120_000,
        score: 0,
        penalty_level: 0,
        is_active: true,
        registered_at: 1_700_000_000,
    };

    println!("Sensor: {} ({})", sensor.sensor_id, sensor.sensor_type);
    println!("Localização: {}, {}", sensor.latitude, sensor.longitude);
    println!("Ativo: {}", sensor.is_active);
}
```

### 6.2 Campos Públicos

Em smart contracts, os campos são `pub` para permitir acesso externo:

```rust
pub struct OracleAccount {
    pub authority: String,
    pub brl_usdc_rate: u64,
    pub token_price: u64,
    pub last_updated: i64,
    pub twap_observations: Vec<PriceObservation>,
    pub bump: u8,
}
```

### 6.3 Métodos com `impl`

Você pode adicionar métodos a uma struct usando `impl`:

```rust
struct Sensor {
    sensor_id: String,
    score: u64,
    penalty_level: u8,
    is_active: bool,
}

impl Sensor {
    /// Construtor (convenção: `new`)
    fn new(id: &str) -> Self {
        Sensor {
            sensor_id: id.to_string(),
            score: 0,
            penalty_level: 0,
            is_active: true,
        }
    }

    /// Método que recebe &self (leitura)
    fn esta_banido(&self) -> bool {
        self.penalty_level >= 3
    }

    /// Método que recebe &mut self (escrita)
    fn adicionar_score(&mut self, delta: u64) {
        if self.is_active && !self.esta_banido() {
            self.score += delta;
        }
    }

    /// Aplicar penalidade com slashing
    fn aplicar_penalidade(&mut self, nivel: u8, slash: u64) {
        self.penalty_level = nivel;
        self.score = self.score.saturating_sub(slash);
        if nivel >= 3 {
            self.is_active = false;
        }
    }
}

fn main() {
    let mut s = Sensor::new("temp-001");
    s.adicionar_score(100);
    println!("Score: {}", s.score);         // 100

    s.aplicar_penalidade(1, 30);
    println!("Score após penalidade: {}", s.score); // 70

    s.aplicar_penalidade(3, 0);
    println!("Banido: {}", s.esta_banido()); // true
    println!("Ativo: {}", s.is_active);      // false
}
```

### 6.4 `&self` vs `&mut self`

| Assinatura | Significado | Quando usar |
|---|---|---|
| `&self` | Referência imutável | Apenas leitura |
| `&mut self` | Referência mutável | Modifica o estado |
| `self` | Toma ownership | Consome a struct |

## 7. Enums (Enumerações)

Enums definem um tipo que pode ser uma dentre várias variantes.

### 7.1 Enum Simples

```rust
enum StatusSensor {
    Ativo,
    Inativo,
    Banido,
}

fn descrever_status(status: &StatusSensor) -> &str {
    match status {
        StatusSensor::Ativo => "Operando normalmente",
        StatusSensor::Inativo => "Desativado temporariamente",
        StatusSensor::Banido => "Removido da rede permanentemente",
    }
}

fn main() {
    let status = StatusSensor::Ativo;
    println!("{}", descrever_status(&status));
}
```

### 7.2 Enum com Dados

```rust
enum Erro {
    SensorIdMuitoLongo,
    ScoreOverflow(u64),
    Personalizado(String),
}

fn descrever_erro(e: &Erro) -> String {
    match e {
        Erro::SensorIdMuitoLongo => "ID muito longo".to_string(),
        Erro::ScoreOverflow(valor) => format!("Overflow no score: {}", valor),
        Erro::Personalizado(msg) => msg.clone(),
    }
}

fn main() {
    let e = Erro::ScoreOverflow(999);
    println!("{}", descrever_erro(&e));
}
```

### 7.3 Enums de Erro em Smart Contracts

No framework Anchor, enums de erro seguem um padrão com atributos:

```rust
#[error_code]
pub enum RegistryError {
    #[msg("Identificador do sensor excede o tamanho máximo")]
    SensorIdTooLong,
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

Cada variante representa um erro distinto com uma mensagem legível.

## 8. Result e Option — Tratamento de Erros

Rust não tem exceções. Em vez disso, usa os tipos `Result<T, E>` e `Option<T>`.

### 8.1 Result

```rust
// Result é definido assim na standard library:
// enum Result<T, E> {
//     Ok(T),
//     Err(E),
// }

fn dividir(a: u64, b: u64) -> Result<u64, String> {
    if b == 0 {
        Err("Divisão por zero".to_string())
    } else {
        Ok(a / b)
    }
}

fn main() {
    // Tratando com match
    match dividir(100, 3) {
        Ok(resultado) => println!("100 / 3 = {}", resultado),
        Err(msg) => println!("Erro: {}", msg),
    }

    // Tratando com unwrap (cuidado: panic se for Err)
    let r = dividir(100, 5).unwrap();
    println!("100 / 5 = {}", r);
}
```

### 8.2 O Operador `?`

O `?` propaga erros automaticamente — se a expressão for `Err`, a função retorna imediatamente com esse erro:

```rust
fn calcular_tokens(receita: u64, preco: u64) -> Result<u64, String> {
    if preco == 0 {
        return Err("Preço não pode ser zero".to_string());
    }

    let elegivel = receita
        .checked_mul(950_000)
        .ok_or("Overflow na multiplicação".to_string())?;  // ← propaga se None

    let tokens = elegivel
        .checked_div(preco)
        .ok_or("Overflow na divisão".to_string())?;        // ← propaga se None

    Ok(tokens)
}

fn main() {
    match calcular_tokens(534_440_000, 500_000) {
        Ok(t) => println!("Tokens: {}", t),
        Err(e) => println!("Erro: {}", e),
    }
}
```

### 8.3 Option

```rust
// Option é definido assim:
// enum Option<T> {
//     Some(T),
//     None,
// }

fn buscar_sensor(id: &str) -> Option<String> {
    match id {
        "temp-001" => Some("Sensor de Temperatura".to_string()),
        "umid-002" => Some("Sensor de Umidade".to_string()),
        _ => None,
    }
}

fn main() {
    // Tratando com match
    match buscar_sensor("temp-001") {
        Some(nome) => println!("Encontrado: {}", nome),
        None => println!("Sensor não encontrado"),
    }

    // Tratando com if let
    if let Some(nome) = buscar_sensor("xyz") {
        println!("Encontrado: {}", nome);
    } else {
        println!("Não encontrado");
    }

    // Convertendo Option para Result com ok_or
    let resultado: Result<String, &str> = buscar_sensor("xyz")
        .ok_or("Sensor não existe");
    println!("{:?}", resultado); // Err("Sensor não existe")
}
```

### 8.4 `ok_or` — Ponte entre Option e Result

O método `.ok_or()` converte `Option<T>` em `Result<T, E>`, e é extremamente usado em smart contracts:

```rust
fn main() {
    let a: u64 = 100;
    let b: u64 = 50;

    // checked_add retorna Option<u64>
    // ok_or converte para Result<u64, &str>
    let soma = a.checked_add(b)
        .ok_or("Overflow aritmético");

    println!("{:?}", soma); // Ok(150)

    // Com valor que causaria overflow em u8
    let x: u8 = 250;
    let resultado = x.checked_add(10)
        .ok_or("Overflow aritmético");

    println!("{:?}", resultado); // Err("Overflow aritmético")
}
```

## 9. Ownership, Borrowing e Referências

Este é **o conceito mais importante** de Rust. O sistema de ownership garante segurança de memória sem garbage collector.

### 9.1 Ownership (Posse)

Cada valor em Rust tem exatamente **um dono**. Quando o dono sai do escopo, o valor é descartado.

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;                     // s1 foi MOVIDO para s2
    // println!("{}", s1);           // ❌ ERRO: s1 não é mais válido
    println!("{}", s2);              // ✅ OK
}
```

### 9.2 Borrowing (Empréstimo)

Para usar um valor sem tomar posse, usamos **referências**:

```rust
fn imprimir_sensor(id: &String) {       // empréstimo imutável
    println!("Sensor: {}", id);
}

fn ativar_sensor(ativo: &mut bool) {    // empréstimo mutável
    *ativo = true;                      // desreferencia para modificar
}

fn main() {
    let sensor_id = String::from("temp-001");
    imprimir_sensor(&sensor_id);        // empresta sem mover
    println!("Ainda tenho: {}", sensor_id); // ✅ sensor_id ainda é válido

    let mut is_active = false;
    ativar_sensor(&mut is_active);
    println!("Ativo: {}", is_active);   // true
}
```

### 9.3 Regras de Borrowing

1. Você pode ter **múltiplas referências imutáveis** (`&T`) ao mesmo tempo.
2. Você pode ter **apenas uma referência mutável** (`&mut T`) por vez.
3. Não pode ter referências imutáveis e mutáveis **ao mesmo tempo**.

```rust
fn main() {
    let mut score = 100u64;

    // Múltiplas referências imutáveis: OK
    let r1 = &score;
    let r2 = &score;
    println!("{} {}", r1, r2);

    // Uma referência mutável: OK (r1 e r2 já não são usadas)
    let r3 = &mut score;
    *r3 += 50;
    println!("{}", r3); // 150
}
```

### 9.4 Borrowing em Smart Contracts

Em smart contracts Solana/Anchor, o padrão `&mut ctx.accounts.conta` é a forma de obter uma referência mutável a uma conta para modificá-la:

```rust
// Referência mutável → podemos escrever na conta
let sensor = &mut ctx.accounts.sensor_account;
sensor.score = sensor.score.checked_add(delta_score)
    .ok_or(RegistryError::Overflow)?;

// Referência imutável → apenas leitura
let oracle = &ctx.accounts.oracle_account;
let rate = oracle.brl_usdc_rate;
```

## 10. Vetores (Vec)

`Vec<T>` é uma coleção dinâmica, como `ArrayList` em Java ou listas em Python.

```rust
fn main() {
    // Criando um vetor
    let mut observacoes: Vec<u64> = Vec::new();

    // Adicionando elementos
    observacoes.push(172_400);
    observacoes.push(175_000);
    observacoes.push(170_800);

    // Acessando
    println!("Primeira: {}", observacoes[0]);
    println!("Tamanho: {}", observacoes.len());

    // Removendo o primeiro elemento
    if observacoes.len() >= 3 {
        observacoes.remove(0);
    }
    println!("Após remove: {:?}", observacoes);

    // Iterando
    let mut soma: u128 = 0;
    for obs in &observacoes {
        soma += *obs as u128;
    }
    println!("Soma: {}", soma);
}
```

### 10.1 Vec com Structs

```rust
#[derive(Debug, Clone)]
struct PriceObservation {
    price: u64,
    duration: u64,
    timestamp: i64,
}

const MAX_OBSERVATIONS: usize = 24;

fn main() {
    let mut observations: Vec<PriceObservation> = Vec::new();

    // Adicionando observações
    observations.push(PriceObservation {
        price: 500_000,
        duration: 3600,
        timestamp: 1_700_000_000,
    });
    observations.push(PriceObservation {
        price: 510_000,
        duration: 3600,
        timestamp: 1_700_003_600,
    });

    // Limitando o tamanho (janela deslizante)
    if observations.len() >= MAX_OBSERVATIONS {
        observations.remove(0);
    }

    // Calculando TWAP (Time-Weighted Average Price)
    let mut weighted_sum: u128 = 0;
    let mut total_duration: u128 = 0;
    for obs in &observations {
        weighted_sum += (obs.price as u128) * (obs.duration as u128);
        total_duration += obs.duration as u128;
    }

    if total_duration > 0 {
        let twap = (weighted_sum / total_duration) as u64;
        println!("TWAP: {}", twap);
    }
}
```

## 11. Módulos e Organização de Código

### 11.1 Módulos Inline

```rust
mod registry {
    pub struct Sensor {
        pub id: String,
        pub score: u64,
    }

    impl Sensor {
        pub fn new(id: &str) -> Self {
            Sensor {
                id: id.to_string(),
                score: 0,
            }
        }
    }

    // Função privada (sem pub)
    fn validar_interno(id: &str) -> bool {
        id.len() <= 32
    }

    // Função pública que usa a privada
    pub fn criar_sensor(id: &str) -> Result<Sensor, String> {
        if validar_interno(id) {
            Ok(Sensor::new(id))
        } else {
            Err("ID muito longo".to_string())
        }
    }
}

fn main() {
    let sensor = registry::criar_sensor("temp-001").unwrap();
    println!("Sensor: {} (score: {})", sensor.id, sensor.score);
}
```

### 11.2 `use` e `super`

```rust
mod protocolo {
    pub const SCALE: u64 = 1_000_000;

    pub mod oracle {
        use super::SCALE;   // acessa constante do módulo pai

        pub fn converter_brl_para_usdc(brl: u64, taxa: u64) -> u64 {
            ((brl as u128) * (taxa as u128) / (SCALE as u128)) as u64
        }
    }

    pub mod treasury {
        use super::SCALE;

        pub fn calcular_partição(receita: u64, bps: u64) -> u64 {
            ((receita as u128) * (bps as u128) / (10_000u128)) as u64
        }
    }
}

fn main() {
    use protocolo::oracle;
    use protocolo::treasury;

    let usdc = oracle::converter_brl_para_usdc(5_000_000_000, 172_400);
    println!("5000 BRL = {} USDC (×10⁶)", usdc);

    let operacional = treasury::calcular_partição(usdc, 4000);
    println!("Operação (40%): {}", operacional);
}
```

### 11.3 `use crate::*` e `use super::*`

Em smart contracts Anchor, é muito comum ver:

```rust
#[program]
pub mod criptosensor_registry {
    use super::*;   // importa tudo que está acima no arquivo (structs, consts, etc.)

    pub fn register_sensor(ctx: Context<RegisterSensor>, sensor_id: String) -> Result<()> {
        // pode acessar MAX_SENSOR_ID_LEN, RegistryError, etc.
        Ok(())
    }
}
```

## 12. Traits e Derive

Traits são como **interfaces** em outras linguagens — definem comportamento compartilhado.

### 12.1 Traits Básicas

```rust
trait Pontuavel {
    fn score(&self) -> u64;
    fn esta_ativo(&self) -> bool;

    // Método com implementação padrão
    fn pode_receber_tokens(&self) -> bool {
        self.esta_ativo() && self.score() > 0
    }
}

struct Sensor {
    id: String,
    score: u64,
    is_active: bool,
}

struct EdgeNode {
    id: String,
    score: u64,
    is_active: bool,
}

impl Pontuavel for Sensor {
    fn score(&self) -> u64 { self.score }
    fn esta_ativo(&self) -> bool { self.is_active }
}

impl Pontuavel for EdgeNode {
    fn score(&self) -> u64 { self.score }
    fn esta_ativo(&self) -> bool { self.is_active }
}

fn imprimir_elegibilidade(item: &dyn Pontuavel) {
    println!("Score: {} | Pode receber: {}", item.score(), item.pode_receber_tokens());
}

fn main() {
    let sensor = Sensor { id: "temp-001".to_string(), score: 120, is_active: true };
    let node = EdgeNode { id: "edge-A".to_string(), score: 0, is_active: true };

    imprimir_elegibilidade(&sensor);  // Score: 120 | Pode receber: true
    imprimir_elegibilidade(&node);    // Score: 0 | Pode receber: false
}
```

### 12.2 Derive — Implementação Automática de Traits

O atributo `#[derive(...)]` gera implementações automaticamente:

```rust
#[derive(Debug, Clone, PartialEq)]
struct PriceObservation {
    price: u64,
    duration: u64,
    timestamp: i64,
}

fn main() {
    let obs1 = PriceObservation { price: 500_000, duration: 3600, timestamp: 1_700_000_000 };
    let obs2 = obs1.clone();             // Clone
    println!("{:?}", obs1);              // Debug
    println!("Iguais: {}", obs1 == obs2); // PartialEq
}
```

Traits comuns usadas com `derive`:

| Trait | O que faz |
|---|---|
| `Debug` | Permite `{:?}` no println |
| `Clone` | Permite `.clone()` |
| `Copy` | Cópia implícita (tipos simples) |
| `PartialEq` | Permite `==` e `!=` |
| `Default` | Valores padrão com `Default::default()` |

### 12.3 Derive em Smart Contracts

No Anchor, structs de dados usam derives específicos:

```rust
// Structs de dados (contas on-chain)
#[account]
#[derive(InitSpace)]
pub struct SensorAccount {
    pub owner: Pubkey,
    #[max_len(32)]
    pub sensor_id: String,
    pub score: u64,
    pub bump: u8,
}

// Structs auxiliares (dentro de contas)
#[derive(AnchorSerialize, AnchorDeserialize, Clone, InitSpace)]
pub struct PriceObservation {
    pub price: u64,
    pub duration: u64,
    pub timestamp: i64,
}
```

## 13. Generics (Tipos Genéricos)

Generics permitem escrever código que funciona com múltiplos tipos.

### 13.1 Funções Genéricas

```rust
use std::fmt::Display;

fn imprimir_valor<T: Display>(nome: &str, valor: T) {
    println!("{}: {}", nome, valor);
}

fn maior<T: PartialOrd>(a: T, b: T) -> T {
    if a >= b { a } else { b }
}

fn main() {
    imprimir_valor("Score", 150u64);
    imprimir_valor("Sensor", "temp-001");
    println!("Maior: {}", maior(100u64, 200u64));
}
```

### 13.2 Structs Genéricas

```rust
#[derive(Debug)]
struct Resultado<T> {
    valor: T,
    epoch: u64,
    timestamp: i64,
}

fn main() {
    let r1: Resultado<u64> = Resultado { valor: 862_000_000, epoch: 10, timestamp: 1_700_000_000 };
    let r2: Resultado<String> = Resultado { valor: "sucesso".to_string(), epoch: 10, timestamp: 1_700_000_000 };

    println!("{:?}", r1);
    println!("{:?}", r2);
}
```

### 13.3 Generics em Smart Contracts

O Anchor usa generics extensivamente. Os mais comuns:

```rust
// Context<T> — contexto da instrução parametrizado pela struct de contas
pub fn register_sensor(ctx: Context<RegisterSensor>) -> Result<()> { ... }

// Account<'info, T> — conta tipada
pub sensor_account: Account<'info, SensorAccount>,

// Program<'info, T> — referência a um programa
pub system_program: Program<'info, System>,
pub token_program: Program<'info, Token>,
```

## 14. Lifetimes (Tempos de Vida)

Lifetimes garantem que referências nunca apontem para dados que já foram liberados.

### 14.1 Conceito Básico

```rust
// O lifetime 'a diz: "a referência retornada vive pelo menos
// tanto quanto as referências de entrada"
fn mais_longo<'a>(s1: &'a str, s2: &'a str) -> &'a str {
    if s1.len() > s2.len() { s1 } else { s2 }
}

fn main() {
    let s1 = String::from("temperatura");
    let resultado;
    {
        let s2 = String::from("umidade");
        resultado = mais_longo(&s1, &s2);
        println!("Mais longo: {}", resultado);  // OK: s2 ainda existe aqui
    }
    // Não podemos usar `resultado` aqui se ele referenciasse s2
}
```

### 14.2 Lifetimes em Structs

```rust
// A struct contém uma referência → precisa de lifetime
struct ConfigView<'a> {
    nome: &'a str,
    versao: &'a str,
}

fn main() {
    let nome = String::from("CriptoSensor");
    let versao = String::from("1.0.0");

    let view = ConfigView {
        nome: &nome,
        versao: &versao,
    };

    println!("{} v{}", view.nome, view.versao);
}
```

### 14.3 O Lifetime `'info` em Smart Contracts

No Anchor, `'info` é o lifetime das referências de conta da Solana. Cada struct de contexto o utiliza:

```rust
#[derive(Accounts)]
pub struct RegisterSensor<'info> {
    #[account(mut)]
    pub owner: Signer<'info>,
    #[account(init, payer = owner, space = 8 + SensorAccount::INIT_SPACE,
        seeds = [b"sensor", owner.key().as_ref(), sensor_id.as_bytes()],
        bump
    )]
    pub sensor_account: Account<'info, SensorAccount>,
    pub system_program: Program<'info, System>,
}
```

O `'info` indica que todas as referências de conta vivem durante a execução da instrução.

## 15. Type Casting e Aritmética Segura

### 15.1 Casting com `as`

```rust
fn main() {
    let a: u64 = 534_440_000;
    let b: u64 = 951_230;

    // Promove para u128 para evitar overflow no cálculo intermediário
    let resultado = (a as u128) * (b as u128) / (1_000_000u128);
    let final_u64 = resultado as u64;  // volta para u64

    println!("Resultado: {}", final_u64);
}
```

**Cuidado:** casting com `as` pode truncar valores silenciosamente! Use sempre com cuidado:

```rust
fn main() {
    let grande: u128 = 999_999_999_999_999;
    let truncado = grande as u64;       // OK (cabe em u64)

    let muito_grande: u128 = u128::MAX;
    let perigoso = muito_grande as u64; // ⚠️ Truncado silenciosamente!

    println!("Truncado: {}", truncado);
    println!("Perigoso: {}", perigoso);
}
```

### 15.2 Aritmética Segura com `checked_*`

Em smart contracts, **nunca** use operações aritméticas comuns (`+`, `-`, `*`, `/`) com valores de usuário. Use as variantes `checked_*`:

```rust
fn main() {
    let score: u64 = 100;
    let delta: u64 = 50;

    // checked_add: retorna None se houver overflow
    let novo_score = score.checked_add(delta);
    println!("checked_add: {:?}", novo_score);  // Some(150)

    // checked_mul: retorna None se houver overflow
    let receita: u64 = 534_440_000;
    let bps: u64 = 4000;
    let operacional = receita.checked_mul(bps);
    println!("checked_mul: {:?}", operacional);  // Some(2_137_760_000_000)

    // checked_div: retorna None se divisor for zero
    let por_epoch = receita.checked_div(0);
    println!("checked_div por 0: {:?}", por_epoch);  // None

    // checked_sub: retorna None se resultado for negativo (para unsigned)
    let penalidade: u64 = 200;
    let resultado = score.checked_sub(penalidade);
    println!("checked_sub: {:?}", resultado);  // None (100 - 200 < 0)
}
```

### 15.3 `saturating_sub` — Subtração Segura

Quando você quer que o resultado seja no mínimo zero (sem panic nem None):

```rust
fn main() {
    let score: u64 = 100;

    // saturating_sub: resultado mínimo = 0 (não estoura para negativo)
    let resultado = score.saturating_sub(200);
    println!("saturating_sub(100, 200) = {}", resultado);  // 0

    let resultado2 = score.saturating_sub(30);
    println!("saturating_sub(100, 30) = {}", resultado2);  // 70
}
```

### 15.4 Method Chaining com `checked_*` e `ok_or`

O padrão mais comum em smart contracts combina aritmética segura com tratamento de erros:

```rust
fn calcular_partição(receita: u64, bps: u64) -> Result<u64, String> {
    let resultado = receita
        .checked_mul(bps)                               // Option<u64>
        .ok_or("Overflow na multiplicação".to_string())? // Result<u64, String>
        .checked_div(10_000)                             // Option<u64>
        .ok_or("Divisão por zero".to_string())?;         // Result<u64, String>
    Ok(resultado)
}

fn pipeline_completo(receita: u64, taxa: u64, preco: u64) -> Result<u64, String> {
    // Encadeamento completo: receita × taxa / SCALE / preço
    let tokens = (receita as u128)
        .checked_mul(taxa as u128)
        .ok_or("Overflow em receita × taxa".to_string())?
        .checked_div(1_000_000)
        .ok_or("Divisão por escala falhou".to_string())?
        .checked_div(preco as u128)
        .ok_or("Divisão por preço falhou".to_string())?;

    Ok(tokens as u64)
}

fn main() {
    match calcular_partição(862_000_000, 5000) {
        Ok(v) => println!("Incentivo (50%): {} USDC", v),
        Err(e) => println!("Erro: {}", e),
    }

    match pipeline_completo(534_440_000, 951_230, 500_000) {
        Ok(t) => println!("Tokens: {}", t),
        Err(e) => println!("Erro: {}", e),
    }
}
```

## 16. Macros

Macros são identificadas pelo `!` e são expandidas em tempo de compilação.

### 16.1 Macros da Standard Library

```rust
fn main() {
    // println! — imprime com formatação
    let sensor_id = "temp-001";
    let score = 150u64;
    println!("Sensor {} | Score: {}", sensor_id, score);

    // format! — retorna String formatada (sem imprimir)
    let msg = format!("Score do sensor {} atualizado: +{} → total {}", sensor_id, 50, score);
    println!("{}", msg);

    // vec! — cria Vec com valores iniciais
    let precos: Vec<u64> = vec![500_000, 510_000, 505_000];
    println!("{:?}", precos);

    // assert! e assert_eq! — verificações (panic se falhar)
    assert!(score > 0, "Score deve ser positivo");
    assert_eq!(2 + 2, 4, "Matemática quebrou");
}
```

### 16.2 Macros do Framework Anchor

Em smart contracts Anchor, macros especiais são essenciais:

```rust
// declare_id! — define o endereço do programa
declare_id!("REGxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx");

// msg! — registra mensagem nos logs da transação (equivale a println! on-chain)
msg!("Sensor registrado: {}", sensor.sensor_id);

// require! — aborta a transação se a condição for falsa
require!(sensor_id.len() <= MAX_SENSOR_ID_LEN, RegistryError::SensorIdTooLong);
require!(amount > 0, TreasuryError::ZeroAmount);

// emit! — emite um evento on-chain que pode ser capturado por indexadores
emit!(PaymentEvent {
    client: ctx.accounts.client.key(),
    amount,
    order_id,
    epoch: treasury.current_epoch,
    timestamp: Clock::get()?.unix_timestamp,
});
```

## 17. Atributos

Atributos (`#[...]`) modificam o comportamento de itens no código.

### 17.1 Atributos Comuns

```rust
// #[derive] — implementa traits automaticamente
#[derive(Debug, Clone, PartialEq)]
struct Ponto {
    x: i64,
    y: i64,
}

// #[allow] — suprime avisos do compilador
#[allow(dead_code)]
fn funcao_nao_usada() {}

// #[cfg] — compilação condicional
#[cfg(test)]
mod tests {
    #[test]
    fn test_soma() {
        assert_eq!(2 + 2, 4);
    }
}

fn main() {
    let p = Ponto { x: 10, y: 20 };
    println!("{:?}", p);
}
```

### 17.2 Atributos Anchor para Smart Contracts

```rust
// #[program] — marca o módulo como programa Solana
#[program]
pub mod criptosensor_registry {
    // instruções do programa
}

// #[account] — marca struct como conta on-chain (serialização automática)
#[account]
#[derive(InitSpace)]
pub struct SensorAccount {
    pub owner: Pubkey,
    #[max_len(32)]       // #[max_len] — tamanho máximo para cálculo de espaço
    pub sensor_id: String,
    pub score: u64,
    pub bump: u8,
}

// #[derive(Accounts)] — gera validação automática de contas
#[derive(Accounts)]
#[instruction(sensor_id: String)]   // acessa argumentos da instrução
pub struct RegisterSensor<'info> {
    #[account(mut)]      // conta mutável (será modificada)
    pub owner: Signer<'info>,
    #[account(
        init,            // será inicializada (criada)
        payer = owner,   // quem paga o aluguel
        space = 8 + SensorAccount::INIT_SPACE,  // tamanho da conta
        seeds = [b"sensor", owner.key().as_ref(), sensor_id.as_bytes()],
        bump             // PDA bump seed
    )]
    pub sensor_account: Account<'info, SensorAccount>,
    pub system_program: Program<'info, System>,
}

// #[error_code] — define erros customizados
#[error_code]
pub enum RegistryError {
    #[msg("Sensor está inativo")]
    SensorInactive,
}

// #[event] — define evento emitido pela instrução
#[event]
pub struct PaymentEvent {
    pub client: Pubkey,
    pub amount: u64,
    pub timestamp: i64,
}
```

## 18. Pattern Matching e Controle de Fluxo

### 18.1 `match`

```rust
fn classificar_penalidade(nivel: u8) -> &'static str {
    match nivel {
        0 => "Sem penalidade",
        1 => "Perda parcial de score",
        2 => "Slashing severo",
        3 => "Banimento permanente",
        _ => "Nível desconhecido",   // _ = padrão (wildcard)
    }
}

fn main() {
    for nivel in 0..=4 {
        println!("Nível {}: {}", nivel, classificar_penalidade(nivel));
    }
}
```

### 18.2 `if let` — Pattern Matching Simplificado

```rust
fn main() {
    let resultado: Option<u64> = Some(862_000_000);

    // Ao invés de match completo...
    if let Some(valor) = resultado {
        println!("Receita: {} USDC", valor / 1_000_000);
    }

    // Equivalente a:
    match resultado {
        Some(valor) => println!("Receita: {} USDC", valor / 1_000_000),
        None => {},
    }
}
```

### 18.3 Expressões `if-else` como Valor

Em Rust, `if-else` é uma **expressão** que retorna valor:

```rust
fn main() {
    let epoch: u64 = 0;
    let revenue: u64 = 500_000_000;
    let ema_anterior: u64 = 0;

    // if-else como expressão
    let new_ema = if epoch == 0 {
        revenue
    } else {
        let alpha = 333_333u64;
        let alpha_r = ((alpha as u128) * (revenue as u128) / 1_000_000) as u64;
        let complement = ((666_667u128) * (ema_anterior as u128) / 1_000_000) as u64;
        alpha_r + complement
    };

    println!("EMA: {}", new_ema);
}
```

### 18.4 `require!` como Guard Clause

Em smart contracts, `require!` funciona como guard clause que aborta a transação:

```rust
// Cada require! verifica uma condição antes de prosseguir
// Se falhar, retorna erro imediatamente

// require!(condição, Erro);

// Equivalente a:
// if !condição { return Err(Erro.into()); }

// Exemplos do CriptoSensor:
// require!(sensor_id.len() <= MAX_SENSOR_ID_LEN, RegistryError::SensorIdTooLong);
// require!(amount > 0, TreasuryError::ZeroAmount);
// require!(sensor.is_active, RegistryError::SensorInactive);
// require!(penalty_level >= 1 && penalty_level <= 3, RegistryError::InvalidPenalty);
```

## 19. Slices e Referências a Arrays

Slices (`&[T]`) são referências a uma porção contígua de memória.

```rust
fn soma_slice(valores: &[u64]) -> u128 {
    let mut total: u128 = 0;
    for v in valores {
        total += *v as u128;
    }
    total
}

fn main() {
    let precos: Vec<u64> = vec![500_000, 510_000, 505_000, 515_000];

    // Slice de todo o vetor
    let total = soma_slice(&precos);
    println!("Total: {}", total);

    // Slice parcial
    let ultimos_dois = &precos[2..];
    println!("Últimos dois: {:?}", ultimos_dois);

    // Byte slices — muito usados em seeds de PDA
    let seed: &[u8] = b"sensor";               // byte string literal
    let id_bytes: &[u8] = "temp-001".as_bytes(); // String → bytes

    println!("Seed: {:?}", seed);
    println!("ID bytes: {:?}", id_bytes);

    // Slice de slices (usado em signer_seeds)
    let bump: u8 = 255;
    let seeds: &[&[u8]] = &[b"emission_config".as_ref(), &[bump]];
    println!("Seeds: {:?}", seeds);
}
```

## 20. Juntando Tudo — Exemplo Completo

Vamos construir um mini-protocolo que usa todos os conceitos aprendidos:

```rust
// --- Constantes ---
const SCALE: u64 = 1_000_000;
const MAX_ID_LEN: usize = 32;
const SENSOR_POOL_BPS: u64 = 7000;
const BPS_BASE: u64 = 10_000;

// --- Enums ---
#[derive(Debug, PartialEq)]
enum ProtoError {
    IdMuitoLongo,
    Overflow,
    SensorInativo,
    ScoreZero,
}

// --- Structs ---
#[derive(Debug, Clone)]
struct Sensor {
    id: String,
    score: u64,
    is_active: bool,
    penalty_level: u8,
}

#[derive(Debug)]
struct EpochResult {
    epoch: u64,
    total_tokens: u64,
    distribuicoes: Vec<(String, u64)>,
}

// --- Traits ---
trait Participante {
    fn id(&self) -> &str;
    fn score(&self) -> u64;
    fn elegivel(&self) -> bool;
}

impl Participante for Sensor {
    fn id(&self) -> &str { &self.id }
    fn score(&self) -> u64 { self.score }
    fn elegivel(&self) -> bool { self.is_active && self.penalty_level < 3 }
}

// --- Implementação ---
impl Sensor {
    fn new(id: &str) -> Result<Self, ProtoError> {
        if id.len() > MAX_ID_LEN {
            return Err(ProtoError::IdMuitoLongo);
        }
        Ok(Sensor {
            id: id.to_string(),
            score: 0,
            is_active: true,
            penalty_level: 0,
        })
    }

    fn adicionar_score(&mut self, delta: u64) -> Result<(), ProtoError> {
        if !self.is_active {
            return Err(ProtoError::SensorInativo);
        }
        self.score = self.score.checked_add(delta)
            .ok_or(ProtoError::Overflow)?;
        Ok(())
    }

    fn aplicar_penalidade(&mut self, nivel: u8, slash: u64) {
        self.penalty_level = nivel;
        self.score = self.score.saturating_sub(slash);
        if nivel >= 3 {
            self.is_active = false;
        }
    }
}

// --- Funções do Protocolo ---

/// Calcula o fator de decaimento e^(-λ·t) via série de Taylor
fn approximate_exp_neg(x: u64) -> Result<u64, ProtoError> {
    if x == 0 { return Ok(SCALE); }

    let x_128 = x as u128;
    let scale_128 = SCALE as u128;

    let x2 = x_128.checked_mul(x_128).ok_or(ProtoError::Overflow)?
        .checked_div(scale_128).ok_or(ProtoError::Overflow)?;
    let x3 = x2.checked_mul(x_128).ok_or(ProtoError::Overflow)?
        .checked_div(scale_128).ok_or(ProtoError::Overflow)?;

    let result = scale_128
        .checked_sub(x_128).unwrap_or(0)
        .checked_add(x2 / 2).ok_or(ProtoError::Overflow)?
        .checked_sub(x3 / 6).unwrap_or(0);

    Ok(result.min(scale_128) as u64)
}

/// Distribui tokens proporcionalmente ao score dos sensores
fn distribuir_tokens(
    sensores: &[Sensor],
    tokens_total: u64,
) -> Result<EpochResult, ProtoError> {
    // Filtra sensores elegíveis
    let elegiveis: Vec<&Sensor> = sensores.iter()
        .filter(|s| s.elegivel())
        .collect();

    let score_total: u64 = elegiveis.iter()
        .map(|s| s.score())
        .sum();

    if score_total == 0 {
        return Err(ProtoError::ScoreZero);
    }

    // Pool de sensores = 70%
    let sensor_pool = (tokens_total as u128)
        .checked_mul(SENSOR_POOL_BPS as u128).ok_or(ProtoError::Overflow)?
        .checked_div(BPS_BASE as u128).ok_or(ProtoError::Overflow)? as u64;

    let mut distribuicoes = Vec::new();
    for sensor in &elegiveis {
        let tokens = (sensor_pool as u128)
            .checked_mul(sensor.score() as u128).ok_or(ProtoError::Overflow)?
            .checked_div(score_total as u128).ok_or(ProtoError::Overflow)? as u64;
        distribuicoes.push((sensor.id().to_string(), tokens));
    }

    Ok(EpochResult {
        epoch: 10,
        total_tokens: tokens_total,
        distribuicoes,
    })
}

// --- Main ---
fn main() {
    println!("═══ Mini-Protocolo CriptoSensor ═══\n");

    // Criar sensores
    let mut sensores = vec![
        Sensor::new("temp-001").unwrap(),
        Sensor::new("umid-002").unwrap(),
        Sensor::new("press-003").unwrap(),
        Sensor::new("spam-004").unwrap(),
    ];

    // Adicionar scores
    sensores[0].adicionar_score(120).unwrap();
    sensores[1].adicionar_score(95).unwrap();
    sensores[2].adicionar_score(60).unwrap();
    sensores[3].adicionar_score(200).unwrap();

    // Banir o spammer
    sensores[3].aplicar_penalidade(3, 200);

    println!("Sensores:");
    for s in &sensores {
        println!("  {} | Score: {} | Ativo: {} | Elegível: {}",
            s.id, s.score, s.is_active, s.elegivel());
    }

    // Calcular decaimento para epoch 10
    let lambda = 5_000u64;  // 0.005 × 10^6
    let epoch = 10u64;
    let x = lambda * epoch / SCALE;
    // x será 0 devido à divisão inteira — usamos o cálculo correto:
    let x_correto = (lambda as u128 * epoch as u128 / (SCALE as u128 / 1000)) as u64;
    // Simplificando: x = 5000 * 10 = 50_000
    let x_final = lambda.checked_mul(epoch).unwrap();

    let decay = approximate_exp_neg(x_final).unwrap();
    println!("\nDecaimento e^(-λ·{}) = {}/{} ≈ {:.6}",
        epoch, decay, SCALE, decay as f64 / SCALE as f64);

    // Tokens do epoch (simulação)
    let incentive_share: u64 = 534_440_000;
    let eligible = (incentive_share as u128 * decay as u128 / SCALE as u128) as u64;
    let token_price: u64 = 500_000;
    let tokens = (eligible as u128 * SCALE as u128 / token_price as u128) as u64;

    println!("Incentive share: {} USDC (×10⁶)", incentive_share);
    println!("Receita elegível: {} USDC (×10⁶)", eligible);
    println!("Tokens emitidos: {} (×10⁶)", tokens);

    // Distribuir
    match distribuir_tokens(&sensores, tokens) {
        Ok(result) => {
            println!("\n═══ Distribuição Epoch {} ═══", result.epoch);
            println!("Pool sensores (70%): {}", tokens * 7 / 10);
            for (id, qtd) in &result.distribuicoes {
                println!("  {} → {} tokens (×10⁶)", id, qtd);
            }
        }
        Err(e) => println!("Erro: {:?}", e),
    }
}
```

## 21. Próximos Passos

Com estes conceitos dominados, você está preparado para:

1. **Ler e entender smart contracts em Rust/Anchor** — todos os padrões fundamentais foram cobertos.
2. **Seguir o tutorial avançado** — [CriptoSensor na Solana: Smart Contracts em Rust com USDC](/projetos/2027/02/14/criptosensor-smartcontract-solana-usdc-tutorial/) usa exatamente os conceitos ensinados aqui.
3. **Explorar o ecossistema** — [The Rust Book](https://doc.rust-lang.org/book/) é a referência oficial e gratuita.

### Resumo dos Conceitos

| Conceito | Seção | Uso em Smart Contracts |
|---|---|---|
| Variáveis e `mut` | 2 | `let sensor = &mut ctx.accounts...` |
| Tipos primitivos | 3 | `u8`, `u64`, `i64`, `u128`, `bool`, `String` |
| Constantes | 4 | `const SCALE: u64 = 1_000_000` |
| Funções e `pub` | 5 | Instruções do programa |
| Structs | 6 | Contas on-chain (`SensorAccount`, etc.) |
| Enums | 7 | Erros customizados (`RegistryError`, etc.) |
| Result e `?` | 8 | Retorno de instruções, `.ok_or()` |
| Ownership/Borrowing | 9 | `&ctx.accounts` vs `&mut ctx.accounts` |
| Vetores | 10 | `Vec<PriceObservation>` no oráculo |
| Módulos | 11 | `mod criptosensor_registry { use super::* }` |
| Traits e Derive | 12 | `#[derive(InitSpace)]`, `AnchorSerialize` |
| Generics | 13 | `Context<T>`, `Account<'info, T>` |
| Lifetimes | 14 | `RegisterSensor<'info>` |
| Type Casting | 15 | `as u128` para cálculos intermediários |
| Aritmética segura | 15 | `checked_add`, `saturating_sub` |
| Macros | 16 | `msg!`, `require!`, `emit!` |
| Atributos | 17 | `#[account]`, `#[program]`, `#[derive(Accounts)]` |
| Pattern Matching | 18 | `match`, `if let`, guard clauses |
| Slices | 19 | Seeds de PDA, signer_seeds |

Bons estudos e bom código! 🦀
