---
title: Introdução ao Python - Estruturas de Controle - If e Else
tags: [programacao, linguagens, python, apresentacao, introducao, basico, estruturas de controle, controle de fluxo, if, else]
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

Estruturas de controle if else permitem que seja escolhido um bloco de código a ser executado conforme o resultado de uma condição lógica.

<!--more-->

Estruturas de controle `if else` podem ser construidas com um, dois ou mais blocos a serem executados conforme determinada condição lógica, se uma determinada condição é falsa, ele pode verificar outras condições ou executar um bloco padrão caso nenhuma condição seja atendida.

Vejamos um exemplo simples, onde uma condição é verificada.

{% highlight python %}
>>> idades = {
...  'Jose': 22, 
...  'Patricia': 33, 
...  'Antonio': 56, 
...  'Pedro': 16, 
...  'Carlos': 18,
...  'Claudio': 8
...  }
>>> for nome in idades:
>>>     if idades[nome] >= 18:
>>>         print("{} pode apostar na loteria!".format(nome))
{% endhighlight %}

![]({{site.url}}/images/programacao/python/estruturas_de_controle/constrole_if.gif)

Vamos ampliar o exemplo anterior e ver o uso da clausula `else` que executado caso nenhuma declaração seja verdadeira.

{% highlight python %}
>>> for nome in idades:
...     if idades[nome] >= 18:
...         print("{} pode apostar na loteria!".format(nome))
...     else:
...         print("{} não tem idade para apostar!".format(nome))
...
>>>
{% endhighlight %}

Caso precise fazer uso de diversos blocos de decisão você pode usar `elif` para testar mais condições caso a declaração anterior não seja verdadeira.

{% highlight python %}
>>> if idades['Antonio'] < idades['Patricia']:
...     print("Antonio é mais jovem que Patricia.")
... elif idades['Patricia'] < idades['Jose']:
...     print("Patricia) é mais jovem que José.")
... elif idades['Pedro'] > idades['Claudio']:
...     print("Pedro é mais velho que Claudio.")
... else
...     print("Nenhuma das afirmações anteriores é verdadeira")
{% endhighlight %}

## Conclusão 

Vimos aqui um pouco sobre o uso das estruturas de controle de fluxo, `if`, `elif` e `else`, existem outras questões que são relevantes porém não neste exato momento e veremos no transcorrer da publicação de novos artigos e conforme o tema se tornar mais avançado.

Veremos a seguir a estrutura de controle mach
## Fontes:

* [https://cieda.com.br](https://cieda.com.br)
* https://docs.python.org/3/reference/compound_stmts.html
* https://medium.com/mlearning-ai/when-and-why-to-use-over-in-python-b91168875453
