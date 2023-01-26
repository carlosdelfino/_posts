---
title: Introdução ao Python - Típos de Variáveis
tags: [programacao, linguagens, python, apresentacao, introducao, basico, tipos de dados, tipos de variáveis,  inteiro, int, ponto flutuante, decimal, float, tipo complexo, complex, string, str, boolean, bool, list, tuple, dictionary, dic]
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

Em programação armazenamos dados em variáveis, e para isso algumas linguagens como o python permitem a existência de alguns típos especificos.

<!--more-->

O Python identifica o típo de sua variável quando o valor é atribuido a ela, e a variável é mutável entre tipos não precisando fazer um casting explicito para mudar o típo.

O Python tem os seguintes tipos de dados:

* Inteiro (int)
* Ponto Flutuante ou Decimal (float)
* Número Complexo (complex)
* Boolean (bool)
* String (str)
* List (list)
* Tuple
* Dictionary (dic)

Para saber qual o tipo de uma variável basta usar a função `type()`.

Vejamos cada um deles em detalhes.

## Típos númericos 

Temos três típos númericos nativos, são eles Inteiro (int), Ponto Flutuante (float) e Números Complexos (complex). 

Um recurso interessante no Python para representação numérica é a possibilidade de agrupar números usando o underscore (_), por exemplo um número como 22_000_444 seria a representação de 22,000,444, veja que a vírgula é usada para separar listas, portanto não pode ser usado como elemento agrupador.

### Inteiro

O tipo inteiro é um tipo númerico sem a parte fracional, composto por algarismos númericos e um sinal positivo ou negativo, no python 3, variáveis do tipo inteiro não tem limite de tamanho.

Há duas formas de criar uma variável do tipo inteiro, você pode informar diretamente o número da base 10 ou usar o contrutor `int()` informando ou não a base de origem do número.

{% highlight python %}
>>> ano = 2023
>>> lotacao = int(45)
>>> ausentes = -10
>>> participantes = int(1000, base=8)
>>> memaddr = int('5A4B', base=16)
>>> print(type(ano))
>>> print(type(2 * 2))
>>> print(type(2 / 3))
>>> print(type(2 // 3))
>>> print(participantes)
>>> print(memaddr)
{% endhighlight %}

![]({{site.url}}/images/programacao/python/tipos_de_dados/tipo_inteiro.gif)

Observe que ao fazer uma divisão com números inteiros no python, você tem como resultado um tipo `float`, para obter um restultado como inteiro você deve usar duas barras, quando usado o módulo (%) também é retornado um inteiro.

## Ponto Flutuante

O ponto flutuante é usado tanto para números fracionados simples como ponto flutuante propriamente, você pode construir números de ponto flutuante usando a notação seguida da parte inteira separada da parte fracionada por um ponto (.) ou pode usar uma representação de exponêncial (223.22E-10).

O ponto flutuante pode ser criado usando o construtor `float()` em casos especiais, como por exemplo um valor que precisa sofrer conversão (_casting_) de tipo, oriundo de outra varíavel do tipo string ou mesmo inteiro.

{% highlight python %}
>>> altura = 75.3
>>> temperatura = 27.5
>>> idade_do_sol = 4.603E9
>>> gravidade_do_sol = float(274)
>>> hexa_para_float = float(int('AB4D',base=16))
>>> print(altura, temperatura, idade_do_sol, gravidade_do_sol, hexa_para_float)
{% endhighlight %}

![]({{site.url}}/images/programacao/python/tipos_de_dados/tipo_float.gif)

## Números Complexos

Números complexos possuiem uma parte *real* e uma parte *imaginária*, sendo cada uma deles um número do tipo *float*. Para criar um número imaginário basta digita-lo como é formádo naturalmente, mas se for usar o construtor `complex()` não pode haver espaços, por exemplo `complex("5 + 8j")` é inválido e será lançado a exeção `ValueError`, o correto é `complex("5+8j"), mas se tiver criando o número complexo diretamente, não tem problemas haver espaços.

![]({{site.url}}/images/programacao/python/tipos_de_dados/tipo_complex.gif)

## Booleanos

Boleanos são valores `True` ou `False` em operações lógicas, quando convertidos para inteiro se tornam 1 e 0 respectivamente, qualquer valor númerico maior que 0 é verdadeiro. Qualquer Objeto é considerada verdadeiro, porém há casos que podem se tornar falso veremos como num contexto avançado. A constante `None` é considerado como falso, coleções, conjuntos e sequências vazias são considerado falso. Se um valor boleano for usado em operações aritiméticas serão tratados como 0 ou 1. 

## String (str)

Strings são sequências de caracteres, em python não existe o tipo caracter simples, apenas strings de caracteres, portanto se for fazer referência de um unico caracter se usa o mesmo formato que se usa para uma sequẽncia. Strings são similares a arrays e podem ser tratadas da mesma forma em algumas situações.

Uma string é delimitada com aspas simples, `'` ou aspas dúplas `"`, tanto faz. Também se pode definir multiplas strings uma por linha, usando três aspas seguidas.

{% highlight python %}
>>> texto = "um texto comum, string comum"
>>> outrotexto = "um outro texto"
>>> multiplas_linhas = """texto em multíplas linhas
... segunda linha
... terceira linha"""
>>> print(multiplas_linhas)
>>> somandostrings = texto + ", " + outrotexto
>>> print(soomandostrings)
{% endhighlight %}

![]({{site.url}}/images/programacao/python/tipos_de_dados/tipo_string.gif)

## List (list)

Listas são um tipo de sequência de dados usadas para armazenar uma coleção de objetos em uma única variável, uma lista pode conter diversos tipos diferentes. Podemos ter listas multidimencionais.

Listas podem conter 9.223.372.036.854.775.807, ou seja mais de 9 sextilhões de objetos.

Para saber com precisão quantos objetos podem conter numa lista use `sys.maxsize`.

Quando formos ver como usar o comando `for` veremos um pouco mais como manipular listas.

Listas são delimitadas por colchetes `[]` e cada objeto é separado por vírgula.


{% highlight python%}
>>> alturas = [22, 35, 58, 99]
>>> nomes = ['Maria', 'Antonia', 'Josefa']
>>> misturado = ['Maria', 26, 5+9j, alturas, nomes]
>>> print(alturas)
>>> print(nomes)
>>> print(misturado)
{% endhighlight %}

![]({{site.url}}/images/programacao/python/tipos_de_dados/tipo_lista.gif)

Veremos mais a frente, com detalhes, os objetos do tipo lista pois ele possuiem metódos e funções que ajudam na sua manipulação, assim teremos um artigo exclusivo para eles.

## Tuple

_Tuple_ são um tipo de sequência imutável, também usados para armazenador diferentes tipos de objeto, vejamos alguns exemplos.

{% highlight python %}
>>> nomes = 'Carlos', 'Rogerio', 'Valerio'
>>> idades = (35,84, 21, 18)
>>> planetas = tuple(['jupter', 'marte', 'terra', 'venus'])
>>> frase = tuple('isso é uma frase que vai se tornar tuple')
>>> print(nomes)
>>> print(planetas)
>>> print(frase)
{% endhighlight %}

![]({{site.url}}/images/programacao/python/tipos_de_dados/tipo_tuple.gif)

## Dictionary (dict)

Dictionary, ou `dict`, é um tipo mutável que mapeia valores 'hash', ou seja valores imutáveis para outros objetos, por exemplo você pode fazer um mapa entre nomes de pessoas e suas idades, posições numa reta númerica e alturas A representação de um dicionário é bem similar ao formato JSON, porém não são idênticos.

{% highlight python %}
>>> alturas = {'Maria': 33, 'Rogerio': 22, 'André': 55}
>>> raio_dos_planetas = {696_342: 'Sol', 69_911: 'Júpiter', 58232: 'Saturno', 25_362: 'Urano'}
>>> print(raio_dos_planetas)
{% endhighlight %}

![]({{site.url}}/images/programacao/python/tipos_de_dados/tipo_dicionario.gif)

## Conclusão

Vimos aqui superficialmente os tipos principais e mais usados do Python, no transcorrer de nosso aprendizado iremos ver como lidar com este típos, manipula-los, acessando seu conteúdo e alterando-os.

## Fontes:

* https://cieda.com.br 
* https://pt.wikipedia.org/wiki/Guido_van_Rossum
* https://pt.wikipedia.org/wiki/Python
* https://www.w3schools.com/python/python_strings.asp
* https://docs.python.org/3/library/stdtypes.html


