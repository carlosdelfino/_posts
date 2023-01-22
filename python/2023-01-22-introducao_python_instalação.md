---
title: Introdução ao Python - Instalação
tags: [programacao, linguagens, python, apresentacao, introducao, basico, install, instalacao]
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

Como instalar o Python no Linux e Windows

<!--more-->

Tal iniciativa se dá com parceria com o projeto [CIEDA](https://cieda.com.br) desenvolvido por Roberto Placido, conhecido como *Beto Byte*, 

# Instalando no Linux

A instalação no Linux em muitas versões  é desnecessária pois já vem instalado por padrão, já que é utilizado para suporte de outras ferramentas escritas na linguagem. Mas se por algum motivo vier a ser necessário é bem simples, irei usar como referência o Ubuntu 22.04, para tal basta digitar o comando a seguir:

{% highlight bash %}
$ sudo apt install python3
{% endhighlight %}

# Instalando no Windows

A Instalação no Windows já exige alguns passos a mais, o primeiro deles é visitar o site [https://python.org](https://python.org) e clicar no botão Download, normalmente o site já identifica o seu sistema operacional e lhe oferece o download mais indicado ao seu sistema operacional, em caso de dúvidas compartilhe nos comentários deste post.

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_13_37.png)

Quando o Download terminar vá na pasta de download de seu navegador ou na lista de downloads e execute o instalador.

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_17_05.png)

Na primeira janela, marque a opção _"Add python.exe to PATH"_, em seguida clica em *Install Now*, aguarde a próxima janela.

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_24_18.png)

Será perguntado se deseja permitir que o aplicativo altere seu dispositivo, é preciso clicar no _Sim_.

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_25_52.png)

Ao fim do processo de instalação será apresentado a seguinte tela, nela clique em _Disable Path Length Limit_.

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_38_01.png)

Novamente será consultado se deseja permitir alterações no seus dispositivo, é preciso clicar em _Sim_.

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_39_02.png)

Então a instalação estará concluida, basta clicar no botão _Close_.

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_39_38.png)

Agora para garantir que todo o processo de instalação foi um sucesso, vá no campo pesquisar ao lado do botão Iniciar do Windows, e digite "Path", vamos com isso verificar se o Path está configurado corretamente.

Clique em _"Editar as Variáveis de ambiente do sistema"

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_41_27.png)

Na janela que abrir clique no botão "Variáveis de Ambiente..."

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_41_46.png)

Na janela que abrir clique duas vezes em _"PATH"_ na lista de _"Variáveis do usuário"_

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_42_41.png)

Confirme que o Path de instalação do python seja os primeiros da lista.

![]({{site.url}}/images/programacao/python/instalacao/VirtualBox_Windows Python_21_01_2023_21_42_59.png)

Pronto seu windows está pronto para o próximo passo com o Python, no artigo a seguir instalaremos nosso ambiente virtual do Python para nossos exercícios.


## Fontes:

* https://cieda.com.br 
* https://pt.wikipedia.org/wiki/Python
* https://www.python.org/about/gettingstarted/
* https://wiki.python.org/moin/BeginnersGuide/Programmers
