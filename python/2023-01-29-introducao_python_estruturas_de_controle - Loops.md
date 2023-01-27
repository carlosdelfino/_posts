---
title: Introdução ao Python - Estruturas de Controle - Loops
tags: [programacao, linguagens, python, apresentacao, introducao, basico, estruturas de controle, loops, for, while]
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

Veremos neste artigo as estruturas de controle de loop, são estruturas que repetem blocos de código continuamente dentro de uma condição estipulada pelo programador.


<!--more-->

O python possui diversas estruturas de controle, entre eleas estruturas para loop e tomada de decisão, além de controle de fluxo do código e controle de erros, e também uma estrutura para contextualização de código. 

As estruturas de controle para loop são compostas de duas partes, o cabeçalho que é composto por uma declaração que identifica o tipo de loop, a condição para repetição e finalizado com dois pontos `:`, em seguida o corpo com as instruções que serão repetidas.

## Loops com `while`

`while` é usado para criar um loop que é continuamente executado enquanto uma declaração lógica é verdadeira. 

Vamos fazer um loop para lidar com elementos de uma lista, já vimos o que é uma lista no artigo [Introdução ao Python - Tipos de Dados]({{ site.url }}{% post_url /python/2023-01-27-introducao_python_tipos_de_dados %})

Uma lista quando está cheia retorna sempre `True`, mas quando está vazia retorna `False`, sendo assim podemos executar um loop `while` enquanto ouver elementos na lista, no exemplo abaixo fazendo este teste que pode ser descrito da seguinte forma: _Enquanto ouver itens, retire um item e imprima na tela_.

{% highlight python %}
>>> idades = [22,43, 21, 30, 88, 90, 57, 86]
>>> idades.sort()
>>> idades.reverse()
>>> while idades:
...     print(idades.pop())
...
>>>
{% endhighlight %}

![]({{ site.url }}/images/programacao/python/estruturas_de_controle/loop_while.gif)
 
No codigo acima, criamos uma lista qualquer que representa idades de pessoas desconhecidas, em seguida pedimos ao python que ordene esta lista de forma crescente, e em seguida invertemos a ordem desta lista, assim temos a lista em forma decresente. Poderiamos ter informado a função `sort`o parametro `reverse` e poupado uma linha de código. finalmente criamos a estrutura de loop com a declaração `while` que certifica se nosso objeto `idades` tem elementos, caso verdadeiro, ele retira um elemento e imprime-o na tela.

Vejamos outro exemplo:

{% highlight python %}
>>> planetas = [
...  'Mercurio',
...  'Vênus',
...  'Terra',
...  'Marte',
...  'Júpiter',
...  'Saturno',
...  'Urano',
...  'Netuno',
...  ]
...
>>> planetas.sort(reverse=True)
>>> while planetas:
...     print(planetas.pop())
... 
>>> 
{% endhighlight %}

![]({{ site.url }}/images/programacao/python/estruturas_de_controle/loop_while_2.gif)

## Loops com `for`

O `for` é um outro tipo de loop que permite maior controle, você pode interagir sobre um `range`, lista, tuple, string ou dicionário ou qualquer outro objeto que seja do tipo `interable`.

Vamos ver dois exemplos, o primeiro interagindo sobre um `range`, o seguindo sobre um dicionário.


{% highlight python %}
>>> for idx in range(4,25,2):
...     print(idx)
...
>>>
{% endhighlight %}

No exemplo logo acima, declaramos um loop do tipo for que irá interagir sobre um range de inteiros iniciando em 4 e finalizando em 25, com passos de 2 em 2. 

![]({{site.url}}/images/programacao/python/estruturas_de_controle/loop_for_range.gif)

Já no próximo exemplo iremos interagir com um dicionário obtendo os valores neles armazenados.

{% highlight python %}
>>> planetas = {
...  'Sol': 696342,
...  'Júpiter': 69911,
...  'Saturno': 58232,
...  'Urano': 25362,
...  }
>>> for nome in planetas:
...     print('Planeta {planeta} tem o raio de {raio:.2f}'.format(planeta=nome, raio=planetas[nome]))
{% endhighlight %}

![]({{site.usr}}/images/programacao/python/estruturas_de_controle/loop_for_dictionary.gif)

No exemplo acima para cada chave no dicionário `planetas` ele faz um loop sobre as instruções identadas no corpo do loop, então ele formata uma string inserindo nela a propriedade `planeta` e `raio`, sendo esta do tipo float com duas casas na fração. Veremos em breve como formatar strings.

## Conclusão

Aprendemos um pouco mais sobre o python, controlamos loops e lidamos com listas e dicionários, veremos no próximo artigo como tomar decisões e alterar o fluxo do código executando blocos de códigos conforme condições determinadas.

## Fontes:

* [https://cieda.com.br](https://cieda.com.br)
* https://docs.python.org/3/reference/compound_stmts.html
* https://www.w3schools.com/python/ref_string_format.asp
* https://docs.python.org/pt-br/3/tutorial/inputoutput.html
