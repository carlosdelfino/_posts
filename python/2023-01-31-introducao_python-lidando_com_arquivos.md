---
title: Introdução ao Python - Lidando com Arquivos
tags: [programacao, linguagens, python, apresentacao, introducao, basico]
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

Umas das principais formas de se obter dados para o Python é através de arquivos estruturados como no formato CVS, iremos ver neste artigo como lidar com arquivos de forma bem simples e didática.

<!--more-->

Não usaremos neste artigo nenhuma ferramenta que possa tornar este trabalho mais simples, e elas existem, por exemplo o [Pandas]({{site.url}}/python/2023-01-23-introducao_python_o_modulo_pandas.md) que foi comentado brevemente numa postagem anterior, visto que nosso intuito é didático que queremos ir construindo o conhecimento sobre a linguagem no contexto mais básico.

A forma mais correta de abrir um arquivo é usando a estrutura de controle `with`, com ela temos um bloco que irá manipular o arquivo e quando o bloco finalizar este será automáticamente fechado, e caso algo dê errado, ele cuidará do processo para fechar-lo corretamente. Mas a abertura do arquivo é feita pela função `open()` que recebe alguns argumentos conforme suas intenções com o arquivo.

Vamos usar o  exemplo a seguir para entender como tudo isso funciona.

Baixe-o arquivo que usaremos em nossos estudos[clicando aqui](https://gist.githubusercontent.com/carlosdelfino/ac57eaef9b502c43895aa10dd7e40689/raw/d63fb4f81aa835b0a8ce60466b48b25e70e53323/COTAHIST_D12122022.TXT), este arquivo é um dos arquivos de cotação da bolsa de valores, usaremos apartir de agora arquivos reais da bolsa para nossos exemplos. Ao baixar o arquivo grave-o na pasta que está usando para seus estudos. No meu caso uso a pasta `$HOME/workspace/curso_python`, substitua o caminho para onde está guardando os arquivos deste curso.

{% highlight python %}
>>> with open('/home/carlosdelfino/workspace/curso_python/COTAHIST_D12122022.TXT',
...          'r',encoding='iso-8859-1') as cot:
...    conta_linhas = 0
...    for linha in cot:
...        conta_linhas = conta_linhas + 1
...        print(conta_linhas, ': 'linha)
{% endhighlight %}

O exemplo acima é bem simples, na primeira linha cria-se o bloco de códigos com `with` então o arquivo é aberto somente para leitura com codificação _iso-8859-1_, em seguida atribui o objeto arquivo a variável `cot`. 

O primeiro argumento é o nome do arquivo a ser aberto, pode-se informar o nome com o caminho completo ou relativo.

O segundo argumento informa como o arquivo será aberto, podemos abrir o arquivo como somente leitura `r`, somente escrita `w`, e leitura e escrita `r+`. Se não informamos o modo que o arquivo deve ser aberto será aberto como sendo somente leitura.

O terceiro argumento é nomeado como `encoding`, com ele informamos qual a codificação do arquivo, veja que se não informamos qual a codificação a função `open` usará a padrão do sistema operacional, UTF-8 para Linux e ISO-8859-1 para windows, no caso nosso arquivo foi criado no windows, mas estou usando o Linux, assim informei a codificação adequada.

As linhas subsequentes são identas pois fazem parte do bloco `with`.

A segunda linha defino uma variável que irá contabilizar o número de linhas.

Na terceira até a quarta linha defino um loop que procesa o arquivo, como pode ver na linha se cria o loop, uso uma estrutura onde o `for` trata o arquivo aberto como um objeto do tipo interable, estes tipos de objeto sempre que são acessados pelo `for` retonam o próximo valor como em uma fila, no caso de um objeto arquivo criado com `open` está pronto para ser usado como manipulado pelo `for`, a cada interação uma linha é retornada.

E finalmente ele contabiliza a linha e em seguida imprime seu conteúdo.

## Conclusão

Nosso objetivo neste artigo foi apresentar rapidamente como lidar com arquivos, em artigos futuros aprofundaremos no tema. 

Neste artigo você aprendeu a abrir o arquivo, a contar as linhas e finalmente obter seu conteúdo.

## Fontes:

* https://cieda.com.br 
* https://pt.wikipedia.org/wiki/Python
* https://docs.python.org/pt-br/3/tutorial/inputoutput.html#reading-and-writing-files
* https://gist.github.com/carlosdelfino/f17caa6c66cd2b96c5715edeb6b624a3
* https://gist.github.com/carlosdelfino/ac57eaef9b502c43895aa10dd7e40689
