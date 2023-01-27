---
title: Introdução ao Python - Boas Práticas
tags: [programacao, linguagens, python, apresentacao, introducao, basico, boas práticas]
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

Boas práticas são valorizadas por programadores sérios e empresas organizadas, portanto antes de começarmos a programar efetivamente com python vamos aprender a melhor forma de escrever seu código.

<!--more-->

O Python tem uma estrutura administratíva bem organizada, todo recurso, toda metodologia ou padrão adotado deve ser discutida com a comunidade Python, e isso é feito através de um mecanismo administrátivo chamado Python Enhancement Proposal - PEP. Através do PEP melhorias na linguagem são discutidas, e também algumas práticas como iremos apresentar aqui.

As boas práticas de codificação com a liguagem Python são definidos primordialmente pelo [PEP-8](https://peps.python.org/pep-0008/) (Style Guide for Python Code), mas isso não termina ai, a liderança de um projeto ou empresa pode vir a definir suas próprias boas práticas ampliando o PEP-8.

É importante ressaltar alguns mótivos que a própria PEP-8 sugere para não ser seguido boas práticas:

* Ao aplicar a diretriz tornaria o código menos legível, mesmo para quem está acostumado a ler código que segue a PEP-8.
* Para ser consistente com o código circundante que também o quebra (talvez por razões históricas) - embora esta também seja uma oportunidade de limpar a bagunça de outra pessoa (no verdadeiro estilo XP).
* Porque o código em questão é anterior à introdução da diretriz e não há outro motivo para modificar esse código.
* Quando o código precisa permanecer compatível com versões mais antigas do Python que não suportam o recurso recomendado pelo guia de estilo em vigência.

Python tem seu código fortemente estruturado com identações e são usadas para agrupar códigos em estruturas de controle. Portanto em alguns casos identação é obrigatório, veremos isso quando começarmos a criar código com Python.

Use sempre 4 espaços para identação, e evite usar `TAB` (tabulações diretamente), um código que se inicia com espaços na identação deve sempre usa-la.

Evite linhas maiores que 79 caracteres, mesmo que tenha aquele monitor gigante e curvo, o campo de visão humano lida melhor com esta limitação e vai evitar também que você tenha que ficar movimento sua cabeça para leitura do seu código.

Já as linhas de comentários e _docstrings_ devem ter no máximo 72 caracteres, isso está relacionado com as ferramentas de documentação.

Codifique seus arquivos sempre em UTF-8, e evite cacteres engraçados só porque são engraçados a não ser que deseja fazer alguma pegadinha em seu código, cuidado com nomes de como _z̯̯͡a̧͎̺l̡͓̫g̹̲o̡̼̘_ escritos em UNICODE, procure se ater aos caracteres ASCII padrão, mas você pode usar alguns caracteres como letras gregas que venham a facilitar o entendimento de suas fórmulas matemáticas, mas lembre-se que isso pode trazer dificuldades para outros programadores menos experiêntes.

Procure usar nomes em inglês para os identificadores (classes, variáveis e funções). Principalmente se pretende abrir seu código e terá uma audiência internacional.

{% highlight python %}
# Forma correta:

# Alinhar com delimitadores abertos.
foo = long_function_name(var_one, var_two,
                         var_three, var_four)

# Adicionar 4 espaços (um nível extra de indentação) para distiguir os argumentos do restante do código.
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)

# Os recuos suspensos devem adicionar ao menos um nível.
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)

# Forma incorreta:

# Argumentos na primeira linha proibidos quando não estiver usando alinhamento vertical.
foo = long_function_name(var_one, var_two,
    var_three, var_four)

# Recuo adicional necessário quando o recuo não é distinguível.
def long_function_name(
    var_one, var_two, var_three,
    var_four):
    print(var_one)
{% endhighlight %}    

Nas operações aritiméticas coloque o operador sempre depois da quebra de linha, sendo assim o primeiro caracter após a indentação, veja o exemplo.

{% highlight python %}
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
{% endhighlight %}

Para códigos de blocos de controle `if` que precisam usar mais de uma linha para sua construção, podemos adotar 3 formatos, eu particularmente prefiro o terceiro formato apresentado no exemplo.

{% highlight python %}
# Adicione uma identação extra as linhas adicionais 
# e coloque os operadores lógicos nas linhas seguintes.
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
{% endhighlight %}

Observem que, o operador lógico é colocado na linha seguinte, assim fica fácil depurar a estrutura lógica por um todo, e sua leitura fica bem intuitiva, já que cada linha será uma condição anexada com um operador lógico.

Quando for criar listas, sejam listas, tuplas, dicionarios ou listas de argumentos, procure fechar a lista em uma linha separa, veja o exemplo.

{% highlight python %}
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
    )
{% endhighlight %}


Barras invertidas (backslashes) podem ser adequadas às vezes. Por exemplo, instruções `with` longas e múltiplas são aceitáveis para esse caso.

{% highlight python %}
with open('/path/to/some/file/you/want/to/read') as file_1, \
     open('/path/to/some/file/being/written', 'w') as file_2:
    file_2.write(file_1.read())
{% endhighlight %}

Envolva funções de nível superior e definições de classe com duas linhas em branco.

As definições de método dentro de uma classe são cercadas por uma única linha em branco.

Linhas em branco extras podem ser usadas (com moderação) para separar grupos de funções relacionadas. Linhas em branco podem ser omitidas entre vários one-liners relacionados (por exemplo, um conjunto de implementações fictícias).

Use linhas em branco em funções, com moderação, para indicar seções lógicas.

Documentação e `docstrings` relativos ao módulo devem ser os primeiros no arquivo.

Procure usar um `imports` para cada módulo. Mas você pode importar membros do módulo na mesma linha.

{% highlight python %}
import os
import sys
from subprocess import Popen, PIPE
{% endhighlight %}

os `imports` devem ser agrupadas na seguinte ordem:

1. Importações de biblioteca padrão.
2. Importações de terceiros relacionadas.
3. Importações específicas de aplicativo/biblioteca local.

Use linhas em branco para separa-los.

Procure usar `imports` absolutos, isso facilita nas mensagens de erro e depuração.

Ao importar uma classe de um módulo que contém a classe, geralmente não há problema em escrever da seguinte forma:

{% highlight python %}
from myclass import MyClass
from foo.bar.yourclass import YourClass
{% endhighlight %}

Porem se houver conflitos de nomes use a forma:

{% highlight python %}
import myclass
import foo.bar.yourclass
{% endhighlight %}

“Dunders” de nível de módulo (ou seja, nomes com dois sublinhados iniciais e dois sublinhados finais), como `__all__`, `__author__`, `__version__`, etc., devem ser colocados após a docstring do módulo, mas antes de qualquer instrução de importação

{% highlight python %}
"""This is the example module.

This module does stuff.
"""

from __future__ import barry_as_FLUFL

__all__ = ['a', 'b', 'c']
__version__ = '0.1'
__author__ = 'Cardinal Biggles'

import os
import sys
{% endhighlight %}

Python não se importa se usar aspas duplas ou aspas simples, escolha uma e vá até o fim com elas, e use uma para evitar problemas em strings compostas com a outra.

## Espaços em branco

Evite espaços em brancos desnecessários nas seguintes situações:

* Imediatamente dentro de parenteses, cochetes e chaves:

{% highlight python %}
# Correto:
spam(ham[1], {eggs: 2})
# Errado:
spam( ham[ 1 ], { eggs: 2 } )
{% endhighlight %}

* Entre vírgula a direita e parenteses de fechamento logo a seguir

{% highlight python %}
# Correto:
foo = (0,)
# Errado:
bar = (0, )
{% endhighlight %}

* Imediatamente antes da virgula, ponto e virgula e dois pontos.

{% highlight python %}
# Correto:
if x == 4: print(x, y); x, y = y, x
# Errado:
if x == 4 : print(x , y) ; x , y = y , x
{% endhighlight %}

No entanto, em um _slice_, os dois pontos atuam como um operador binário e devem ter valores iguais em ambos os lados (tratando-o como o operador com a menor prioridade). Em um _slice_ estendida, ambos os dois-pontos devem ter o mesmo espaçamento aplicado. Exceção: quando um parâmetro de _slice_ é omitido, o espaço é omitido:

{% highlight python %}
# Correto:
ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
ham[lower:upper], ham[lower:upper:], ham[lower::step]
ham[lower+offset : upper+offset]
ham[: upper_fn(x) : step_fn(x)], ham[:: step_fn(x)]
ham[lower + offset : upper + offset]
# Errado:
ham[lower + offset:upper + offset]
ham[1: 9], ham[1 :9], ham[1:9 :3]
ham[lower : : upper]
ham[ : upper]
{% endhighlight %}

* imediatamente antes de abrir parenteses que inicia a lista de argumentos de uma chamada de função:

{% highlight python %}
# Correto:
spam(1)
# Errado:
spam (1)
{% endhighlight %}

* Imediatamente antes de abrir parentesis que inicia uma idexação ou _slicing_:

{% highlight python %}
# Correto:
dct['key'] = lst[index]
# Errado:
dct ['key'] = lst [index]
{% endhighlight %}

* Mais que um espaço em torno de um operador de atribuição (ou outro) para alinhar com outros:

{% highlight python %}
# Correto:
x = 1
y = 2
long_variable = 3
# Errado:
x             = 1
y             = 2
long_variable = 3
{% endhighlight %}

## Outras Recomendações

* Evite espaços em branco à direita em qualquer lugar. Como geralmente é invisível, pode ser confuso.
* Sempre coloque esses operadores binários com um único espaço em cada lado: atribuição `=`, atribuição aumentada (`+=`, `-=` etc.), comparações (`==`, `<`, `>`, `!=`, `<=`, `>=` , `in`, `not in`, `is`, `is not`), booleanos (`and`, `or`, `not`).
* Se forem usados operadores com prioridades diferentes, considere adicionar espaços em branco ao redor dos operadores com a(s) prioridade(s) mais baixa(s). Use seu próprio julgamento; no entanto, nunca use mais de um espaço e sempre tenha a mesma quantidade de espaço em branco em ambos os lados de um operador binário:

{% highlight python %}
# Correct:
i = i + 1
submitted += 1
x = x*2 - 1
hypot2 = x*x + y*y
c = (a+b) * (a-b)
# Wrong:
i=i+1
submitted +=1
x = x * 2 - 1
hypot2 = x * x + y * y
c = (a + b) * (a - b)
{% endhighlight %}

* Funções anotadas devem usar as regras normais para dois pontos e sempre ter espaços entre -> se presente.

{% highlight python %}
# Correto:
def munge(input: AnyStr): ...
def munge() -> PosInt: ...
# Errado:
def munge(input:AnyStr): ...
def munge()->PosInt: ...
{% endhighlight %}

* Não use espaços em torno do sinal `=` quando usado para indicar um argumento de palavra chave, ou quando usado para indicar um valor padrão para um parametro de função não anotado:

{% highlight python %}
# Correto:
def complex(real, imag=0.0):
    return magic(r=real, i=imag)
# Errado:
def complex(real, imag = 0.0):
    return magic(r = real, i = imag)
{% endhighlight %}

Ao combinar uma anotação de argumento com o valor default, no entanto, use espaços em torno do sinal `=`:

{% highlight python %}
# Correto:
def munge(sep: AnyStr = None): ...
def munge(input: AnyStr, sep: AnyStr = None, limit=1000): ...
# Errado:
def munge(input: AnyStr=None): ...
def munge(input: AnyStr, limit = 1000): ...
{% endhighlight %}

* Declarações compostas (várias instruções na mesma linha) geralmente são desencorajadas:

{% highlight python %}
# Correto:
if foo == 'blah':
    do_blah_thing()
do_one()
do_two()
do_three()

# Errado:
if foo == 'blah': do_blah_thing()
do_one(); do_two(); do_three()
{% endhighlight %}

* Embora às vezes não haja problema em colocar um if/for/while com um corpo pequeno na mesma linha, nunca faça isso para declarações com várias cláusulas. Evite também dobrar linhas tão longas! Melhor não:

{% highlight python %}
# Errado:
if foo == 'blah': do_blah_thing()
for x in lst: total += x
while t < 10: t = delay()
{% endhighlight %}

Definitivamente não faça:

{% highlight python %}
# Errado:
if foo == 'blah': do_blah_thing()
else: do_non_blah_thing()

try: something()
finally: cleanup()

do_one(); do_two(); do_three(long, argument,
                             list, like, this)

if foo == 'blah': one(); two(); three()
{% endhighlight %}

## Quando usar virgulas a direita

Tecnicamente é redundante usar os parentesis quando se cria tuplas de um só argumento, porém é preciso usar a virgula a direita.

{% highlight python %}
# Correto:
FILES = ('setup.cfg',)
# Errado:
FILES = 'setup.cfg',
{% endhighlight %}

Quando criando listas do tipo tupla ou não procure colocar cada item em uma linha própria, isso ajuda o sistema de versionamento a controlar adções futuras caso isso venha a ocorrer, coloque a virgula a direita na última linha também, assim o versionamento não identifica essa linha como alterada.

{% highlight python %}
# Correto:
FILES = [
    'setup.cfg',
    'tox.ini',
    ]
initialize(FILES,
           error=True,
           )
# Errado:
FILES = ['setup.cfg', 'tox.ini',]
initialize(FILES, error=True,)
{% endhighlight %}

## Comentários

Comentários que contrariam o código são piores do que a ausencia deles. Sempre mantenha os comentários atualizados com seu código.

Nunca altere a grafia de identificadores quando referido nos comentários.

O primeiro caracter na primeira palavra deve estar em maiúsculo, a não ser que seja um identificador que use minúsculo.

Comentários em blocos geralmente são compostos com um ou mais paragráfos, cada sentenção deve ser completa e finalizada com ponto.

Em comentários com multíplas sentenças deve-se usar dois espaços após o ponto final.

Certifique-se que os comentários são escritos de forma clara e compreencíveis para os falantes do idioma escolhido. Se possível use sempre o inglês.

### Comentários em blocos

Comentários em blocos dizem respeito a todo o código que o segue na mesma identação. Cada linha deve começar com um `#`, parágrafos são separados por uma linha em branco iniciada com `#`.

### Comentários em linha

Use comentários em linhas com moderação.

Um comentário em linha é um comentário na mesma linha do que uma declaração, o comentário deve ser separado da declação com pelo menos dois espaços em branco e inicia com `#`

## Comentários em Strings (docstring)

Conversões para docstring são apresentadas na [PEP-257](https://peps.python.org/pep-0257), num artigo futuro tratarei em detalhes este assunto.

* Docstrings devem ser escrito para cada módulo, função, classe e método que seja público, caso não sejam use comentários em blocos.

* Docstrings devem vir na linha após `def` para métodos e funções, para classes também a pós a primeira linha de declaração.

Observe que, o mais importante, o `"""` que encerra uma docstring multilinha deve estar em uma linha isolada:

{% highlight python %}
"""Return a foobang

Optional plotz says to frobnicate the bizbaz first.
"""
{% endhighlight %}

Para docstrings de uma linha, mantenha o fechamento `"""` na mesma linha:

{% highlight python %}
"""Return an ex-parrot."""
{% endhighlight %}

## Convenções de nomes (identificadores)

Novos módulos e pacotes (incluindo estruturas de terceiros) devem ser escritos de acordo com esses padrões, mas uma biblioteca já existente que tiver um estilo diferente, mantenha a consistência interna de preferência.

### Principio básico

Os nomes de identificadores públicos da API devem ser relativos ao seu uso e não a implementação do código em sí.

### Estílos de nomes

Há vários estilos adotados por programadores, usaremos como base o _CamelCase Style_.

Para classes use a primeira letra de cada palavra em maiúsculas.

Para variáveis, funções e métodos use a primeira palabra toda minúscula, e as demais com a primeira letra em maiúsculo.

Para constantes use todas as letras em maiúsculo, com as palavras separadas por underscore `_`.

Evite abreviações desnecessárias, ou identificadores extremamente longos, use o bom senso.

Para variáveis, funções e métodos privados use a convenção de iniciar os identificadores com um `_`, ao fazer um `from module import *` estes identificadores não serão importados.

Um underscore a direita `_` deve ser usado para evitar conflitos de nomes com palavras reservadas.

{% highlight python %}
tkinter.Toplevel(master, class_='ClassName')
{% endhighlight %}

_Magic Objects_ ou atributos não podem ser criados de forma arbitrária, use apenas os já documentados. por exemplo `__init__`, `__import__`,  `__file__` e `__version__`.

Você pode usar dois underscore `__` para tornar identificadores mais privados, não entraremos em detalhes no nível básico desta série sobre este recurso do Python (Name mangling).

## Outras convenções

### Nomes a serem evitados

Evite o uso de `l` (letra _éle_ minúsculo), `O` (letra _oh_ maiúsculo), I (letra _i_ maiúsculo), essas letras sozinhas podem ser confundidas com 0 e 1 em algumas fontes.

### Caracteres ASCII

Procure usar, em especial em identificadores públicos, caracteres restritos a tabela ASCII.

### Nomes de pacotes e módulos

Os nomes de pacotes e módulos devem ser escrito em minúsculo, e pode se usar underscore caso tenham mais de uma palavra

## Conclusão

Vimos um pouco sobre boas práticas, tal tema é bem extenso e pode gerar muito debate, e não é nosso proposito aqui, apenas buscamos apresentar superficialmente tais práticas, que serão amadurecidas com o aprendizado da linguagem, e cada projeto e empresa busca apresentar seu próprio manual que pode reforçar o que é proposto na PEP-8 entre outras, como também ampliar tais propostas.

## Fontes:

* https://barry.warsaw.us/software/STYLEGUIDE.txt
* http://www.wikipedia.com/wiki/CamelCase
* https://pt.wikipedia.org/wiki/Python
* https://en.wikipedia.org/wiki/Literate_programming
* https://stackoverflow.com/a/7456865/2766598
* https://www.geeksforgeeks.org/name-mangling-in-python/
* https://en.wikipedia.org/wiki/Name_mangling
* https://peps.python.org/pep-0001/
* https://peps.python.org/pep-0008/
* https://peps.python.org/pep-3131/
* https://peps.python.org/pep-0526
