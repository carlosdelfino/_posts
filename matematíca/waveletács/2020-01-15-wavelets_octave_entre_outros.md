---
title: Wavelets no Octave, entre outros
tags: [Wavelet, Haar, Daubechies, Biorthogonal, Coiflats, Symlets, Morlet, Mexican Hat, Meyer, octave, DSP, Signal, Signal Processing]
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

Revisado em 27/12/2021
{: .notice }

Veremos a abaixo como usar o Octave para estudar Wavelets, e também veremos outras ferramentas que foram sugeridas nos livros e papers que eu li.

## Preparando o Ambiente

Para começar use o artigo que apresenta os [primeiros passos com Wavelet e como instalar o Octave]({{site.url}}{% post_url 2020-01-13-wavelets-introducao %})


## Listando os Wavelets disponíveis

O Toolbox **LTFAT** do Octave vem com diversos Wavelets já pré-programados que podem ser usados fácilmente, a função que representa as definições dos Wavelets são chamadas de **filterbank** e segue a seguinte nomenclatura: cada função é prefixada com `wfilt_`, seguido pelo nome abreviado e uma sequência numérica separada por `:`. Sendo assim para as Delbechs Wavelets o nome fica sendo `db10`, e a função se chama `wfilt_db`, sendo o primeiro argumento o número seguinte `wfilt_db(10)`, outro exemplo seria a referência a Wavelet Biorthogonal spline 4 momentos de fuga, sua referência seria `spline4:4` assim será usada a função com os seguintes argumentos: `wfilt_spline(4,4)`.

Para listar as Wavelets disponíveis use a função _dir_ com os seguintes argumentos: `dir([ltfatbasepath,filesep,'wavelets',filesep,'wfilt_*']);`, o resultado será similar:

|wfilt_algmband.m|wfilt_ddena.m|wfilt_matlabwrapper.m|wfilt_optsymb.m|wfilt_sym.m|
|wfilt_cmband.m|wfilt_ddenb.m|wfilt_mband.m|wfilt_qshifta.m|wfilt_symdden.m|
|wfilt_coif.m|wfilt_dgrid.m|wfilt_oddevena.m|wfilt_qshiftb.m|wfilt_symds.m|
|wfilt_db.m|wfilt_hden.m|wfilt_oddevenb.m|wfilt_remez.m|wfilt_symorth.m|
|wfilt_dden.m|wfilt_lemarie.m|wfilt_optsyma.m|wfilt_spline.m|wfilt_symtight.m|

Como pode ver, ele listou os arquivos que contem as implementações relativas as wavelets, ou seja os Filtros Wavelets. Para saber onde stão estes aquivos veja a pasta que é informada pela variável `ltfatbasepath`

Já o Toolbox **Signal** possui poucas definições de wavelets:

|morlet|mexihat|cmorwavf|shanwavf|meyeraux|

Assim que eu tiver material suficiente irei documentar cada um deles e trazer exemplos de usos, estarei lincando estes exemplos conforme apresenta-los.

## Referências

Com o objetivo de unificar as referências desta série de artigos criei um post para todas elas, [clique aqui para ve-las.]({{site.url}}/{% post_url 2020-01-15-Wavelets_referencias %})

