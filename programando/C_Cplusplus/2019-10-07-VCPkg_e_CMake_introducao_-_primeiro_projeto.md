---
title: Introdução ao VCPkg e CMake - Primeiro Projeto
tags: [Primeiro Projeto, C, C++, Compilação, Biblioteca, CMake, CLang, VCPkg, Linux, Windows, MacOS, OpenCV]
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

OpenCV tem sido uma das bibliotecas OpenSource mais utilizadas da atualidade, com ela podemos fazer pequenos ajustes em imagens em práticamente formato, como também reconhecimento facial e de objetos através de redes neurais e outros algoritmos avançados. Veremos neste artigo como usar o VCPkg e o CMake para criar um pequeno projeto de exemplo.

<!--more-->

Para ter sucesso neste projeto você precisa ler o artigo e ter certeza que o VCPkg está instalado corretamente. Caso ainda não tenha lido o [Artigo Anterior Introdutório ao VCPkg]({% post_url /programando/C_Cplusplus/2019-10-03-VCPkg_e_CMake_introducao_-_instalacao %})

É importante lembrar que a compilação é um processo demorado, ainda mais em máquinas de baixo desempenho ou que tenha muitos programas em execução simultaneamente, procure fechar todos os programas que não venha a precisar.

Outra coisa importante que pode lhe atrapalhar, infelizmente, são os antivirus, que podem sempre dar alertas (falsos positivos) conforme novos executáveis vão sendo gerados.

## Instalando Pacotes

Vamos então rever o processo de instalação de um pacote, porem desta vez iremos instalar um pacote focado na arquitetura x64, já que meu Windows 10 64bits.

Certifique-se que os pacotes individuais do Visual Studio ou do MSBuild foram instalados nas suas versões atuai. Lembre-se que o path de instalação do vcpkg não deve ser longo, já que pode ocorrer problemas com algumas ferramentas de compilação. Caso tenha que instalar o VCPkg em um path longo, use o comando `subst` para mapear tal path para um driver.

Para o OpenCV, faça a instalação da biblioteca com o seguinte comando, porém desta vez aponte a Arquitetura através do que é chamado *triplet*(veremos mais destalhes deste conceito no próximo artigo):

``` PowerShell
> %PATH_VCPKG_INSTALL%\vcpkg install opencv4 --triplet x64-windows
```

VCPkg irá aproveitar tudo já foi baixado da internet conforme possível, e irá gerar uma nova biblioteca conforme as configurações do **triplet** informado.

## Criando a base de nosso projeto

Lembre-se as ferramentas de compilação não ficam muito seguras de trabalhar com paths que possuem espaço, caracteres especiais e também que sejam muito longas.

Vou usar o diretório `c:\users\Admin\workspace\vscode\primeiro_projeto_opemcv_vcpkg`, mas como o path é muito longo, vou mapeá-lo para a unidade de disco `O:`.

``` PowerShell
> subst O: c:\users\Admin\workspace\vscode\primeiro_projeto_opemcv_vcpkg
```

No seu diretório escolhido crie uma basta `build` e outra `src`,

``` PowerShell
> cd O:
> mkdir build
> mkdir src
```

A instância do VCPkg que vou usar está na unidade virtual `V:`.

### Criando meu código C inicial

Abaixo está o código que deve ser usado como exemplo, o mesmo código estará [atualizado no gist, basta clicar neste link](https://gist.github.com/carlosdelfino/7bb930a705386659aa6c433c4f90d324)
Crie um arquivo main.cpp com o código abaixo na pasta mapeada para o Drive `O:` e no subdiretório `src` (`o:\src`):

``` C++
#include <opencv2/opencv.hpp>
#include <iostream>

int main()
{
	cv::namedWindow("raw", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("gray", cv::WINDOW_AUTOSIZE);
	cv::namedWindow("canny", cv::WINDOW_AUTOSIZE);

	cv::VideoCapture cap;
	cap.open(0);

	if (!cap.isOpened())
	{
		std::cerr << "Couldn't open capture." << std::endl;
		return -1;
	}
	
	cv::UMat bgr_frame, gray, canny;

	for (;;) 
	{
		cap >> bgr_frame;
		if (bgr_frame.empty()) break;

		cv::imshow("raw", bgr_frame);

		cv::cvtColor(bgr_frame, gray, cv::COLOR_BGR2GRAY);
		cv::imshow("gray", gray);

		cv::Canny(gray, canny, 10, 100, 3, true);
		cv::imshow("canny", canny);

		char c = cv::waitKey(10);
		if (c == 27) break;
	}

	cap.release();
	return 0;
}
```

E na mesma pasta `o:\src`, crie um arquivo `CMakeLists.txt` com o código abaixo:

``` CMake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.0)
project("Hello World OpenCV com VCPkg e CMake")

find_package(OpenCV CONFIG REQUIRED)

add_executable(main main.cpp)

target_link_libraries(main PRIVATE opencv_core opencv_imgproc opencv_videoio opencv_highgui)
```

## Obtendo referências

Para obter as referências que devem ser usadas com o CMake, basta usar o comando:

``` PowerShell
> %PATH_VCPKG_INSTALL%\vcpkg integrate install
```

E você terá como retorno uma mensagem como abaixo:

![Integrando o VCPkg](/images/programacao/ccplusplus/vcpkg/vcpkg-integrate-install-powershell.png)

Veja que na ultima linha ela apresenta o parâmetro a ser usado com o CMake `-DCMAKE_TOOLCHAIN_FILE=V:/scripts/buildsystems/vcpkg.cmake`, caso você esteja usando o MSBuild não será preciso fazer nada mais.

Você também poderá trabalhar internamente no VSCode ou mesmo no Eclipse, basta ao criar seu projeto fazer as devidas referências a pasta do VCPkg, assim ele irá não só encontrar os cabeçalhos das bibliotecas como também irá lhe permitir navegar entre elas.

## Preparando o build

Agora vamos preparar o build de nosso novo projeto usando o CMake. Entre na pasta `build` e digite o comando abaixo:

``` PowerShell
> cd build
> %PATH_CMAKE_INSTALL%\cmake ..\src -A x64 -DCMAKE_TOOLCHAIN_FILE=V:\scripts\buildsystems\vcpkg.cmake
```

A variável usada no inicio da linha de comando, aponta onde o CMake foi instalado (PATH_CMAKE_INSTALL), é apenas para exemplificar que se deve referênciar o path onde foi instalado o CMake que se deseja usar, ele pode estar no PATH do sistema, mas eu prefiro não faze-lo assim, ou quando é meu ambiente de trabalho fixo eu faço scripts que ajustam o PATH e as variáveis de ambiente necessárias.

Em seguida temos o comando *cmake*, depois o caminho para o *source*, a arquitetura que se deseja utilizar, informada logo após a diretiva `-A` no caso x64, e finalmente a diretiva de compilação que aponta o arquivo de apoio do VCPkg. como resultado temos a tela a seguir:

![Preparando o build](/images/programacao/ccplusplus/vcpkg/cmake-prepare-build-1.png)

O processo acima, cria um diretório de build que funciona como um cache das informações que serão usadas na compilação do programa, você pode criar várias pastas para o *build* de sua aplicação, cada uma usando um conjunto de parâmetros específicos, mas, se você quiser ajustar algum parâmetro, remova a pasta build a recrie novamente.

Finalmente usamos o comando a seguir para compilar, dentro do diretório `build`, no meu caso `o:\build`:

``` PowerShell
> %PATH_CMAKE_INSTALL%\cmake --build .
```

![Compilando (build) o aplicativo](/images/programacao/ccplusplus/vcpkg/cmake-build-1.png)

Observe que o build final é feito dentro do diretório que foi populado pelo comando anterior, para não ter dúvida no meu caso foi o diretório `o:\build`.

Agora vamos o que conseguimos, para isso veja se foi criado um diretório chamado `Debug` dentro do diretório de cache do build, no caso seria `o:\build\Debug`, lá você verá que o cmake compilou e gerou tanto o seu executável como as dlls necessárias para o perfeito funcionamento do aplicativo no windows. Inclusive os arquivos com assinaturas para depuração.

No futuro veremos mais detalhes para compilarmos a aplicação para distribuição final.

Vamos então executar a aplicação, para isso basta chamar o executável criado na pasta `Debug`, que deve ser `main.exe` conforme nossa parametrização no arquivo `CMakeList.txt`.

Veja como ficou no meu computador:

![Execução da aplicação](/images/programacao/ccplusplus/vcpkg/execucao-applicacao.png)

## Referências

* [Documentação do VCPkg](https://vcpkg.readthedocs.io/en/latest/)
* https://www.eximiaco.tech/en/2019/07/27/hello-opencv-with-c-using-visual-studio-2017-and-vcpkg/
* [Nosso código  no GIST](https://gist.github.com/carlosdelfino/7bb930a705386659aa6c433c4f90d324)
