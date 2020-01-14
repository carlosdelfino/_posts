---
title: Introdução aos Wavelets
tags: [Wavelet, Haar, Daubechies, Biorthogonal]
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
Wavelets são formas de onda efetivamente limitadas num curto espaço de tempo, que tem uma média de valores entre zero e não zero.

<!--more-->

Wavelets tendem a ser irregulares, não mantem uma recorrência como sinais senoidais ou composições de sinais senoidais, como proposto pelas Analises de Furrier. Porém, pode ser que algum sinal seja composto pela repetição de wavelets, o que pode gerar um pouco de confusão, mas vamos tentar entender um pouco isso abaixo.

Bem, se você está procurando a Banda Wavelets, não iremos falar dela aqui, você pode obter [mais informações clicando aqui](http:/bit.ly/wavelets). Não poderia deixar de cita-la.

Estou trabalhando nestas anotações, estudos retomados em 13/01/2020.
{: .notice-warning}

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

## Haar Wavelets

O aprendizado sobre Wavelets devem começar pelo Haar Wavelet, que é um modelo simples, descontinuo e similar a uma função "step", veja na imagem abaixo obtido no site do MathLab, como muitas utilizadas neste artigo.

<figure class="image">
  <img src="{{site.url}}/{{page.images.dir}}/ch01_intro34-haar.gif" alt="Haar Wavelets" >
  <figcaption>Haar Wavelets</figcaption>
</figure>

O Wavelet Haar é também citado como sendo o primeiro modelo do [Wavelet Daubechies (db1)](@daubechies-wavelets).

## Daubechies Wavelets

[Ingrid Daubechies]({{site.url}}/{% post_url perfil/2020-01-13-ingrid_daubechies %}), Matemática que leciona na Universidade de Duke, dá o nome a esta série, ela é a maior e mais brilhante especialista da atualidade no tema.

Ingrid Daubechies, criou o que é chamado de _*"Wavelets Ortogonais Compactadamente Suportados"*_ tornando assim a analise discreta viável.

Os Wavelets de Daubechies são nomeados usando duas letras e um número que define o nível do wavelets, assim usa-se as letras "db" seguido dos números, veja na imagem abaixo alguns Daubechs Wavelets, lembrando que o *"db1"* é o [Haar Wavelet](#haar-wavelets), a Mãe dos Wavelets.

<figure class="image">
   <a href="{{site.url}}/{%post_url 2020-01-14-daubechs-wavelets %}"> <img src="{{site.url}}/{{page.images.dir}}/ch01_intro36-daubechs.gif" alt="Daubechs Wavelets" ></a>
   <figcaption><a href="{{site.url}}/{%post_url 2020-01-14-daubechs-wavelets %}" alt="Daubechies Wavelets">Daubechies Wavelets</a></figcaption>
</figure>

## Biorthogonal Wavelets

Este típo de wavelet é importante para reconstruções de sinais e imagens, ela apresenta a propriedade de phases lineares. São usados dois wavelets, um para decomposição (o lado esquerdo) e o outro para reconstrução (o lado direito), ao invés de usar simplesmente um.

<figure class="image">
  <img src="{{site.url}}/{page.images.dir}}/ch01_intro62-Biorthogonal.gif" alt="Biorthogonal Wavelets" >
  <figcaption>[Biorthogonal Wavelets]({{site.url}}/{%post_url 2020-01-14-biorthogonal-wavelets %})</figcaption>
</figure>

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

## Referências

* [O que são Wavelets - (Inglês)](https://www.mathworks.com/help/wavelet/gs/what-is-a-wavelet.html)
* [Introdução a família de Wavelets = Mathworks - (Inglês)](https://www.mathworks.com/help/wavelet/gs/introduction-to-the-wavelet-families.html)
* [Video Aulas - Introdução a Wavelets - Mathworks - (Inglês)](https://www.youtube.com/playlist?list=PLn8PRpmsu08ojy02wi4QLVzELM545Xw3p)
