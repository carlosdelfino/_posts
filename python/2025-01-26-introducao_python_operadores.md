---
title: Introdução ao Python - Operadores
tags: [programacao, linguagens, python, apresentacao, introducao, basico, operadores, operadores matematico, operadores aritmeticos, operadores de atribuicao, operadores de comparacao, operadores logicos, operadores de identidade, operadores de associacao)]
categories: [programacao,python]
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

Python possui diversos tipos de operadores que o tornam matemáticamente e como instrumento de lógica bem poderoso, veremos neste artigo todos os operadores.

<!--more-->

Os operadores no Python são divididos em seis grupos, listados abaixo:

* Operadores aritméticos
* Operadores de atribuição
* Operadores de comparação
* Operadores lógicos
* Operadores de identidade
* Operadores de associação

## Operadores aritméticos

Os operadores aritméticos são adequados a todas as operações matemáticas e tornam o Python bem poderoso neste aspecto, veja como usa-los:

* + (Adição ou sinal positivo), Realiza a soma entre operandos, pode ser usado também para adicionar o sinal de positivo ao número.
* - (Subtração ou sinal negativo), Realiza a subtração entre operandos, pode ser usado também para adicionar o sinal de negativo ao número.
* * (Multiplicação)	Realiza a multiplicação entre operandos
* / (Divisão)	Realiza a divisão entre operandos
* // (Divisão inteira)	Realiza a divisão entre operandos e a parte decimal do resultado
* % (Módulo)	Retorna o resto da divisão entre operandos
* ** (Exponenciação)	Retorna um número elevado a potência de outro

Vejamos na prática estes operadores, você pode usa-los diretamente no interpretador interativo como se fosse uma calculadora.

{% highlight python %}
>>> 4 + -5
>>> 7 - 2
>>> 3 * 6
>>> 45 / 7
>>> 45 // 7
>>> 45 % 7
>>> 10 ** 101
{% endhighlight %}

![]({{site.url}}/images/programacao/python/operadores/operadores_aritimeticos.gif)

## Operadores de Atribuição

Existem seis tipos de atribuição, cinco delas são operações aritiméticas como apresentado acima porém efetuando a operação aritimética com o valor da váriável a esquerda com o valor da direita em seguida se atribui o resultado a variável.

* = atribuição simples
* += atribuição com soma
* -= atribuição com subtração
* *= atribuição com multiplicação
* /= atribuição com divisão
* %= atribuição com módulo

![]({{site.url}}/images/programacao/python/operadores/operadores_atribuicao.gif)

Caso seja seu primeiro contato com programação, variável é um espaço de memória identificado por um nome, se precisa de mais exemplos solicite uma aula sobre o tema no meu [Super Prof](https://www.superprof.com.br/aulas-introdutoria-intermediarias-programacao-aulas-praticas-sem-enrolacao-exemplos-reais.html)

## Operadores de Comparação

Os operadores de comparação retornam verdadeiro (True) ou (False) quanto a dois valores, são usados em operações lógicoas e blocos de controle para tomadas de decisões, veremos mais detalhes em artigos futuros quando estudarmos blocos de controle. São 6 operadores:

* >(Maior que)	Verifica se um valor é maior que outro
* <(Menor que)	Verifica se um valor é menor que outro
* == (Igual a)	Verifica se um valor é igual a outro
* != (Diferente de)	Verifica se um valor é diferente de outro
* >= (Maior ou igual a)	Verifica se um valor é maior ou igual a outro
* <= (Menor ou igual a)	Verifica se um valor é menor ou igual a outro

![]({site.url}/images/programacao/python/operadores/operadores_comparacao.gif)

## Operadores Lógicos

Operadores lógicos são usados como conectores de _operações de comparação_ ou com variáveis boleanas. São 3 os operadores lógicos:

* `and`, retorna `
True` se ambas as condições forem verdadeiras, caso contrário retorna `False`
* `or`,	Retorna `True` se uma das condições for verdadeiras, caso contrário retorna `False`
* `not`,	Inverte o resultado: se o resultado da expressão for `True`, o operador retorna `False`

![]({{site.url}}/images/programacao/python/operadores/operadores_logicos.gif)

## Operadores de Identidade

Os operadores de Identidade são usados para comparar dois objetos, se ambos são os mesmos em posição de memória, ou seja se cada variável faz referência ao mesmo objeto, por que foram passados por referência.

* is
* not is

![]({{site.url}}/images/programacao/python/operadores/operadores_identidade.gif)

## Operadores de Associação

Os operadores de associação são usados para verificar se uma sequência contem um objeto, veremos no artigo a seguir o que são sequẽncias de objetos/dados.

* in
* not in

No caso de strings, que veremos em detalhes no próximo artigo, é verificado se uma sequência de caracteres(string) é contida em outra sequência.

![]({{site.url}}/images/programacao/python/operadores/operadores_associacao.gif)

## Conclusão

Até aqui aprendemos todos os operadores, entre outros recursos que permite um uso bastante interessante do interpretador interativo. Já sabemos atribuir valores a variáveis, fazer operações aritiméticas e comparações.

No próximo artigo vamos aprender sobre os tipos de dados do Python.

Caso seja seu primeiro contato com programação, precisa de mais exemplos solicite uma aula sobre o tema no meu [Super Prof](https://www.superprof.com.br/aulas-introdutoria-intermediarias-programacao-aulas-praticas-sem-enrolacao-exemplos-reais.html)

## Fontes:

* [https://www.superprof.com.br/aulas-introdutoria-intermediarias-programacao-aulas-praticas-sem-enrolacao-exemplos-reais.html](https://www.superprof.com.br/aulas-introdutoria-intermediarias-programacao-aulas-praticas-sem-enrolacao-exemplos-reais.html)
* [https://cieda.com.br](https://cieda.com.br)
* https://pt.wikipedia.org/wiki/Guido_van_Rossum
* https://pt.wikipedia.org/wiki/Python
* https://www.devmedia.com.br/operadores-no-python/40693
