---
title: Instalando o Octave no Linux 
tags: [Wavelet, Haar, Daubechies, Biorthogonal, Coiflats, Symlets, Morlet, Mexican Hat, Meyer, octave, DSP, Signal, Signal Processing, octave, install, pkg]
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

Algumas anotações para instalar o Octave no linux, em especial Ubuntu 18.4 32bits

<!--more-->

Diante da necessidade de instalar o Octave no Linux Ubuntu 18.4 32bits, fiz este pequeno tutorial para relembrar e facilitar.

Use o "Apt-Get" para instalar o Octave e também a biblioteca de desenvolvimento "liboctave-dev", e a biblioteca de desenvolvimento Portaudio para uso com o pacote LTFat:

```bash
sudo apt install octave liboctave-dev portaudio19-dev
```

Depois crie um diretório `~/octave` faça o download dos seguintes pacotes para ele:

* https://gnu-octave.github.io/packages/pkg
* https://gnu-octave.github.io/packages/control
* https://gnu-octave.github.io/packages/ltfat
* https://gnu-octave.github.io/packages/signal

Abra o `octave-cli` e execute o comando pkg install para cada bacote que foi baixado.

