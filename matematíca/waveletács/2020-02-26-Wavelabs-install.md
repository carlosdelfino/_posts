---
title: Instalando Wavelab no Octave 5.2 para Windows - Introdução aos Wavelets
tags: [Wavelet, Transformada, Fourrier, Haar, Mallat, Daubechies, Orthogona, Orthonormal, Biorthogonal, Coiflats, Symlets, Morlet, Mexican Hat, Meyer, Octave, DSP, Signal, Signal Processing, Wavelab, Framelet, Stanford, ]
categories: [Matematica, Wavelets]
layout: article
share: true
toc: true
comments: true
feature:
 category: true
 index: true
tagcloud: true
ads: 
 show: true
image:
   teaser: matematica/wavelets/wavelabs/cwt.jpg
   feature: matematica/wavelets/wavelets.jpg
math: true
images:
  dir: /images/matematica/wavelets/
---

Wavelab é um Toolbox para Mathlab que pode ser usado no Octave, foi criado pela Universidade de Stanford, e tem como filosofia: que um Artigo Acadêmico para que seja adequado ao ensino deve ser capaz de ser replicado; assim com a ferramenta proposta os pesquisadores são convidados a fazerem seus artigos de forma que os leitores possam replicar seus experimentos.

<!--more-->

<figure class="image image-center center">
  <img src="{{site.url}}/{{page.images.dir}}/wavelabs/cwt.jpg" alt="Continuous wavelet transform of Cantor Signal" >
  <figcaption>
  Continuous wavelet transform of Cantor Signal
  </figcaption>
</figure>

Para baixar o Wavelab visite o seguinte link [http://www-stat.stanford.edu/~wavelab/Wavelab_850/download.html](http://www-stat.stanford.edu/~wavelab/Wavelab_850/download.html)

## Instalando o Wavelab no Octave

Como eu uso o Octave e acredito na filosofia proposta acima pelos pesquisadores de Stanford, e vale a pena o uso da ferramenta.

Abaixo vai um pequeno tutorial para usar o Toolbox.

* Crie uma pasta por exemplo `~/workspace/wavelets/wavelab` para guardar seu trabalho e downloads relativos;
* Descompacte a o pacote Wavelab 850, [baixado deste link](http://www-stat.stanford.edu/~wavelab/Wavelab_850/download.html);
* Copie a nova pasta de nome Wavelab850 para a pasta `toolbox` que deve ser criada na pasta raiz de instalação do seu Octave para windows, a pasta raiz é  `c:\octave\octave5.2\mingwin\`, ficando similar ao meu: `c:\octave\octave5.2\mingwin\toolbox\Wavelab850`;
* Edite o arquivo `WavePath.m` que está neste novo diretório depois de populado com o pacote baixado;
* Adicione o código abaixo após a linha 31, para que ele reconheça a instalação do Octave 5.2 no Windows:

```
	elseif strcmp(Friend,'x86_64-w64-mingw32');
 	   PATHNAMESEPARATOR = '\';	  
	   WAVELABPATH = [cd PATHNAMESEPARATOR];  
      MATLABPATHSEPARATOR = ';';
      WAVELABPATH=strcat(matlabroot,'\toolbox\Wavelab850\')
```

* Execute o comando `addpath c:\octave\octave5.2\mingw64\toolbox\Wavelab850` para que o novo path seja adicionado ao Octave, se preciso corrija para refletir a sua instalação, eu não fiz testes com paths diferentes do sugerido acima, então caso tenha sucesso compartilhe para que possamos atualizar nossa documentação.
* Execute então o comando `WavePath` para que o pacote seja instalado finalmente no Octave.

## Testando a instalação

Para testar a instalação, é possível usar `WTBrowser`, script que já está pronto no ToolBox e demonstra as imagens/figuras apresentadas no livro do [Sthefane Mallat](Mallat.md), [a Wavelet tour of signal processing][1]

Tal script, apresenta todas as imagens propostas no livro, e é uma fonte gigantesca de material de estudo complementar ao que já apresentado no livro, trazendo praticidade.

A numeração das imagens não confere exatamente nem com a 2° Edição ou 3° do livro, acredito que possa ser referente a primeira edição. Mas é suficiente para ampliar os horizontes.

## Mais exemplos e meus estudos

Conforme eu venha desenvolvendo no assunto e aprendendo a criar experimentos com Wavelets estarei colocando os exemplos no Fork Wavelabs disponível em meu repositório.

[https://carlosdelfino.github.io/Wavelab850](https://carlosdelfino.github.io/Wavelab850)


## Referências:

* http://statweb.stanford.edu/~wavelab/
* http://fossphosis.blogspot.com/2011/10/wavelab-with-gnu-octave.html
* [a Wavelet tour of signal processing][1]

[1]: https://g.co/kgs/wNjt4A "a Wavelet tour of signal processing - The sparse Way em sua terceira edição"
[2]: https://core.ac.uk/download/pdf/22879255.pdf "Wavelet Theory and some of its Applications"
[3]: https://www.eecis.udel.edu/~amer/CISC651/IEEEwavelet.pdf "A Introduction to Wavelets - Dra. Amara Graps"