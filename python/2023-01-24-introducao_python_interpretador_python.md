---
title: Introdução ao Python - O Interpretador Interativo do Python
tags: [programacao, linguagens, python, apresentacao, introducao, basico, interpretador, linha de comando]
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

A linguagem Python é interpretada, e possui um interpretador interativo que pode ser acessado da linha de comando, permitindo que o programador apresente os comandos um a um e faça tarefas rápidas e de forma eficiente.

<!--more-->

Além do interpretador ser uma avançada calculadora, em especial com a ajuda de módulos avançados como o [NumPy]({% post_url python/2023-01-23-introducao_python_o_modulo_numpy %}) e [Pandas]({% post_url python/2023-01-23-introducao_python_o_modulo_pandas %}), também é possível fazer tratamentos de dados em arquivos, APIs remotas.

Para acessar o interpretador interativo basta executar o comando `python` na linha de comando do shell de seu sistema operacional. Para sair basta digitar `exit()` seguido da tecla `enter`.

Ao entrar lhe será apresentado a versão do python entre outras informações, seguido pelo prompt `>>>`.

Python tem uma estrutura de programação fortemente atrelada a intentação, e para identificar que é preciso identar o prompt muda para `...`, vejamos um exemplo prático que será estudado em detalhes mais a frente.

{% highlight python %}
>>> for i in range(6):
...     print(i)
{% endhighlight %}

![]({{site.url}}/images/programacao/python/interpretador_interativo/usando_interpretador_interativo.gif)


## Operações matemáticas básicas

No interpretador interativo você pode fazer operações matemáticas básicas, por exemplo:

{% highlight python %}
>>> 10 + 10
>>> 20 * 33 / 4
>>> (-34**2 - 3) / 2
>>> print("e assim por diante!")
{% endhighlight %}

![]({{site.url}}/images/programacao/python/interpretador_interativo/usando_interpretador_interativo_como_calculadora.gif)

## Easter Eggs

Easter Eggs são surpresas inseridas no código e o python como tudo que envolve bons programadores tem os seus, veja alguns.

### The Zen of Python

Ao digitar `import this`no interpretador interativo você obtem o poema *The Zen of Python*, ele foi inserido no [PEP 20](https://peps.python.org/pep-0020/) (Python Enhancement Proposal) e deveria ser 20 aforismos porém apenas 19 foram escritos. Este poema foi escrito pelo Pythoneer de longa data Tim Peters com o objetivo de apontar os princípios orientadores do BDFL para o design do Python.

### Antigravity

Digite `import antigravity` e você é direcionado para um artigo especial no site XKCD um site de webcomics.

![]({{site.url}}/images/programacao/python/easter_eggs/python_antigravity.png)

### Usando Unicode nos identificadores

Python 3 é 100% compátivel com Unicode, sendo assim, pode-se usar caracteres especiais como letras gregas, e até acentuação para descrever suas variáveis.

{% highlight python %}
>>> from math import pi as π
>>> raio = 100
>>> área = π * raio**2
{% endhighlight %}

### Mais Easter Eggs

Para outros Easter Eggs visite a página [https://github.com/OrkoHunter/python-easter-eggs](https://github.com/OrkoHunter/python-easter-eggs)

## Conclusão

Aprendemos o Prompt Interativo do Python, como usa-lo fazendo dele uma calculadora e algumas surpresas típicas de programador, agora precisamos aprender as como evitar erros básicos, e aprenderemos no próximo artigo as palavras reservadas da linguagem e que só devem ser usadas com indicadas.

## Fontes:

* https://cieda.com.br 
* https://pt.wikipedia.org/wiki/Guido_van_Rossum
* https://pt.wikipedia.org/wiki/Python
