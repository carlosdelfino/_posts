---
title: Verilog Coding Rules
category: [FPGA, Verilog]
tags: [FPGA, Verilog, Coding]
---
Seguir algumas regras de codificação eliminam comportamentos estranhos na simulação.

<!--more-->

* Quando modelando uma lógica sequêncial, use **nonblocking assignments**
* Quando modelando lógica compbinacional com **always block**, use **blocking assignments**. Tenha certeza que todas as variáveis RHS no bloco apareção na expressão **@** 
* Se você misturar lógica combinacional e sequêncial no mesmo bloco **always** use **nomblocking assignments**
* Não misture **assignments nonblocking** e **blocking assignments** no mesmo bloco **always**
