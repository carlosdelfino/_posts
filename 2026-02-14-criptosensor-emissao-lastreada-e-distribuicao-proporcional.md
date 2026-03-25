---
title: "CriptoSensor: Emissão Lastreada e Distribuição Proporcional — Algoritmos e Código"
tags: [blockchain, tokenomics, iot, sensores, criptosensor, algoritmos, python]
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

Este artigo detalha os algoritmos matemáticos por trás do modelo de **emissão lastreada** e **distribuição proporcional** do [CriptoSensor](https://rapport.tec.br/projetos/criptosensor/), um protocolo que remunera sensores IoT com tokens cripto vinculados a receita real. Toda a lógica descrita aqui está alinhada com o projeto original disponível em [rapport.tec.br/projetos/criptosensor/](https://rapport.tec.br/projetos/criptosensor/).

<!--more-->

## 1. Visão Geral do Modelo

O [CriptoSensor](https://rapport.tec.br/projetos/criptosensor/) resolve um problema clássico de redes de sensores: **como remunerar nós que produzem dados úteis sem gerar inflação descontrolada de tokens?**

A resposta está em dois pilares:

- **Emissão lastreada** — tokens só são cunhados proporcionalmente à receita líquida confirmada.
- **Distribuição por score** — cada sensor recebe tokens na exata proporção da sua contribuição verificada.

## 2. Emissão Lastreada — O Algoritmo

### 2.1 Média Móvel da Receita

Para evitar picos de emissão causados por receitas pontuais, o protocolo suaviza o faturamento com uma **Média Móvel Exponencial (EMA)**:

$$
\text{EMA}_t = \alpha \cdot R_t + (1 - \alpha) \cdot \text{EMA}_{t-1}
$$

Onde:

- $R_t$ é a receita líquida confirmada no epoch $t$
- $\alpha = \frac{2}{N + 1}$ é o fator de suavização ($N$ = número de epochs na janela)

### 2.2 Curva de Emissão Decrescente

Para tornar o token mais escasso ao longo do tempo, aplica-se um **fator de decaimento** inspirado em modelos de halving suave:

$$
D(t) = e^{-\lambda \cdot t}
$$

Onde $\lambda > 0$ controla a velocidade do decaimento e $t$ é o índice do epoch (contado desde a gênese da rede).

### 2.3 Receita Elegível e Tokens do Epoch

A cada epoch o algoritmo calcula:

$$
\text{ReceitaElegível}_t = \text{EMA}_t \times \tau \times D(t)
$$

$$
\text{TokensDoEpoch}_t = \frac{\text{ReceitaElegível}_t}{P_{\text{ref}}}
$$

Onde:

- $\tau$ é a **taxa de incentivo** (fração da receita destinada a tokens, por exemplo 0,20 para 20%)
- $P_{\text{ref}}$ é o **preço de referência** do token, obtido via oráculo on-chain ou TWAP (Time-Weighted Average Price)

Se ainda não existir mercado secundário, $P_{\text{ref}}$ é ancorado por uma política interna. Por exemplo: **1 token = direito a consumir $X$ unidades de dado**. Essa âncora garante que a emissão tenha lastro funcional mesmo antes de haver liquidez.

### 2.4 Código Python — Emissão Lastreada

```python
import math

def calc_ema(receitas: list[float], n_janela: int) -> list[float]:
    """Calcula a Média Móvel Exponencial (EMA) sobre uma série de receitas."""
    alpha = 2.0 / (n_janela + 1)
    ema = [receitas[0]]
    for r in receitas[1:]:
        ema.append(alpha * r + (1 - alpha) * ema[-1])
    return ema


def fator_decaimento(epoch: int, lambd: float = 0.005) -> float:
    """Retorna o fator de decaimento D(t) = e^(-λ·t)."""
    return math.exp(-lambd * epoch)


def tokens_do_epoch(
    ema_t: float,
    taxa_incentivo: float,
    preco_ref: float,
    epoch: int,
    lambd: float = 0.005,
) -> float:
    """
    Calcula quantos tokens são emitidos em um epoch.

    ReceitaElegível = EMA_t × taxa_incentivo × D(t)
    TokensDoEpoch   = ReceitaElegível / PreçoDeReferência
    """
    d_t = fator_decaimento(epoch, lambd)
    receita_elegivel = ema_t * taxa_incentivo * d_t
    return receita_elegivel / preco_ref


# --- Exemplo numérico ---
receitas_confirmadas = [
    1000, 1200, 900, 1500, 1100,
    1300, 1400, 1250, 1600, 1350,
]

N_JANELA = 5          # janela da EMA em epochs
TAXA_INCENTIVO = 0.20 # 20 % da receita vai para tokens
PRECO_REF = 0.50      # 1 token = 0,50 unidade monetária
LAMBDA = 0.005        # decaimento suave

emas = calc_ema(receitas_confirmadas, N_JANELA)

print(f"{'Epoch':>5} {'Receita':>10} {'EMA':>10} {'D(t)':>8} {'Tokens':>10}")
print("-" * 48)
for t, (r, e) in enumerate(zip(receitas_confirmadas, emas)):
    tokens = tokens_do_epoch(e, TAXA_INCENTIVO, PRECO_REF, t, LAMBDA)
    d_t = fator_decaimento(t, LAMBDA)
    print(f"{t:>5} {r:>10.2f} {e:>10.2f} {d_t:>8.4f} {tokens:>10.4f}")
```

Saída esperada (valores aproximados):

```
Epoch    Receita        EMA     D(t)     Tokens
------------------------------------------------
    0    1000.00    1000.00   1.0000   400.0000
    1    1200.00    1066.67   0.9950   424.5333
    2     900.00    1011.11   0.9900   400.4000
    3    1500.00    1174.07   0.9851   462.6540
    4    1100.00    1149.38   0.9802   450.2762
    5    1300.00    1199.59   0.9753   468.0477
    6    1400.00    1266.39   0.9704   491.5693
    7    1250.00    1260.93   0.9656   487.1041
    8    1600.00    1374.28   0.9608   528.0106
    9    1350.00    1366.19   0.9560   522.6269
```

Observe como o fator $D(t)$ vai diminuindo lentamente a cada epoch — tokens ficam progressivamente mais escassos mesmo que a receita cresça.

## 3. Preço de Referência — TWAP e Âncora Interna

### 3.1 TWAP (Time-Weighted Average Price)

Quando já existe mercado, o preço de referência é calculado como a média ponderada pelo tempo dos preços observados:

$$
P_{\text{TWAP}} = \frac{\sum_{i} p_i \cdot \Delta t_i}{\sum_{i} \Delta t_i}
$$

Onde $p_i$ é o preço no instante $i$ e $\Delta t_i$ é o intervalo de tempo que esse preço vigorou.

### 3.2 Âncora Interna (Pré-mercado)

Antes de haver liquidez:

$$
P_{\text{ref}} = \frac{\text{Custo de } X \text{ unidades de dado}}{\text{1 token}}
$$

Isso garante que cada token sempre represente um direito tangível — consumir $X$ unidades de dado na rede — evitando emissão sem lastro.

```python
def twap(precos: list[tuple[float, float]]) -> float:
    """
    Calcula o TWAP a partir de uma lista de (preço, duração_segundos).

    P_twap = Σ(p_i × Δt_i) / Σ(Δt_i)
    """
    soma_ponderada = sum(p * dt for p, dt in precos)
    soma_tempo = sum(dt for _, dt in precos)
    return soma_ponderada / soma_tempo


# Exemplo: preços observados em intervalos de tempo
observacoes = [
    (0.48, 3600),   # 0,48 por 1 hora
    (0.50, 7200),   # 0,50 por 2 horas
    (0.52, 1800),   # 0,52 por 30 min
    (0.49, 5400),   # 0,49 por 1,5 horas
]

preco_twap = twap(observacoes)
print(f"TWAP calculado: {preco_twap:.4f}")
# TWAP calculado: 0.4950
```

## 4. Distribuição Proporcional ao Score

Após determinar `TokensDoEpoch`, a distribuição segue um modelo proporcional simples e auditável, conforme descrito no [projeto CriptoSensor](https://rapport.tec.br/projetos/criptosensor/).

### 4.1 Fórmula de Distribuição

Para o sensor $i$ com score $S_i$, em um epoch com score total $S_{\text{total}}$:

$$
\text{Tokens}_i = \text{TokensDoEpoch} \times \frac{S_i}{S_{\text{total}}}
$$

O pool total é dividido em duas fatias:

| Destinatário | Percentual |
|---|---|
| **Sensores** (produtores de dados) | 70 % |
| **Nós de borda** (validação e agregação) | 30 % |

### 4.2 Score Válido — Regras Anti-farming

O score de um sensor **só é contabilizado** se o dado que ele produziu:

1. Entrou em um **lote entregue** para uma **ordem paga**.
2. Passou pela validação do nó de borda (integridade, não-duplicação, consistência).

Sensores que tentam burlar o sistema sofrem **penalidades progressivas**:

- **Nível 1** — Perda parcial de score no epoch.
- **Nível 2** — Corte de stake (slashing).
- **Nível 3** — Banimento temporário da rede.

### 4.3 Código Python — Distribuição Completa

```python
from dataclasses import dataclass


@dataclass
class Sensor:
    id: str
    score: float
    valido: bool = True  # False se penalizado


@dataclass
class NoDeBorda:
    id: str
    score: float


def distribuir_tokens(
    tokens_epoch: float,
    sensores: list[Sensor],
    nos_borda: list[NoDeBorda],
    frac_sensores: float = 0.70,
    frac_borda: float = 0.30,
) -> dict[str, float]:
    """
    Distribui os tokens do epoch proporcionalmente ao score.

    - 70 % do pool para sensores válidos
    - 30 % para nós de borda
    """
    pool_sensores = tokens_epoch * frac_sensores
    pool_borda = tokens_epoch * frac_borda

    # Filtra apenas sensores com score válido (anti-farming)
    sensores_validos = [s for s in sensores if s.valido and s.score > 0]
    s_total = sum(s.score for s in sensores_validos)

    distribuicao: dict[str, float] = {}

    # Distribuição para sensores
    for s in sensores_validos:
        fracao = s.score / s_total if s_total > 0 else 0
        distribuicao[f"sensor:{s.id}"] = pool_sensores * fracao

    # Distribuição para nós de borda
    b_total = sum(n.score for n in nos_borda)
    for n in nos_borda:
        fracao = n.score / b_total if b_total > 0 else 0
        distribuicao[f"borda:{n.id}"] = pool_borda * fracao

    return distribuicao


# --- Exemplo completo de um epoch ---
tokens_epoch = 500.0  # tokens calculados pela emissão lastreada

sensores = [
    Sensor(id="temp-001", score=120),
    Sensor(id="umid-002", score=95),
    Sensor(id="press-003", score=60),
    Sensor(id="spam-004", score=200, valido=False),  # penalizado!
]

nos_borda = [
    NoDeBorda(id="edge-A", score=80),
    NoDeBorda(id="edge-B", score=45),
]

resultado = distribuir_tokens(tokens_epoch, sensores, nos_borda)

print(f"\nTokens do Epoch: {tokens_epoch:.2f}")
print(f"Pool sensores (70%): {tokens_epoch * 0.70:.2f}")
print(f"Pool borda    (30%): {tokens_epoch * 0.30:.2f}")
print()
print(f"{'Participante':<20} {'Tokens':>10}")
print("-" * 32)
total = 0.0
for participante, tokens in sorted(resultado.items()):
    print(f"{participante:<20} {tokens:>10.4f}")
    total += tokens
print("-" * 32)
print(f"{'TOTAL':<20} {total:>10.4f}")
```

Saída esperada:

```
Tokens do Epoch: 500.00
Pool sensores (70%): 350.00
Pool borda    (30%): 150.00

Participante             Tokens
--------------------------------
borda:edge-A            96.0000
borda:edge-B            54.0000
sensor:press-003        76.3636
sensor:temp-001        152.7273
sensor:umid-002        120.9091
--------------------------------
TOTAL                  500.0000
```

Note que o sensor `spam-004`, apesar de ter o maior score bruto (200), **recebeu zero tokens** por estar penalizado. Isso demonstra a eficácia do mecanismo anti-farming.

## 5. Simulação Integrada — Epoch Completo

O código abaixo integra **emissão + distribuição** em uma simulação de múltiplos epochs:

```python
def simular_epochs(
    receitas: list[float],
    sensores_por_epoch: list[list[Sensor]],
    nos_borda: list[NoDeBorda],
    n_janela: int = 5,
    taxa_incentivo: float = 0.20,
    preco_ref: float = 0.50,
    lambd: float = 0.005,
):
    """Simula a emissão e distribuição ao longo de vários epochs."""
    emas = calc_ema(receitas, n_janela)

    print(f"\n{'='*60}")
    print(f"  SIMULAÇÃO INTEGRADA — {len(receitas)} EPOCHS")
    print(f"{'='*60}\n")

    acumulado: dict[str, float] = {}

    for t in range(len(receitas)):
        tk_epoch = tokens_do_epoch(emas[t], taxa_incentivo, preco_ref, t, lambd)
        dist = distribuir_tokens(tk_epoch, sensores_por_epoch[t], nos_borda)

        print(f"--- Epoch {t} | Receita: {receitas[t]:.0f} | "
              f"EMA: {emas[t]:.2f} | Tokens: {tk_epoch:.2f} ---")

        for k, v in dist.items():
            acumulado[k] = acumulado.get(k, 0) + v
            print(f"  {k}: +{v:.4f}")
        print()

    print(f"{'='*60}")
    print("  ACUMULADO TOTAL")
    print(f"{'='*60}")
    for k, v in sorted(acumulado.items()):
        print(f"  {k}: {v:.4f}")
```

## 6. Propriedades do Modelo

O design descrito no [CriptoSensor](https://rapport.tec.br/projetos/criptosensor/) garante as seguintes propriedades:

| Propriedade | Mecanismo |
|---|---|
| **Sem inflação** | Tokens só são emitidos contra receita confirmada |
| **Escassez crescente** | $D(t) = e^{-\lambda t}$ reduz emissão ao longo do tempo |
| **Suavização** | EMA evita picos de emissão por receitas pontuais |
| **Lastro real** | $P_{\text{ref}}$ ancora o token a consumo de dados |
| **Anti-farming** | Score só conta para dados em ordens pagas |
| **Penalidades** | Slashing e banimento desestimulam dados falsos |
| **Transparência** | Toda a lógica é determinística e auditável on-chain |

## 7. Conclusão

O modelo do [CriptoSensor](https://rapport.tec.br/projetos/criptosensor/) combina três elementos fundamentais:

1. **Emissão controlada** — média móvel + decaimento exponencial impedem que o supply cresça além do que a receita real justifica.
2. **Preço de referência robusto** — TWAP para mercados ativos, âncora funcional para fase de bootstrap.
3. **Distribuição justa** — score proporcional com anti-farming e penalidades garante que apenas contribuições legítimas sejam remuneradas.

O resultado é um token com fundamentos econômicos sólidos: lastro em receita, escassez programada e incentivos alinhados entre todos os participantes da rede.

Para mais detalhes sobre o projeto completo, acesse: **[CriptoSensor — Rapport Tecnologia](https://rapport.tec.br/projetos/criptosensor/)**.
