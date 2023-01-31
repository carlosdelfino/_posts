---
title: Introdução ao Python - Lidando com Porta Serial
tags: [programacao, linguagens, python, apresentacao, introducao, basico, porta serial, serial, pyserial, arduino]
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

Uma outra forma de obter dados para processar com o Python é através da porta serial, por exemplo usando o Arduino, iremos ver neste artigo, de forma breve, como ler a porta serial.

<!--more-->

Neste artigo vamos supor que você saiba usar o Arduino, caso não saiba, solicite nosso curso de *[Introdução ao Arduino](https://www.superprof.com.br/aulas-introdutoria-intermediarias-programacao-aulas-praticas-sem-enrolacao-exemplos-reais.html)*

Você também irá precisar do Shield com LCD para o Arduino como da imagem abaixo para este exercício. Clique na imagem pra compra-lo.

[![]({{site.url}}/images/arduino/lcd/shield_lcd_adc_button.png)](https://s.click.aliexpress.com/e/_DmJKctV)

Iremos considerar também que o [código disponível neste link](https://gist.githubusercontent.com/carlosdelfino/ac57eaef9b502c43895aa10dd7e40689/raw/cb89a82f7e99ebf5268040ebb86dc1882e5bb3c0/SerialLCD.ino) está gravado em seu arduino.

O Python precisa de um módulo especial para ter acesso a porta serial, portanto vamos ativar nosso ambiente virtual para que tenhamos um ambiente limpo e não encha de pacotes a sua instalação padrão.
Se não sabe ou não lembra [como ativar seu ambiente virtual volte no artigo clicando aqui]({% post_url /python/2023-01-22-introducao_python_venv_numpy_pandas %}).

Instale o módulo `Serial` com o comando `pip install serial`.

No python o acesso a porta serial é bem similar ao acesso a arquivos comuns, sendo a unica diferença os parâmetros que são passados para preparar a porta serial, no nosso código do Arduino preparamos a porta para seguir a codificação padrão com velocidade de 9600 bps (bits por segundo), sendo assim precisamos usar esta mesma parametrização quando abrirmos a porta no computador.

Outra informação importante é o nome da porta, o que é diferente no windows e linux, no windows as portas seriais são composta pela abreviação `com` seguida do número da porta, típicamente um número maior ou igual a 5, no linux, varia conforme a distribuição e o kernel usado, mas independente do sistema operacional basta consultar a IDE do Arduino. No meu ambiente a porta tem o nome `/dev/ttyACM0`, veja a imagem abaixo.

![]({{site.url}}/images/programacao/python/leitura_arquivos/descobrindo_porta_serial_arduino.gif)

Vejamos um exemplo do código em Python.

{% highlight python %}
>>>with serial.Serial('/dev/ttyACM0', 9600, timeout=1) as ser:
...    cod = 0
...    while not cod > 700:
...        for x in ser:
...            if x:
...                cod = int(x)
...                if cod > 700:
...                    break
...                print(f'Código: {cod}')
... 
{% endhighlight %}

![]({{site.url}}/images/programacao/python/leitura_arquivos/acessando_porta_serial.gif)

No código acima, como é feito na leitura de arquivos temos o primeiro parâmetro que é o nome da porta a ser aberta, em seguida temos a valocidade em _bps_, e finalmente o parâmetro `timeout` que informa quanto tempo esperar pela disponibilidade de dados na porta. Se `timeout` não for informado será esperado até que haja algum dado na porta.

Em seguida temos o bloco de código que será executado no contexto da declaração `with` com a variável `ser` disponível com o objeto de controle da porta serial.

Na primeira linha do bloco criamos a variável que irá conter o código em formato `int`.

Em seguida cria um loop do tipo `while` que será executado enquanto `cod` for menor ou igual a 700.

A instrução for a seguir apresenta um recurso bastante interessante do python, para uso em objetos especializados em IO (entrada e saída de dados),  este formato funciona em arquivos comuns abertos com a construtor (função) `open`, como também com o `serial.Serial`. Com essa declaração será similar a chamar a função `readline()` e atribuir o seu retorno para `x` continuamente, e a cada vez executa o bloco pertencente ao `for`.

Então ele verifica se `x` contém algum conteúdo, pois com o `timeout` atigido, retorna vazio.

Havendo valor para `x` é convertido em inteiro como é verificado se ele é maior que `700`, se sim interrompe o loop `for`, como a condição para o loop `while` é `cod` não ser maior que `700` este loop também é naturalmente interrompido.

A linha do `print`, apresenta uma forma diferente de formatar string, prefixando a string com `f` as variáveis são acessadas internamente quando contornadas por chaves `{}`.

Outra forma de escrever este código é eliminando o `while` e o `timeout` tornando o código mais simples, tente e comente como seu resultado.

## Conclusão

Apredendos aqui como acessar a porta serial, fizemos apena leituras, veremos em outros artigos como escrever na porta, caso se interesse em aprender mais, entre em [contato e solicite uma aula.](https://www.superprof.com.br/profissional-com-anos-experiencia-programacao-aulas-introducao-linguagem-python.html)


## Fontes:

* https://cieda.com.br 
* https://pt.wikipedia.org/wiki/Python
* https://pyserial.readthedocs.io/en/latest/shortintro.html
