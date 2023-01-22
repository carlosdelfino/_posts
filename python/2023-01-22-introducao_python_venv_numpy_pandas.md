---
title: Introdução ao Python - Virtual Enviroment, NumPy e Pandas
tags: [programacao, linguagens, python, apresentacao, introducao, basico, módulos, venv, numpy, pandas]
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

A melhor forma de se trabalhar com o Python é criando _Ambientes Virtuais_ onde são instalados versões especificas dos _módulos_, neste artigo falaremos sobre o que são estes _Ambientes Virtuais_ e o que são _Módulos_

<!--more-->
## Módulos o que são e como usa-los

O Python possui um sistema poderoso de extensão baseado em módulos que foi emprestado da linguagem _Modula 3_, este sistema permite a carga de código produzido por terceiros como pequenos arquivos ou frameworks completos, permitindo o reaproveitamento de código de forma muito fácil.

O Primeiro módulo que usuaremos é um módulo bem especifico e de uso bem restrito, chamado _venv_, este módulo permite a criação de ambientes virtuais de forma que cada ambiente tenha seu conjunto próprio de módulos em versões específicas.

## Ambientes Virtuais

O Python possui a capacidade de, digamos, enjaular diversos ambiente de execução, disponbilizando apenas as versões de módulos desejados, evitando riscos de conflitos de versões e principalmente o uso das versões desejadas. Podemos ter quantos ambientes seu dispositivo de armazenamento suportar.

Em cada Shell aberto pode haver um ambiente diferente ativo.

### Criando um Ambiente Virtual

Vamos criar uma pasta para nossos estudos, como uso Linux, irei apresentar os paths conforme o padrão Linux, adapte para o windows conforme sua necessidade.

Para criar um ambiente virtual usamos o módulo _"venv"_, para executa-lo digite o comando abaixo:

{% highlight bash %}
$ python -m venv ~/workspace/aulas_python/aula_4/venv
{% endhighlight %}

O comando acima se divide em três partes, a primeira `python`, nome do interpretador da linguagem Python, o segundo `-m venv`, chama o módulo _"Virtual Enviroment", e a terceira parte é o caminho de onde será armazenado os arquivos do novo ambiente virtual, veja que por prática comum, se nomea o diretório final como `venv` você pode usar outro nome e os caminhos como quiser desejar organizar estes ambientes. Eu particulamente crio um ambiente em cada pasta de projeto isso me ajuda a encontrar os ambientes facilmente. A desvantagem é que todos os ambientes acabam com o mesmo nome, mesmo estando em pastas diferentes.

Veja que o comando cuida de criar as pastas não existentes, cuidado ao digitar os nomes, pois se digitar uma pasta errado será assim mesmo criado.

O comando ao ser executado não emite mensagem alguma.

### Ativando o Ambiente Virtual

Para ativar o ambiente virtual use o comando abaixo:

{% highlight bash %}
$ source ~/workspace/aulas_python/aula_4/venv
{% endhighlight %}

![]({{ site.url }}/images/programacao/python/instalacao/Captura de tela de 2023-01-21 23-47-56.png)

## Instalando outros módulos

A instalação de novos módulos é bem simples, para isso utilizamos o comando `pip` que acompanha o framework da linguagem Python, sendo instalado juntamente com os demais recursos básicos da linguagem.

Certifique que o _ambiente virtual_ esteja ativo como foi esplicado acima, e digite o comando 

{% highlight bash %}
$ pip install numpy pandas
{% endhighlight %}

Este comando, como mostrado da imagem abaixo instala dois módulos que serão explicados em artigos a seguir a este em detalhes, veja na tela abaixo o processo, observe que o comando `pip` identifica as dependências e instala todos os módulos necessários.

Por questões didáticas informei no mesmo comando o módulo _NumPy_ e _Pandas_ (no plural, panda no singular é outro módulo), veja que, _NumPy_ é uma dependência de _Pandas_
![]({{ site.url }}/images/programacao/python/instalacao/Captura de tela de 2023-01-21 23-52-18.png)

## Fontes:

* https://cieda.com.br 
* https://pt.wikipedia.org/wiki/Python
* https://www.python.org/about/gettingstarted/
* https://wiki.python.org/moin/BeginnersGuide/Programmers
