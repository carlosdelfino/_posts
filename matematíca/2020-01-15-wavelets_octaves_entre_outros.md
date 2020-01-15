---
title: Wavelets no Octave, entre outros
tags: [Wavelet, Haar, Daubechies, Biorthogonal, Coiflats, Symlets, Morlet, Mexican Hat, Meyer, octave]
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
   teaser: matematica/wavelets/wavelets.jpg
   feature: matematica/wavelets/wavelets.jpg
math: true
images:
  dir: /images/matematica/wavelets/
---

O Octave é uma excelente ferramenta para estudos da Matemática, e não fica atrás quanto aos Wavelets.

<!--more-->

Veremos a abaixo como usar o Octave para estudar Wavelets, e também veremos outras ferramentas que foram sugeridas nos livros e papers que eu li.

## Preparando o Ambiente

Para desenvolver tal estudo, eu precisei preparar minha estação de trabalho, inicialmente dois software estão sendo usados, o **Octave** e o **VScode**, o primeiro é uma ferramenta similar ao Mathlab que permite desenvolver scripts matemáticos, produzida pela projeto GNU é _Open Source_ e gratuita, muito útil para o estudo e avanço no domínio da matemática, o segundo talvez mais conhecido é uma IDE aberta produzida pela Microsoft que permite integrar plugins e ferramentas como o Octave, entre muitas outras. Para usar o Octave junto com o VScode usei o plugin disponível neste link](https://marketplace.visualstudio.com/items?itemName=toasty-technologies.octave)

* [Clique aqui para baixar o Octave](https://www.gnu.org/software/octave/download.html)
* [E aqui para obter o VSCode](https://code.visualstudio.com/download)

Bem, instale ambos os programas, depois dentro do VSCode instale o plugin, não irei entrar em detalhes quanto a esta instalação.


<figure class="image">
  <img src="{{site.url}}/{{page.images.dir}}/ltflat-wavelet-plotwavelet-db8.png" alt="Um exemplo simples de uso da função FWT para wavelet 'db8'" >
  <figcaption> Um exemplo simples de uso da função FWT para wavelet 'db8'</br>
    <code>
    [f,fs] = greasy;
    J = 10;
    [c,info] = fwt(f,'db8',J);
    plotwavelets(c,info,fs,'dynrange',90);
    </code>
  </figcaption>
</figure>

Finalmente, precisamos instalar alguns _toolbox_ no Octave para trabalhar com Wavelets. Iremos inicialmente instalar o [**LTFAT - Larger Timer/Frequency Analise Tools**](https://octave.sourceforge.io/ltfat/index.html), um toolbox que pode ser usado tanto para pesquisas acadêmicas como profissionais.

## Listando os Wavelets disponíveis

O Octave vem com diversos Wavelets já preprogramados que podem ser usados fácilmente, a função que representa as definições dos Wavelets são chamadas de **filterbank** e segue a seguinte nomenclatura: cada função é prefixada com `wfilt_`, seguido pelo nome abreviado e uma sequência numérica separada por `:`. Sendo assm para as Delbechs Wavelets o nome fica sendo `db10`, e a função se chama `wfilt_db`, sendo o primeiro argumento o número seguinte `wfilt_db(10)`

Several formats of the basic filterbank definition w are recognized. One of them is a text string formed by a concatenation of a function name with the wfilt_ prefix followed by a list of numerical arguments delimited by :. For example 'db10' will result in a call to wfilt_db(10) or 'spline4:4' in call to wfilt_spline(4,4) etc. All filter defining functions can be listed by running dir([ltfatbasepath,filesep,'wavelets',filesep,'wfilt_*']); Please see help of the respective functions and follow references therein.

## Referências

Com o objetivo de unificar as referências desta série de artigos criei um post para todas elas, [clique aqui para ve-las.]({{site.url}}/{% post_url 2020-01-15-Wavelets_referencias %})