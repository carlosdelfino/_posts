---
title: Verilator
tags: [DE0-Nano, FPGA, Sintetizar,.Verilator]
categories: [Embarcados, FPGA]
layout: article
---

Verilator é uma ferramenta que permite gerar código em C++ que simula o funcionamento do circuito a ser sintetizado em FPGA através do que foi transcrito em Verilog ou SystemVerilog.

<!--more-->

Através de um wrapper escrito em C++ é possível carregar o modelo gerado pelo Verilator apartir do que foi escrito em Verilog ou SystemVerilog, assim fazendo verificações "lint", inserir "assertions" e pontos de analise. Estes modelos fazem amplo uso de multhreads.

Os modelos "Verilated" são convertidos em código C++ que são compilados usando Gcc, CLang e também MSVC++. Podemos também gerar bibliotecas que podem ser carregadas em outros simuladores.

## Referências

* https://www.synopsys.com/verification/simulation/vcs.html
* https://en.wikipedia.org/wiki/List_of_HDL_simulators
