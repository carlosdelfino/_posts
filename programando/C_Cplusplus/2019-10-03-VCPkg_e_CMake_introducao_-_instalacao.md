---
title: Introdução ao VCPkg e CMake - Instalação
tags: [C, C++, Compilação, Biblioteca, CMake, CLang, VCPkg, Linux, Windows, MacOS, Instalação]
categories: [programacao, ccplusplus]
layout: article
share: true
toc: true
comments: true
feature:
 category: true
 index: true
image:
 feature: embarcados/fpga/de0-nano/de0-nano.jpg
 teaser: embarcados/fpga/de0-nano/de0-nano.jpg
ads: 
 show: true
tagcloud: true
coinbase:
 show: false
---

A programação em C e C++ deixou de ser um mito e uma programação avançada acessível para poucos, hoje com a disseminação de frameworks, bibliotecas, IDEs, compiladores gratuitos e gerenciadores de pacotes, ficou tudo muito mais simples, porém ainda há algumas pequenos macetes e documentações superficiais.

<!--more-->

Pesquisando sobre programação com o VSCode e a Linguagem C e C++, me deparei com o VCpkg que deveria ser instalado para uso do VSCode, confesso que não me aprofundei porque isso seria necessario, mas me chamou a atenção esta nova ferramenta e procurei testa-la para ver no que ela poderia me ajudar.

Realmente, é uma nova ferramenta bem útil, que permite e facilita a instalação de bibliotecas C e C++ e a integração com CMake reduzindo bastante o trabalho de configuração. O VCPkg ajuda a manter as Bibliotecas e seus fontes em C e C++ atualizadas e organizadas no Windows, Linux e MacOS.

Mas nem tudo é tão plug-and-play assim. É preciso se atender a pequenos detalhes que estou descobrindo e vou anotar nesta série de artigos relacionados a ela.

## Instalação

A instalação do VCPkg é bem simples, e bem ao estilo "programador", já que usa um repositório Git para obter o fonte que deve ser compilado e integrado ao seu ambiente de desenvolvimento. Mas isso, é bem simples de ser feito e não parece haver problemas desde que os requisitos mínimos estejam instalados.

Primeiro certifique-se que o Visual Studio em sua ultima versão esteja bem instalado conforme instruções da Microsoft, Você pode usar a partir da versão 2015 Update 3.

O pacote **"Ferramentas de Build do C++"** e o Pacote de Idiomas "Inglês" deve estar instalado, observei que ao fazer a atualização do Visual Estúdio depois de instalado, o VCPkg pode vir a se queixar da ausência do pacote de Idiomas, isso não causa problemas, eu não identifiquei como remover tal mensagem de alerta. Ao selecionar as Ferramentas de Build o C++ certifique-se que o SDK do Windows, o CMAke e CLang estejam selecionados e sejam instalados corretamente.

Por hora não irei entrar em detalhes quanto a preparação do ambiente e a instalação das ferramentas C e C++ já que não há nada de especial para uso com o VCPkg, seja no Windows, Linux ou MacOS, mas futuramente escreverei algo usando o Linux.

Você precisa também que o Git e o CMake estejam instalados, o CMake deve ser a partir da versão 3.12.4.

Vamos então a instalação do VCPkg, é preciso que você faça o clone do repositório disponível no GitHub, usarei meu Fork, mesmo não tendo contribuição alguma apenas para manutenção da URLs usadas.

Os comandos abaixo serão apresentados como sendo Windows, as variações para Linux e MacOS são simples, no caso de um arquivo `.bat` use a extensão `.sh` para tais. É importante observar que estou usando o PowerShell no Windows.

``` PowerShell
> git clone https://github.com/Microsoft/vcpkg.git
> cd vcpkg

> .\bootstrap-vcpkg.bat
```

O processo de compilação e preparação do VCPkg pode levar um tempo, certifique-se que tudo correu bem sem nenhuma mensagem de erro. O resultado será uma tela similar a baixo:

![Inicio da execução do VCPkg Bootstrap](/images/programacao/ccplusplus/vcpkg/vcpkg-bootstrap-1.png)

Serão listado alguns arquivos com extensões `.cpp`, algumas mensagens e finalmente a mensagem de sucesso:

![Finalização bem sucedida do comando VCPkg Bootstrap](/images/programacao/ccplusplus/vcpkg/vcpkg-bootstrap-2.png)

### Integrando com o Ambiente de Desenvolvimento

Não irei entrar em muitos detalhes quanto a integração com o ambiente de desenvolvimento, porém saiba que tal passo é fundamental para o perfeito funcionamento do VCPkg, até mesmo porque a instalação do VCPkg se resume a este passo depois da compilação.

Tal integração parametriza seu ambiente para que sejam usadas as vária de compilação de forma adequada, incluindo as bibliotecas corretamente e para que o CMake as encontre sem grandes surpresas, quando isso for possível.

Ainda dentro da pasta onde foi feito o clone do repositório digite agora os seguintes comandos:

``` PowerShell
> ./vcpkg.exe integrate install
> ./vcpkg.exe integrate powershell
```

O primeiro torna disponível a todos os usuários do MSBuild C++ as bibliotecas gerenciadas pelo VCPkg de forma automática, bastando então usar a diretiva `#include`. Caso esteja usando o **CMake**, deve ser adicionado a diretiva como no exemplo abaixo, veja que usei o caminho da minha instalação, portanto `c:/Users/Admin/workspace/vscode/vcpkg` deve refletir o path de seu repositório:

``` PowerShell
> CMake "-DCMAKE_TOOLCHAIN_FILE=C:/Users/Admin/workspace/vscode/vcpkg/scripts/buildsystems/vcpkg.cmake"
```

Já o segundo adiciona ao PowerShell a habilidade de autocompletar para o VCPkg.

## Instalando um pacote

Para finalizar esta primeira parte de nosso pequeno tutorial, vamos instalar um pacote, usaremos como exemplo o OpenCV que nos grandes possibilidades de uso, somente na próxima publicação desta série é que iremos ver como usar o OpenCV com o VCPkg, neste momento veremos apenas como instala-lo.

Antes de tudo vamos ver se já há algum pacote instalado, para isso usaremos o comando `list`, para isso basta digitar: `.\vcpkg list`, pronto temos listado os pacotes já instalado, porém ainda não instalamos nenhum pacote, então a lista deve estar vazia como apresentado abaixo:

![Listando pacotes instalados no VCPkg](/images/programacao/ccplusplus/vcpkg/vcpkg-list-1.png)

Para encontrarmos o nome exato do pacote a ser instalado podemos usar o comando `search`, assim digitaremos o comando seguido do nome que desejamos procurar `.\vcpkg search opencv`, e teremos uma saida relativamente longa, já que temos além vários pequenas bibliotecas, temos também diferentes versões, então vamos reduzir a listagem usando o seguinte nome `opencv4`, ficando o comando assim: `.\vcpkg search opencv4` e teremos uma saida  simmilar a seguinte:

![Usando o VCPkg para listar os pacotes disponíveis](/images/programacao/ccplusplus/vcpkg/vcpkg-search-opencv4.png)

Dos pacotes listados iremos selecionar os seguintes pacotes:

* opencv4
* opencv4[contrib]
* opencv4[ffmpeg]
* opencv4[png]
* opencv4[jpeg]
* opencv4[qt]

Assim temos uma coleção interessante de pacotes a serem instalados, para instala-los use o comando:

``` PowerShell
.\vcpkg install opencv4 opencv4[contrib] opencv4[ffmpeg] opencv4[png] opencv4[jpeg] opencv4[qt]
```

Veja que ao começar a instalação, o vcpkg primeiro identifica as dependências necessárias e então lista todos os pacotes que serão instalados:

![Pacotes a serem instalados](/images/programacao/ccplusplus/vcpkg/vcpkg-install-opencv4-deps-1.png)
![Pacotes a serem instalados](/images/programacao/ccplusplus/vcpkg/vcpkg-install-opencv4-deps-2.png)

Você deve estar se perguntando por alguns pacotes estão sinalizados com um asterisco, estes pacotes são as dependências, foram identificados como necessários para uso ou compilação da biblioteca solicitada, vemos ai a maior utilidade do VCPkg.

Durante o inicio do download, por ser a primeira vez que utilizamos o VCPkg e sendo usado no PowerShell, são feitos alguns downloads extras:

![Downloads dos Pacotes de apoios](/images/programacao/ccplusplus/vcpkg/vcpkg-install-opencv4-downloads-1.png)

E em seguida são feitos os downloads de cada pacote e a compilação para o seu ambiente de desenvolvimento, veja que ao iniciar o ambiente de compilação, pode haver uma mensagem de aviso se queixando da falta do pacote de idiomas como citei logo no inicio, isso não irá causar problemas. Não tenha pressa esta instalação irá demorar algumas horas, isso mesmo algumas horas, mas valerá a pena. (Vou aproveitar para preparar o próximo tutorial.)

Agora vamos novamente listar os pacotes que foram instalados e teremos a seguinte saída:

![Listando pacotes instalados no VCPkg](/images/programacao/ccplusplus/vcpkg/vcpkg-list-2.png)

## Arquitetura

Quando instalamos um pacote o VCPkg instala o pacote padrão disponível que para Windows 32 bits, isso é visto no momento que o pacote é preparado, veja na lista acima que os pacotes tiveram seu nome pos-fixados com `:x86-Windows`.

Para baixar e preparar pacotes para outras arquiteturas como Windows 64 bits, basta informar ao vcpkg adicionado ao nome do pacote `:x64-windows` como no exemplo abaixo:

``` PowerShell
> .\vcpkg install opencv4:x64-windows
```

Ao listar os pacotes instalados, verá os pacotes listados cada um para a arquitetura indentificada.

## Atualizando

A Atualização do VCPkg é bem simples, e deve ser feita periodicamente, veja que é um projeto bem ativo, sugiro que seja feito inicialmente uma vez por semana.

Para atualiza-lo basta voltar a pasta do repositório e atualizado com o comando do git: `git pull`, caso haja novidades executar novamente o comando `.\bootstrap-vcpkg.bat`

Em seguida você deve atualizar os pacotes, executando o comando `.\vcpkg update` mantendo-se na pasta do repositório.

## Referências

* [Documentação do VCPkg](https://vcpkg.readthedocs.io/en/latest/)