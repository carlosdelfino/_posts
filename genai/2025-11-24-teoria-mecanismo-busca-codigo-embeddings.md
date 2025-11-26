---
title: A teoria por trás de um mecanismo de busca de código
subtitle: Entendendo tokenização, embeddings e similaridade de cosseno
layout: article
categories: [programando]
tags: [busca de código, information retrieval, embeddings, word2vec, similaridade de cosseno, machine learning]
share: true
toc: true
comments: true
ads:
  show: true
tagcloud: true
---

Este artigo foi **inspirado** e fortemente baseado no texto de Gustavo Pinto, publicado na newsletter ML4SE:

- "A teoria por trás de um mecanismo de busca de código – Entendendo sobre tokenização, embeddings e cálculos de similaridades" ([ML4SE](https://ml4se.substack.com/p/a-teoria-por-traz-de-uma-busca-de))

O objetivo aqui é **recontar e complementar** aquela explicação, trazendo mais exemplos e referências para quem quer se aprofundar em mecanismos de busca de código.

<!--more-->

## 1. Motivação: por que buscar código é difícil?

Pessoas desenvolvedoras passam uma parte enorme do tempo **lendo** e **procurando** código: seja em bases internas, em projetos open source, em repositórios no GitHub ou em perguntas no Stack Overflow. O artigo original do Gustavo Pinto, *“A teoria por trás de um mecanismo de busca de código – Entendendo sobre tokenização, embeddings e cálculos de similaridades”* (disponível em [ML4SE](https://ml4se.substack.com/p/a-teoria-por-traz-de-uma-busca-de)), destaca justamente esse ponto: a busca de código é tão onipresente que muitas vezes nem percebemos o quanto nossa produtividade depende de bons mecanismos de busca.

Para perceber o impacto disso, basta imaginar um cenário sem esses mecanismos:

- **Sem busca de código**, você precisaria:
  - Navegar manualmente pela documentação (muitas vezes desatualizada ou incompleta).
  - Ler código de múltiplos módulos/pacotes até “tropeçar” em um exemplo útil.
  - Descobrir “na unha” qual API usar, antes mesmo de entender como usá-la.

- **Com boa busca de código**, você pode:
  - Escrever uma dúvida em linguagem natural, como “como iterar por uma `HashMap` em Java?”.
  - Receber **diretamente um trecho de código** relevante (muitas vezes com explicação ou discussão da comunidade).

Esse contraste ajuda a entender por que plataformas como **Stack Overflow** se tornaram tão populares: elas permitem expressar problemas complexos em linguagem natural e recuperar rapidamente **exemplos de código concretos**.

### 1.1. O desafio de conectar linguagem natural e código

O texto do Gustavo Pinto usa um exemplo bastante ilustrativo:

- Query em linguagem natural:  
  `“como iterar por uma HashMap?”`
- Trecho de código potencialmente relevante (extraído de uma resposta no Stack Overflow, conforme o próprio Gustavo referencia o link [1066589](https://stackoverflow.com/questions/1066589/iterate-through-a-hashmap)):

```java
public static void printMap(Map mp) {
    Iterator it = mp.entrySet().iterator();
    while (it.hasNext()) {
        Map.Entry pair = (Map.Entry)it.next();
        System.out.println(pair.getKey() + " = " + pair.getValue());
        it.remove(); // avoids a ConcurrentModificationException
    }
}
```

Ambos descrevem a **mesma intenção** (iterar sobre um mapa e imprimir os pares chave/valor), mas:

- A query usa termos como `“iterar”` e `“HashMap”` em português.
- O código usa:
  - Tipos e métodos de Java (`Map`, `Iterator`, `entrySet`, `hasNext`, `System.out.println`).
  - Nenhum comentário com as palavras “iterar” ou “HashMap”.

Ou seja, **a semântica é parecida**, mas o vocabulário e a estrutura são completamente diferentes. Esse descompasso (que a literatura chama de **heterogeneidade entre linguagem natural e código**) é uma das principais razões pelas quais **métodos clássicos de busca** têm dificuldade em encontrar o código “certo” para uma pergunta em linguagem natural.

### 1.2. Por que mecanismos sofisticados de busca importam?

Mecanismos mais sofisticados de busca de código tentam responder a perguntas como:

- Dada uma pergunta em linguagem natural, **como encontrar trechos de código semanticamente relacionados**, mesmo que as palavras não batam literalmente?
- Como lidar com:
  - **Sinônimos** ou traduções (por exemplo, “executar” vs `run`, “mapa” vs `Map`/`HashMap`).
  - **Queries mal formuladas**, com termos irrelevantes ou ambíguos.
  - **Diferenças de granularidade**, como perguntas de alto nível (“como paginar resultados numa API REST?”) vs implementações de baixo nível (loops, condicionais, chamadas de biblioteca).

A comunidade de **Recuperação de Informação (IR)** já desenvolveu há décadas técnicas para buscar documentos de texto de forma eficiente (ver, por exemplo, a entrada de [Information Retrieval](https://en.wikipedia.org/wiki/Information_retrieval) na Wikipedia). Porém, ao longo de quase 20 anos de pesquisa em busca de código, como lembra o artigo do Gustavo, a comunidade percebeu que **aplicar IR diretamente a código fonte não é suficiente**.

No restante deste artigo (inspirado e fortemente baseado no texto do Gustavo Pinto em ML4SE, mas complementado com referências adicionais), vamos:

- Revisitar rapidamente a **IR clássica aplicada a código**.
- Ver como surgem conceitos de **tokenização**, **bag-of-words** e **embeddings**.
- Entender como **similaridade de cosseno** e **modelos neurais** (como Word2Vec e representações específicas de código) ajudam a reduzir o abismo entre texto e código.
- Apontar para projetos open source que implementam esses conceitos na prática.

## 2. Recuperação de Informação (IR) aplicada a código

O artigo do Gustavo Pinto apresenta como ponto de partida as técnicas clássicas de **Recuperação de Informação (Information Retrieval – IR)**, amplamente usadas para buscar documentos textuais em grandes coleções. De forma simplificada, um sistema de IR tradicional costuma envolver:

- **Pré-processamento dos documentos**, incluindo etapas como tokenização, remoção de stopwords, stemming ou lematização.
- Construção de um **índice invertido**, estrutura central descrita em obras clássicas de IR (como o livro *Introduction to Information Retrieval*, de Manning, Raghavan e Schütze, publicado pela Cambridge University Press), em que para cada termo armazenamos a lista de documentos onde ele aparece.
- Um modelo de pontuação, frequentemente baseado em variantes de **TF–IDF (Term Frequency–Inverse Document Frequency)**, para medir a relevância de um documento em relação a uma query (ver, por exemplo, as entradas de [TF–IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) e [Inverted index](https://en.wikipedia.org/wiki/Inverted_index) na Wikipedia).

Em muitas plataformas de busca de código, especialmente as mais antigas, esses mesmos princípios foram adaptados para trabalhar sobre arquivos de código-fonte. Nesses casos, cada arquivo, função ou trecho de código é tratado como um “documento” em que:

- Os **tokens** podem incluir identificadores, nomes de métodos, literais de string e comentários.
- A **query** do usuário (por exemplo, "iterate hashmap java") é processada de forma semelhante e comparada com os documentos indexados.

### 2.1. Exemplo histórico: Sourcerer

Um dos exemplos citados pelo Gustavo é o **Sourcerer**, um mecanismo de busca de código desenvolvido no meio acadêmico por pesquisadores da Universidade da Califórnia, Irvine (UCI). O repositório público do projeto está disponível em: [https://github.com/Mondego/Sourcerer](https://github.com/Mondego/Sourcerer).

O Sourcerer foi concebido na metade dos anos 2000 com duas ideias importantes para a época:

- **Indexar grandes bases de código open source**, permitindo buscas em larga escala.
- Levar em conta não apenas palavras soltas, mas também **aspectos estruturais do código**, como:
  - Presença de laços (`for`, `while`).
  - Condicionais (`if`, `switch`).
  - Padrões de orientação a objetos (classes, métodos, herança).
  - Elementos relacionados a concorrência, entre outros.

Na prática, isso significava ir além de simplesmente buscar por termos textuais, tentando incorporar ao índice informações sintáticas/estruturais extraídas por meio de análise do código. O objetivo era aproximar a busca do modo como desenvolvedores pensam sobre programas (por exemplo, “métodos que usam laços sobre coleções” ou “classes que implementam determinada interface”).

### 2.2. Limitações de IR quando aplicada diretamente a código

Apesar dessas melhorias, a comunidade técnico-científica foi, ao longo de quase duas décadas, documentando diversas **limitações** quando técnicas de IR clássica são aplicadas diretamente a código. O próprio exemplo da query “como iterar por uma HashMap?” evidencia alguns desses pontos:

- O código relevante pode **não compartilhar os mesmos tokens** da query (por exemplo, usar `Map` em vez de `HashMap`, ou não conter a palavra “iterar” em comentários).
- Queries em linguagem natural podem trazer **sinônimos**, variações morfológicas ou até termos em línguas diferentes (português vs inglês) que não aparecem literalmente no código.
- Muitas consultas reais são **mal formuladas**, com termos redundantes ou irrelevantes, o que prejudica modelos que dependem fortemente de correspondência exata de palavras.

Diversos trabalhos em busca de código apontam para esse problema de **heterogeneidade** entre linguagem natural e código-fonte. Enquanto o código é altamente estruturado, com uma gramática rígida e convenções específicas, a linguagem natural é ambígua e rica em variações. Como consequência, mesmo com índices eficientes e modelos de pontuação bem estabelecidos, a IR tradicional:

- Tem dificuldade em capturar **intenção de alto nível** da pergunta.
- Não consegue, sozinha, reconhecer que “executar um comando” e “chamar `run()` em uma thread” podem ser eventos semanticamente próximos.

Essas limitações motivaram a adoção de abordagens baseadas em **aprendizado de máquina** e, mais recentemente, em **aprendizado profundo**, que procuram representar tanto texto quanto código em espaços vetoriais contínuos, onde **similaridade semântica** possa ser medida de forma mais robusta do que apenas pela sobreposição de tokens.

Na próxima seção, vamos dar o próximo passo nessa transição: entender como ideias como **tokenização**, **bag-of-words** e **embeddings** ajudam a “traduzir” programas e consultas de linguagem natural para vetores numéricos que podem ser comparados por medidas como a **similaridade de cosseno**.

## 3. Do bag-of-words aos embeddings

O texto do Gustavo Pinto faz uma transição importante: sair da visão puramente baseada em termos e frequências e caminhar para representações vetoriais que capturam melhor a semântica. O ponto de partida é um exemplo simples de código em Java:

```java
for (entry : map.entrySet()) {
    System.out.println(entry);
}
```

Para que um mecanismo de busca "entenda" esse trecho de programa, é comum traduzi-lo para uma forma textual mais simples. Uma etapa clássica, tanto em compiladores quanto em sistemas de IR, é a **análise léxica**:

- O código é quebrado em **tokens**: identificadores, palavras-chave, operadores, símbolos especiais, etc.
- Comentários e espaços em branco geralmente são removidos.

Aplicando uma tokenização simplificada ao exemplo, o artigo do Gustavo mostra uma sequência como:

```text
for entry map entry set system out println entry
```

Nesse ponto, temos uma lista de tokens que representam o código. A pergunta natural é: **como transformar essa lista em algo que um algoritmo de máquina possa manipular?**

### 3.1. Bag-of-words: uma primeira representação vetorial

Uma forma simples e bastante difundida em IR é usar o modelo de **bag-of-words** (BoW). A ideia é:

- Construir um **vocabulário** com todos os tokens relevantes que aparecem na coleção (por exemplo, `for`, `entry`, `map`, `set`, `system`, `out`, `println`).
- Representar cada documento (no nosso caso, cada trecho de código) por um **vetor de contagens**, em que cada posição indica quantas vezes o token correspondente aparece.

No exemplo anterior, considerando apenas os tokens distintos, poderíamos ter algo como:

- Vocabulário: `[for, entry, map, set, system, out, println]`
- Vetor de frequências: `[1, 2, 1, 1, 1, 1, 1]`

O próprio Gustavo usa exatamente essa sequência de frequências no artigo original para ilustrar como se obtém um *embedding* simples via contagem de tokens.

Em IR tradicional, variações do bag-of-words com **TF–IDF** são onipresentes (vide [bag-of-words model](https://en.wikipedia.org/wiki/Bag-of-words_model) e [Tf–idf](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) na Wikipedia). A mesma lógica pode ser aplicada a código: cada trecho se torna um vetor em um espaço de alta dimensão, e a similaridade entre documentos pode ser calculada, por exemplo, via **similaridade de cosseno**.

### 3.2. Aplicando bag-of-words a diferentes trechos de código

O artigo do ML4SE também sugere aplicar essa mesma ideia ao primeiro trecho de código apresentado (aquele que itera um `Map` com `Iterator`). Após tokenizar o código, obtém-se uma longa sequência de tokens (todos em minúsculo, sem pontuação), como:

```text
public static void printmap map mp iterator it while it hasnext map entry pair map entry it next system out println pair getkey pair getvalue it remove
```

Contando a frequência de cada token, o autor chega a um vetor como:

```text
[1, 1, 1, 1, 2, 1, 1, 2, 1, 1, 2, 2, 1, 1, 1, 1, 1, 1, 1]
```

Agora temos dois vetores (um para cada trecho de código) e podemos compará-los numericamente. Em teoria, isso abre espaço para usar medidas como:

- **Distância euclidiana**.
- **Similaridade de cosseno**, muito comum em modelos vetoriais de IR.

Na prática, porém, surgem problemas:

- Os vetores podem ter **dimensões diferentes** se o vocabulário usado não for o mesmo ou se o pré-processamento for inconsistente.
- Mesmo com o mesmo vocabulário, o bag-of-words **ignora a ordem dos tokens**, o que é particularmente grave em código, onde a ordem muitas vezes carrega semântica (por exemplo, `System.out.println(x)` não é equivalente a `println.out.system(x)`).

Essas limitações já são bem documentadas na literatura de IR e de NLP (Natural Language Processing), e motivaram o surgimento de técnicas de **representações distribuídas**, como os **embeddings**.

### 3.3. Limitações do bag-of-words para código

O próprio texto do Gustavo destaca alguns pontos críticos do bag-of-words aplicado a código:

- Como os tokens são tratados **individualmente**, é difícil manter as relações entre eles (por exemplo, a chamada completa `System.out.println()` acaba "quebrada" em partes que podem perder seu contexto conjunto).
- A perda de ordem prejudica a captura de construções linguísticas e estruturais importantes.

Estudos recentes em busca de código argumentam que essas limitações tornam o bag-of-words um ponto de partida útil, mas **insuficiente** para capturar relações semânticas mais ricas entre programas e consultas em linguagem natural. Por isso, trabalhos mais modernos partem para modelos que aprendem vetores contínuos em que tokens e sequências de tokens semanticamente relacionados ficam próximos no espaço vetorial.

Na próxima seção, vamos nos apoiar em uma dessas técnicas fundamentais, o **Word2Vec**, originalmente proposto por Tomas Mikolov e colaboradores em 2013 (ver o artigo "[Distributed Representations of Words and Phrases and their Compositionality](https://arxiv.org/abs/1310.4546)" e o verbete [Word2vec](https://en.wikipedia.org/wiki/Word2vec) na Wikipedia). Veremos como a mesma ideia de aprender representações vetoriais para palavras em linguagem natural pode ser adaptada para tokens de código e, mais adiante, para construir **embeddings conjuntos de código e texto** usados em busca semântica de código.

## 4. Embeddings com Word2Vec e representações distribuídas

O passo seguinte ao bag-of-words é abandonar a ideia de que cada palavra (ou token) é um índice discreto em um vetor esparso e passar a representá-la como um **vetor denso** em um espaço contínuo. Essa é a proposta central dos modelos de **word embeddings**, como o **Word2Vec**.

De acordo com a descrição no artigo de Mikolov et al. (*Distributed Representations of Words and Phrases and their Compositionality*, 2013) e no verbete [Word2vec](https://en.wikipedia.org/wiki/Word2vec), o Word2Vec é:

- Uma **rede neural rasa** (duas camadas) treinada de forma não supervisionada.
- Projetada para atribuir a cada palavra um vetor de dimensão fixa (por exemplo, 100 ou 300 dimensões).
- Treinada com base no **contexto** em que as palavras aparecem, seguindo a hipótese distribucional de que “palavras que ocorrem em contextos semelhantes tendem a ter significados semelhantes”.

### 4.1. CBOW e Skip-gram

O Word2Vec é comumente implementado em duas variantes principais:

- **CBOW (Continuous Bag-of-Words)**: dado o contexto (palavras ao redor), o modelo tenta **prever a palavra central**. Em termos simples, ele recebe como entrada uma janela de palavras e retorna a probabilidade de cada possível palavra central.
- **Skip-gram**: faz o inverso; dado uma palavra central, tenta **prever as palavras de contexto**. Essa abordagem é particularmente eficiente para aprender boas representações mesmo para palavras relativamente raras.

Ambas as variantes produzem, ao final do treinamento, uma matriz de embeddings em que cada linha corresponde ao vetor de uma palavra do vocabulário. A qualidade desses vetores é frequentemente avaliada usando tarefas de similaridade semântica e analógicas (por exemplo, "king - man + woman ≈ queen"), como discutido na seção de "Analysis" do artigo e na própria página da Wikipedia de Word2Vec.

### 4.2. Similaridade de cosseno em embeddings

Uma vez que temos vetores densos para cada palavra, podemos medir a **proximidade semântica** entre duas palavras (ou entre combinações de palavras) usando métricas como a **similaridade de cosseno**. Essa técnica é extensivamente mencionada em materiais introdutórios sobre embeddings e em guias gerais de busca semântica (por exemplo, artigos que explicam a relação entre embeddings e bancos de dados vetoriais).

No artigo do Gustavo Pinto, essa ideia é ilustrada com um exemplo numérico:

- Suponha que o termo `execute` seja representado pelo vetor `[0.12, -0.32, 0.01]`.
- E que o termo `run` seja representado por `[0.12, -0.31, 0.02]`.

Ao calcular a similaridade de cosseno entre esses vetores, observamos um valor alto, indicando que os vetores (e, portanto, as palavras) ocupam posições próximas no espaço vetorial. Isso reflete o fato de que, em muitos contextos, `execute` e `run` podem aparecer em situações semanticamente parecidas.

### 4.3. Adaptando a ideia para tokens de código

Embora o Word2Vec tenha sido originalmente proposto para **palavras em linguagem natural**, a mesma técnica pode ser aplicada a **tokens de código**:

- Em vez de treinar o modelo em frases ou documentos de texto, treinamos em **arquivos de código-fonte**, onde cada linha, método ou arquivo é tratado como uma sequência de tokens.
- O contexto passa a ser formado por tokens vizinhos em um trecho de código (por exemplo, `System`, `out`, `println`, `entry`).

Essa adaptação é discutida em diversos trabalhos de *code embeddings* e *neural code search*. Um exemplo influente é o **code2vec**, de Alon et al. (*code2vec: Learning Distributed Representations of Code*, 2018, disponível em [https://arxiv.org/abs/1803.09473](https://arxiv.org/abs/1803.09473)), que propõe representar trechos de código como combinações de múltiplos caminhos na árvore de sintaxe abstrata (AST). Embora o code2vec vá além do Word2Vec clássico, ele se apoia na mesma intuição de atribuir **vetores densos** a elementos de código e combiná-los para representar um snippet inteiro.

Outros modelos mais recentes, como variantes baseadas em Transformers (por exemplo, modelos do tipo *CodeBERT* e sucessores, descritos em artigos de pesquisa e documentados em plataformas como o Hugging Face), seguem a mesma filosofia de gerar **embeddings contextuais** para tokens de código, permitindo capturar nuances sintáticas e semânticas mais finas.

### 4.4. Vantagens em relação ao bag-of-words

Comparado com o bag-of-words tradicional, o uso de embeddings à la Word2Vec oferece algumas vantagens importantes para busca de código:

- **Captura de sinônimos e variações**: palavras (ou tokens) que aparecem em contextos semelhantes tendem a ter vetores próximos, mesmo que nunca coocorram literalmente com as mesmas palavras-chave.
- **Espaço contínuo**: torna possível interpolar significados e medir similaridade de forma mais rica do que uma simples contagem de termos.
- **Fundação para modelos mais avançados**: embeddings estáticos (como Word2Vec) servem de base para arquiteturas mais complexas, incluindo modelos bi-modais que projetam texto e código no mesmo espaço vetorial.

 No contexto de mecanismos de busca de código, isso significa que uma query em linguagem natural pode ser transformada em um vetor que "entende" melhor a intenção do usuário, e pode ser comparado com vetores de trechos de código mesmo quando não há sobreposição literal de tokens.

 Na próxima parte do artigo, vamos olhar para **embeddings conjuntos de texto e código (bi-modais)**, como discutido pelo Gustavo Pinto, e para trabalhos que treinam redes neurais para mapear simultaneamente descrições em linguagem natural e trechos de código para o **mesmo espaço vetorial**, permitindo busca semântica mais robusta.

## 5. Embeddings bi-modais para texto e código

Até aqui, falamos principalmente de embeddings de **um único tipo de dado** (texto ou código). No entanto, mecanismos modernos de busca de código precisam lidar, ao mesmo tempo, com:

- **Queries em linguagem natural** (por exemplo, “como iterar por uma HashMap?”).
- **Trechos de código** (como o método `printMap` em Java visto no início).

O artigo do Gustavo Pinto introduz o conceito de **bi-modal embeddings** (ou multi-modal quando há mais de dois tipos de dados): representações em que **diferentes modos de dados** (texto, código, imagens etc.) são projetados para um **mesmo espaço vetorial compartilhado**.

### 5.1. Ideia geral de embeddings bi-modais

De forma resumida, um sistema de embeddings bi-modais costuma ter:

- Uma rede (ou parte da rede) dedicada a processar **texto em linguagem natural**.
- Outra rede dedicada a processar **código-fonte** (muitas vezes usando arquiteturas especializadas ou informações estruturais como ASTs).
- Uma ou mais camadas que **combinam** essas representações em um **espaço comum**.

As abordagens descritas na literatura incluem o uso de:

- **Redes neurais convolucionais (CNNs)**, historicamente aplicadas a texto para capturar padrões locais de n-gramas.
- **Redes neurais recorrentes (RNNs)**, como LSTMs e GRUs, para modelar sequências de tokens.
- **Modelos baseados em Transformers**, que hoje são amplamente utilizados para texto e código, como diversas variantes BERT-like treinadas em corpora de código.

Embora a arquitetura concreta varie de trabalho para trabalho, a ideia central é a mesma mencionada no texto do ML4SE: cada tipo de dado é processado por uma rede que extrai uma representação específica, e depois essas representações são combinadas (por concatenação, soma, multiplicação ou camadas adicionais) de forma a habitar um **espaço vetorial compartilhado**.

### 5.2. Benefícios para busca de código

Com embeddings bi-modais, consultas em linguagem natural e trechos de código passam a ser representados por vetores comparáveis diretamente:

- Uma query como “como iterar por uma HashMap?” é mapeada para um vetor `q` em \(\mathbb{R}^d\).
- Cada trecho de código indexado é mapeado para um vetor `c_i` no mesmo espaço.

Em seguida, é possível:

- Calcular a **similaridade de cosseno** entre `q` e cada `c_i`.
- Recuperar os **top N** trechos de código mais similares.

Esse é exatamente o cenário descrito pelo Gustavo, quando ele menciona que uma consulta como “como iterar por um HashMap” pode ser codificada em um vetor de embedding (no exemplo, `[0.2, 0.4, 0.5]`) e utilizada para buscar trechos de código relevantes de acordo com a proximidade vetorial.

Trabalhos de **neural code search** publicados nos últimos anos, alguns dos quais são referenciados no próprio artigo do ML4SE (por exemplo, o PDF `ncs.pdf` citado pelo autor), seguem esse princípio de projetar código e texto em um espaço compartilhado para permitir busca semântica.

## 6. Pipeline prático de uma busca semântica de código

Com todos os blocos teóricos apresentados, podemos resumir um pipeline típico de **busca semântica de código** que segue a linha descrita no artigo do Gustavo Pinto e em diversos trabalhos acadêmicos:

1. **Coleta e pré-processamento do código**
   - Clonar repositórios ou coletar bases open source.
   - Extrair unidades de interesse (por exemplo, métodos, funções, blocos de código).
   - Realizar **tokenização** e outros pré-processamentos (remoção de comentários irrelevantes, normalização, etc.).

2. **Geração de embeddings de código**
   - Utilizar um modelo treinado (por exemplo, baseado em Word2Vec, code2vec ou modelos Transformer especializados em código) para converter cada trecho em um **vetor de dimensão fixa**.
   - Armazenar esses vetores em uma estrutura de dados apropriada (como um índice vetorial).

3. **Indexação vetorial**
   - Usar estruturas otimizadas para busca por similaridade (muitas vezes algoritmos de **aproximação de vizinhos mais próximos** – ANN), que permitem responder consultas em alta dimensão de forma eficiente.

4. **Processamento da query em linguagem natural**
   - Receber a pergunta do usuário (por exemplo, “como iterar por uma HashMap?”).
   - Tokenizar e normalizar o texto da query.
   - Utilizar o mesmo modelo (ou um modelo complementar, no caso de embeddings bi-modais) para gerar o **vetor de embedding da query**.

5. **Cálculo de similaridade e recuperação**
   - Calcular a **similaridade de cosseno** (ou outra métrica apropriada) entre o vetor da query e os vetores de código indexados.
   - Retornar os **top N** trechos de código mais similares.

6. **Apresentação dos resultados**
   - Exibir os snippets de código encontrados, frequentemente acompanhados de metadados (arquivo, projeto, linguagem, etc.) e, quando possível, de explicações ou comentários da comunidade (como no Stack Overflow).

O artigo do ML4SE ilustra esse pipeline de forma conceitual quando mostra a query sendo mapeada para um vetor `[0.2, 0.4, 0.5]` e usada para buscar trechos de código mais próximos nesse espaço vetorial.

Projetos open source recentes implementam partes ou a totalidade desse fluxo, combinando bibliotecas de embeddings, bancos de dados vetoriais e integrações com IDEs ou plataformas web.

## 7. Conclusão e referências

Realizar buscas de código eficazes é uma tarefa que exige a combinação de **técnicas clássicas de IR**, **modelagem de linguagem natural** e **modelos neurais especializados em código**. Ao longo deste texto, inspirado diretamente no artigo “A teoria por trás de um mecanismo de busca de código” do Gustavo Pinto na newsletter ML4SE, vimos como:

- Abordagens clássicas de IR (índice invertido, TF–IDF, Sourcerer) fornecem uma base sólida, mas esbarram na heterogeneidade entre texto e código.
- Modelos simples como **bag-of-words** ajudam a transformar código em vetores, mas perdem ordem e relações semânticas importantes.
- Técnicas de **embeddings** (Word2Vec e derivados) permitem representar tokens em espaços vetoriais contínuos onde similaridade semântica pode ser mensurada de forma mais robusta.
- Modelos mais avançados de **code embeddings** (por exemplo, code2vec) e arquiteturas baseadas em Transformers estendem essas ideias para capturar melhor a estrutura e o significado de programas.
- **Embeddings bi-modais** e pipelines de busca vetorial possibilitam mapear, em um mesmo espaço, consultas em linguagem natural e trechos de código, permitindo que queries como “como iterar por uma HashMap?” retornem implementações relevantes mesmo sem coincidência literal de tokens.

O texto original do Gustavo conclui mencionando projetos open source que implementam partes desse quebra-cabeça. Um exemplo citado é o projeto **sem** (*semantic code search*), disponível em: [https://github.com/sturdy-dev/semantic-code-search](https://github.com/sturdy-dev/semantic-code-search). Explorar o código-fonte de ferramentas como essa é uma forma prática de ver como os conceitos de tokenização, embeddings e similaridade são conectados em sistemas reais.

### Referências selecionadas

- Gustavo Pinto. *A teoria por trás de um mecanismo de busca de código – Entendendo sobre tokenização, embeddings e cálculos de similaridades*. ML4SE, disponível em: [https://ml4se.substack.com/p/a-teoria-por-traz-de-uma-busca-de](https://ml4se.substack.com/p/a-teoria-por-traz-de-uma-busca-de)
- Christopher D. Manning, Prabhakar Raghavan, Hinrich Schütze. *Introduction to Information Retrieval*. Cambridge University Press.
- Wikipedia. *Information retrieval*: [https://en.wikipedia.org/wiki/Information_retrieval](https://en.wikipedia.org/wiki/Information_retrieval)
- Wikipedia. *Bag-of-words model*: [https://en.wikipedia.org/wiki/Bag-of-words_model](https://en.wikipedia.org/wiki/Bag-of-words_model)
- Wikipedia. *Tf–idf*: [https://en.wikipedia.org/wiki/Tf%E2%80%93idf](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)
- Wikipedia. *Word2vec*: [https://en.wikipedia.org/wiki/Word2vec](https://en.wikipedia.org/wiki/Word2vec)
- Tomas Mikolov et al. *Distributed Representations of Words and Phrases and their Compositionality*, 2013. Disponível em: [https://arxiv.org/abs/1310.4546](https://arxiv.org/abs/1310.4546)
- Uri Alon et al. *code2vec: Learning Distributed Representations of Code*, 2018. Disponível em: [https://arxiv.org/abs/1803.09473](https://arxiv.org/abs/1803.09473)
- Projeto **Sourcerer** – engine de busca de código acadêmica: [https://github.com/Mondego/Sourcerer](https://github.com/Mondego/Sourcerer)
- Projeto **sem** – *semantic code search*: [https://github.com/sturdy-dev/semantic-code-search](https://github.com/sturdy-dev/semantic-code-search)
