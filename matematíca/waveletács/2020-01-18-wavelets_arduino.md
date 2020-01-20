---
title: Wavelets no Arduino - Início
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

A Ideia deste artigo é mostrar como pode-se usar o arduino para estudos de Wavelets, passo a passo, das dificuldades as soluções, e se é possível ou não.

<!--more-->

Para entender o que é Wavelets, comece lendo o artigo [Introdução a Wavelets]({{site.url}}{% post_url 2020-01-13-wavelets-introducao %}).
{: .info }

Este post é uma sequência, sem número definido de posts, portanto novos posts serão adicionados dia a dia conforme os estudos forem desenvolvendo.
{: .info }

O desempenho quanto as operações matemáticas é uma das características mais importantes a serem ponderadas, veja o clock quanto maior melhor além do desempenho. Uma última consideração é a capacidade de _samplear_ os sinais na porta analógica, veremos isso um pouco mais detalhado em cada seção abaixo.

## Ponto Fluante

## Bibliotecas Wavelets

Você vai precisar de uma biblioteca especializada em Wavelets, já que não é nosso objetivo escrever todas formulas criando novos algoritmos, iremos escolher uma para este post e eu também estou fazendo uma lista das bibliotecas em C ou C++ que podem ser usadas neste estudo, [veja a lista clicando aqui]({{site.url}}{% post_url 2020-01-18-wavelets-programacao %}), porém veja que a maioria não pode ser usada em microcontroladores, principalmente aqueles que não tem ponto flutuante ou que o compilador não seja capaz de emular tal tipo e funções de ponto flutuante, o AVR usado no [Arduino Uno]({{site.url}}{% post_url 2020-01-18-arduino-uno %}} e [Mega]({{site.url}}{% post_url 2020-01-18-arduino-mega %}} pode emular muitas operações de ponto flutuante, mas o desempenho é baixo, o Cortex-M usado no [Arduino Zero]({{site.url}}{% post_url 2020-01-18-arduino-zero %}} e [Arduino Due]({{site.url}}{% post_url 2020-01-18-arduino-due %}} com certeza também pode, porém o ideal é usar um que tenha hardware próprio para tais operações, normalmente os que tem core Cortex-M4F, o Arduino Due é um Cortex-M3 e apenas pode emular operações com ponto flutuante. 

