---
title: Introdução aos conceitos de Wavelet
tags: [Wavelet, Haar, Daubechies ]
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
---
Wavelets são formas de onda efetivamente limitadas num curto espaço de tempo, que tem uma média de valores entre zero e não zero.

<!--more-->

Wavelets tendem a ser irregulares, não mantem uma recorrência como sinais senoidais ou composições de sinais senoidais, como proposto pelas Analises de Furrier. Porém pode ser quem sinal seja composto pela repetição de wavelets, o que pode gerar um pouco de confusão, mas vamos tentar entender um pouco isso abaixo.

Estou trabalhando nestas anotações
{: .notice-warning}

## Haar Wavelets

O aprendizado sobre Wavelets devem começar pelo Haar Wavelet, que é um modelo simples, descontinuo e similar a uma função "step", veja na imagem abaixo obtido no site do MathLab, como muitas utilizadas neste artigo.

<figure class="image">
   <img src="{{site.url}}/images/matematica/wavelets/ch01_intro34-haar.gif" alt="Haar Wavelets" >
   <figcaption>Haar Wavelets</figcaption>
</figure>

O Wavelet Haar é também citado como sendo o primeiro modelo do Wavelet Daubechies (db1).

## Daubechies Wavelets

[Ingrid Daubechies]({{site.url}}/{% post_url perfil/2020-01-13-ingrid_daubechies %}), Matemática que dá o nome a esta série, é uma especialista da atualidade no tema, 

![]({{site.url}}/images/matematica/wavelets/ch01_intro36-daubechs.gif)

## Outros Wavelets Reais

Conforme o site MathWorks há outras Wavelets reais que estão disponíveis no seu Toolbox:

* Biorthogonal Reverso
* Familia Gaussiana derivativa
* Aproximação baseada em FIR do wavelet Meyer

[Veja mais informações sobre outros wavelets Guia de Usuário do Wavelet Toolbox](https://www.mathworks.com/help/wavelet/ug/wavelet-families-additional-discussion.html#f8-40111).

## Wavelets Complexos

Existem alguns outros wavelet complexos disponível no toolbox:

* Derivativos de wavelets Gaussianos
* Morlet
* Frequência B-Spline
* Shannon

[Mais informações sobre Wavelets complexos podem ser obtidos no Guia de Usuário do Wavelet Toolbox do Mathlabs](https://www.mathworks.com/help/wavelet/ug/wavelet-families-additional-discussion.html#f8-40149).
