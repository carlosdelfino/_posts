---
title: Introdução ao VCPkg e CMake - Instalação
tags: [Instalação, C, C++, Compilação, Biblioteca, CMake, CLang, VCPkg, Linux, Windows, MacOS, Instalação]
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

O Pacote de Idiomas "Inglês" deve estar instalado, observei que ao fazer a atualização do Visual Estúdio depois de instalado o **"Ferramentas de Build do C++"**, o VCPkg pode vir a se queixar da ausência do pacote de Idiomas, certifique-se que ambos estão completos, portanto tome cuidado para não confundir.

Certifique-se que os seguintes pacotes foram instalados nas suas versões atuais:

* MSVC bibliotecas com mitigação de espectro do VS 2019 C++ x64/x86
* MSVC ferramentas de build do VS 2019 C++ x64/x86
* SDK do Windows 10
* MFC do C++ para ferramentas de build e relacionados (x86 e x64)
* ATL do C++ para ferramentas de build e relacionados (x86 e x64)
* Recursos principais do C++
* Runtime do Windows Universal
* Ferramentas do CMake do C++ para Windows
* C++ Clang-cl para ferramentas de Build (x64/x86)
* Compilador do C++ Clang para Windows

Por hora não irei entrar em detalhes avançados quanto a preparação do ambiente, incluindo parametrização, e a instalação das ferramentas C e C++ já que não há nada de especial para uso com o VCPkg, seja no Windows, Linux ou MacOS, mas futuramente escreverei algo usando o Linux e outros detalhes especiais quanto a pacotes que exigem cuidados especiais.

Você precisa também que o Git, CMake e Clang estejam instalados, o CMake deve ser a partir da versão 3.12.4.

Vamos então a instalação do VCPkg, é preciso que você faça o clone do repositório disponível no GitHub, usarei meu Fork, mesmo não tendo contribuição alguma apenas para manutenção da URLs usadas.

Os comandos abaixo serão apresentados como sendo Windows, as variações para Linux e MacOS são simples, no caso de um arquivo `.bat` use a extensão `.sh` para tais. É importante observar que estou usando o PowerShell no Windows.

Atenção evite paths (estruturas de diretórios) longas, o ideal é instalar em diretório na raiz do sistema. casa seja necessário usar paths longos veja como usar o [comando Subst](https://docs.microsoft.com/pt-br/windows-server/administration/windows-commands/subst) para direcionar para um drive virtual.

``` PowerShell
> git clone https://github.com/Microsoft/vcpkg.git
> cd vcpkg

> .\bootstrap-vcpkg.bat
```

O processo de compilação e preparação do VCPkg pode levar um tempo, certifique-se que tudo correu bem sem nenhuma mensagem de erro. O resultado será similar ao explicado abaixo:

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

### Múltiplas Instalações e Personalizações

Você pode manter várias instalações do VCPkg, elas são chamadas instâncias, para isso basta clonar o repositório em diretórios diferentes conforme os projetos que você estiver trabalhando, evitando assim sobrecarregar sua lista de pacotes instalados. Cada instância não interfere na outras já que o VCPkg não interfere no path nem no registro ou variáveis.

Você pode usar a chave de comando `--vcpkg-root` apontando o diretório de referência do vcpkg para armazenar downloads e bibliotecas. Neste nível de uso do VCPkg é melhor fazer um novo clone para cada projeto e não coloque o vcpkg no path para evitar erros de referência.

Além disso você também pode personalizar o VCPkg para suas necessidades, mas não irei tratar sobre tais possibilidades por hora, deixarei isso para um artigo futuro.

## Instalando pacotes

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

Assim temos uma coleção interessante de pacotes a serem instalados, em fim use o comando:

``` PowerShell
.\vcpkg install opencv4 opencv4[contrib, ffmpeg, png, jpeg, qt]
```

Veja que ao começar a instalação, o vcpkg primeiro identifica as dependências necessárias e então lista todos os pacotes que serão instalados:

![Pacotes a serem instalados](/images/programacao/ccplusplus/vcpkg/vcpkg-install-opencv4-deps-1.png)
![Pacotes a serem instalados](/images/programacao/ccplusplus/vcpkg/vcpkg-install-opencv4-deps-2.png)

Você deve estar se perguntando por alguns pacotes estão sinalizados com um asterisco, estes pacotes são as dependências, foram identificados como necessários para uso ou compilação da biblioteca solicitada, vemos ai a maior utilidade do VCPkg.

Durante o inicio do download, por ser a primeira vez que utilizamos o VCPkg e sendo usado no PowerShell, são feitos alguns downloads extras:

![Downloads dos Pacotes de apoios](/images/programacao/ccplusplus/vcpkg/vcpkg-install-opencv4-downloads-1.png)

E em seguida são feitos os downloads de cada pacote e a compilação para o seu ambiente de desenvolvimento, veja que  pode haver uma mensagem de aviso se queixando da falta do pacote de idiomas, como citei logo no inicio, isso não irá causar problemas. Não tenha pressa, esta instalação irá demorar algumas horas, isso mesmo algumas horas, mas valerá a pena. (Vou aproveitar para preparar o próximo tutorial.)

O motivo da demora é devido a compilação de um pacote bastante complexo, por isso o escolhi, diante de sua  complexidade percebi-se fácilmente a utilidade do VCPkg.

O VCPkg prepara todo o pacote aplicando paths e ajustados as dependências na ordem necessária para que se tenha sucesso na compilação reduzindo imensamente o trabalho do desenvolvedor, permitindo que foque no código exclusivamente. Abaixo veja durante o processo a aplicação de paths:

![Paths sendo aplicados](/images/programacao/ccplusplus/vcpkg/vcpkg-install-opencv4-paths-1.png)

Outro fato interessante a ser observado éq ue fizemos a instalação de diversos pacotes deliberadamente, o que aumenta progressívamente o tempo já que envolve maior complexidade de dependências. Isso também torna mais complexo o entendimento das configurações que o próprio vcpkg propõem ao térmmino, como pode ser visto parcialmente na imagem abaixo.

![Lista de configurações que devem ser adicionadas ao seu projeto para uso das bibliotecas preparadas](/images/programacao/ccplusplus/vcpkg/vcpkg-install-opencv4-config-1.png)

Agora vamos novamente listar os pacotes que foram instalados e teremos a seguinte saída:

![Listando pacotes instalados no VCPkg](/images/programacao/ccplusplus/vcpkg/vcpkg-list-2.png)

## Arquiteturas

Quando instalamos um pacote o VCPkg instala o pacote padrão disponível que para Windows 32 bits, isso é visto no momento que o pacote é preparado, veja na lista acima que os pacotes tiveram seu nome pos-fixados com `:x86-Windows`.

Para baixar e preparar pacotes para outras arquiteturas como Windows 64 bits, basta informar ao vcpkg adicionado ao nome do pacote `:x64-windows` como no exemplo abaixo:

``` PowerShell
> .\vcpkg install opencv4:x64-windows
```

Ao listar os pacotes instalados, verá os pacotes listados cada um para a arquitetura identificada.

Você pode também usar a chave de comando --triplet para informar uma arquitetura padrão para todos os pacotes que serão instalados, assim para uma lista de pacotes a ser instalado basta adicionar a chave indicando a arquitetura padrão dos que não pos-fixados.

```PowerShell
> .\vcpkg install opencv4 sqlite3 zlib:x86-windows --triplet x64-windows
```

O comando acima irá instalar todos os pacotes como sendo para a arquitetura x64-windows, menos o pacote zlib que foi pós fixado para que seja instalado a versão x86-windows.

Num artigo especifico veremos detalhes sobre como criar configurações especificas para arquiteturas, tais configurações são chamadas de "Tripleto" e estão na pasta de nome `triplet` do VCPkg

## Atualizando

A Atualização do VCPkg é bem simples, e deve ser feita periodicamente, veja que é um projeto bem ativo, sugiro que seja feito inicialmente uma vez por semana.

Para atualiza-lo basta voltar a pasta do repositório e atualizado com o comando do git: `git pull`, caso haja novidades executar novamente o comando `.\bootstrap-vcpkg.bat`

Em seguida você deve atualizar os pacotes, executando o comando `.\vcpkg update` mantendo-se na pasta do repositório.

## Upgrade de pacotes

Os pacotes instalados podem ser atualizados com o comando `upgrade --no-dry-run`, que permite alguns parametros bem úteis descrito abaixo:

* --no-dry-run *se não for informado apenas lista os pacotes a serem atualizados.*
* --keep-going *Continua a atualização dos pacotes, mesmo se um falhar.*
* --triplet <t> *Informa o tripleto a ser usado (Arquitetura) para os pacotes que não foram informados pos-fixados*
* --vcpkg-root <path> *Especifica o diretório de instalação do vcpkg a ser usado em vez do diretório atual ou do diretório da ferramenta*

## Remoção de pacotes e do VCPkg

A remoção de qualquer pacote de biblioteca pode ser feito através do comando `remove`, assim basta digitar `./vcpkg remove sqlite3` para remover o pacote de nome *sqlite3*


## Referências

* [Documentação do VCPkg](https://vcpkg.readthedocs.io/en/latest/)
* [Solução para problemas de compilação do QT5-base](https://github.com/microsoft/vcpkg/issues/6833)
