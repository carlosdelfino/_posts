---
title: ARM empodera Microcontroladores com Mais Inteligencia Articificial
tags: [ARM, ML, Ethos, Cortex, Ethos-U, NPU, microNPU, Cortex-M, Cortex-M55, Ethos-U55, Corstone, Corstone-300, Helium, AI, Inteligência Artificial, Machine Learning, TensorFlow, TensorFlow Lite Micro]
categories: [arm, cortex-m]
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
math: true
image:
  teaser: arm/corstone/Corstone-300-1040x1040.jpg
  feature: arm/corstone/Corstone-300-1040x1040.jpg
  credit: Thomas Lorenser
  creditlink: https://community.arm.com/developer/ip-products/processors/b/processors-ip-blog/posts/cortex-m55-and-ethos-u55-processors-extending-the-performance-of-arm-ml-portfolio-for-endpoint-devices
tagcloud: true
---

Cortex-M55 somando ao Ethos-U55 superam 480x mais poder e desempenho para desenvolvimneto de soluções com IA em sistema baseados em ML, ampliando excepcionalmente o desempenho energético.

<!--more-->

Com a adoção do Cortex-M55 e em especial o uso do Ethos-U55 em microcontroladores, os sistemas se tornam muito mais eficazes e inclusive seguros, já que muito do processamento prévio para IA e DSP passa a ser feito no próprio embarcado sem onerar seu desempenho e consumo energétco. Um exemplo prático e muito demandado é o reconhecimento de voz para comandos de audio (Automatic Speech Recognition - ASR), que quando envolvendo microcontroladores depende do envio de amostragens da voz para servidores especializados, ou tem um desempenho muitas vezes ineficaz para uso em produção, porém com a adoção do Cortex-M55 seu coprocessador Ethos-U55, isso se reduz nos cenários mais comuns, tornando tais projetos viáveis, com baixa latência e menor custos por depender menos de conexões com internet. Não havendo tanta necessidade de conexão com a internet, aumenta drásticamente a segurança, já que nos cenários mais comuns não é necessário usar um servidor de apoio para o processamento de audio.

Obseve que o uso de microcontroladores é bem diferente de processadores de aplicação, entenda que micropocessadores usados em microcontroladores por mais poderosos que sejam são mais limitados que processadores usados em equipamentos com os usados em RaspeberryPI, para citar apenas o mais comum. O Rapsberry PI usa um Cortex-A que é uma arquitetura voltada para Aplicação e tem poder para tornar um SmartPhone Real e até susbstituindo Desktops como seu notebook quanto a navegação, edição de texto e até videos na internet. Mas microcontroladores por mais poderosos como os usados na linha Teensy que usam a linha Cortex-M4F.

Diante disso podemos veremos uma enchurrada denovos placas para IoT em especial com ASR (Reconhecimento Automático da fala), Mídia e DSP muito mais capazes e inteligentes, sem onerar o consumo energetico e sem tornar complicado o desenvolvimento, em muitos casos códigos desenvolvidos usando DSP e ML terão um desempenho melhorado em mais de 400x com um consumo de 25x mais energia, ainda sim sendo muito eficaz, pois demandará muito menos uso de internet e asism trazendo a sgurança citada.

{% include image.html 
  url="/images/arm/cortex-m/Examplo-eficiencia-energetica-cortex-m55-1040x1040.png" 
  description="Gráfico da Eficiência Energética" 
%}

## O Cortex-M55

O Cortex-M55 é o processador Cortex-M mais qualificado em IA da atualidade para uso em microcontroladores, ele é capaz de oferecer 15x mais performance em algortimos de ML que os demais microcontroladores, sem alterar o código original, o que traz grande vantagem para os projetos que já estão consolidados e aprovados, ele também é capaz de trazer 5x mais desempnho a projetos que sejam especificamente de DSP, já que possuem nativamente uma unidade de ponto flutuante do tipo "Half Precision Float Point" (Ponto flutuante representa do em 16bits) e "Full Precision Float Point" (Ponto flutuante representado em 32bits), e ainda podem lidar com operações de ponto flutuante de dupla precisam ou seja 64bits.

Os microcontroladores baeados em Cortex-M55 são os primeiros a incluir a tecnológia Helium, que extende a arquitetura Armv8.1-M (arquitetura comumente usada nos Cortex-M) com mais 150 novas instruções Escalares e Vetoriais. O procesador baseado em Cortex-M55 por sí só é capaz de lidar eficientemente com aplicações ML aumentand seu desempenho, porém pode ser adicionado de um coprocessador Ethos-U55 ampliando ainda mais sua capacidade para projetos mais complexos e que demandem mais eficiencia.

Algumas novas instruções customizadas serão adicionadas no próximo ano, em 2021.

## O que é Ethos

Ethos, representado neste publicação pelo Ethos-U55 é a primeira unidade de processamento Neural da ARM, chamado microNPU, projetado para atender as demandas de microcontroladores da família Cortex-M, incluindo Cortex-M55, Cortex-M33, Cortex-M7, and Cortex-M4, esta unidade atua como um coprocessador sendo conectada ao processodor principal do microcontrolador, da mesma forma que ocorre com as Unidades de Ponto Flutuantes para os microcontroladores da linha Cortex-M4F por exemplo.

{% include image.html 
  url="/images/arm/ethos/Arm_Ethos_U55_microNPU_block_diagram_v2.svg" 
  description="Digrama de Blocos do microNPU Ethos" 
%}

Esta nova arquitetura de unidade de processamento é totalmente integrado com as ferramentas de desenvolvimento Cortex-M (toolchain), fornecendo uma elevação de performance excepcional sem maiores complexidades a nível de software. O Ethos-U55 oferece uma performance para ML 32x maior para o Cortex-M55 quando demandando por sistemas ML, e além disso, melhora o desempenho de funcionamento da ML em 480 vezes comparado a geração Cortex-M anterior. A Configuração Ethos-U55 funciona em pequeno espaço no chip, apenas 0.1mm2 (1 décimo de milimetros quadrados) do chip quando usando tecnológia de fabricação de 16nm, o que o torna ideal para aplicações IA sensíveis ao custo, e dispositivos limitados quando ao fornecimento de energia.

A nível de software a adoção do Ethos-U55 não complicação nenhuma a nível de desenvolvimento, sendo transparente a migração do código existente para os novos microcontroladores, tendo assim toolchain integrado os novos recursos do Cortex-M55 e do Ethos-U55 quando este estiver presente.

## e este tal de Corstone

{% include image.html 
  url="/images/arm/corstome/Corstone-300-1040x1040.jpg" 
  description="Proposta de Arquitetura para um microcontrolador baseado na especificação Corstone-300" 
%}

Corstone-300 é um projeto Arquitetural sugerido pela ARM aos fabricantes de microcontroladores que venham adotar a processador Cortex-M55, é uma forma rápida de incorporar os novos processadores com ou sem Ethos-U55 em um projeto de SoC. Trazendo segurança a nível do chip com a Arquitetura TrustZone para Armv8-M fácil e rápido e com a robustez necessária a todo o chip.

O uso do Corstone-300 pelo fabricante do microcontrolador também facilita o desenvolvimento de software por terceiros para o microcontrolador com a adoção do Trusted Firmware-M (TF-M), um algoritmo opensource para uso do TrustZone, e finalmente facilitando e acelerando a rota de obtenção do certificado PSA.


{% include image.html 
  url="/images/arm/cortex-m/Cortex-M55 CPU Block-Diagram.png-1000x400.png" 
  description="Diagram de Blocos do Cortex-M" 
%}

## Helium 

Como dito, o Cortex-M55 é o processador ARM Cortex-M mais qualificado a trabalhar com IA. É o primeiro a adotar a tecnologia de processamento vetorial Helium,  trazendo assim avanços, eficiência energética em processamento de sinais e performance em ML. 

Helium introduz o conceito de "beat", que corresponde ao peso de operações aritiméticas de 32-bits. No Cortex-M55, o Helium foi constrida com base em **dual-beat per tick** ou seja o processamento de 64-bits por clock do processador. Sendo mantido um barramento de 64 bits com a memória e escalando os caminhos dos dados das unidades de execução de cordo com este comprimento, mantendo um equilibrio entre o custo do sistema e a energia usada, equanto é entregue uma significante elevação da performance computacional em processamento de sinais e ML.

Helium torna nativo o processamento de pontoflutuante de 16-bits (half precission float point) o que é novidade na família Cortex-M. Traz também suprote para multplis tipos de dados, o que dá mais poder ao desenvolvedor para optimizar o uso da mémoria atingindo a performance desejada em seu algoritimo. E finalmente dá suporte tanto para processamento de 8-bit, 16-bit e 32-bits tanto para processamento vetorial como escalar com tipos inteiros, e provê operações nativas vetoriais e escalares para fp16 e fp32 (half precision float point e full precision float point), seem esquecer a capacidade para lidar com fp64 (double precision float point).

Para otimização da area e consumo de energia, o banco de registradores na unidade de ponto flutuante é reusado para processamento vetorial. A ARM garante em suas pesquisas internas que tal compartilhamento não impacta na performance.

### Facilitando o desenvolvimento

A Technologia Helium de processamento vetorial integra o processamento de sinais e o desenvolvimento de soluções em Machine Learning uma unica ferramenta (toolchain) para maior produtividade e facilidade. Ele funciona com bibliotecas de ML existentes, CMSIS-NN e CMSIS-DSP tanto para ML clássico e DSP como com os mais comuns frameworks ML, tal como, TensorFlow Lite Micro. Tornando o projeto, desenvolvimento e manutenção de aplicações baseados em AI para IoT facílimo e rápido, com o menor risco e custo possível.

Veja a adaptação do TensorFlow Lite Micro para o Cortex-M55 e verá como é simples.

## Referências

* [Arm Cortex-M55 and Ethos-U55 Processors: Extending the Performance of Arm’s ML Portfolio for Endpoint Devices](https://community.arm.com/developer/ip-products/processors/b/processors-ip-blog/posts/cortex-m55-and-ethos-u55-processors-extending-the-performance-of-arm-ml-portfolio-for-endpoint-devices)
* [Cortex-M55 processor](https://community.arm.com/developer/ip-products/processors/b/processors-ip-blog/posts/cortex-m55-and-ethos-u55-processors-extending-the-performance-of-arm-ml-portfolio-for-endpoint-devices#Cortex-M55%20processor:%20adding)
* [Ethos-U55 microNPU](https://community.arm.com/developer/ip-products/processors/b/processors-ip-blog/posts/cortex-m55-and-ethos-u55-processors-extending-the-performance-of-arm-ml-portfolio-for-endpoint-devices#Ethos-U55%20microNPU)
* [Corstone-300 reference design](https://community.arm.com/developer/ip-products/processors/b/processors-ip-blog/posts/cortex-m55-and-ethos-u55-processors-extending-the-performance-of-arm-ml-portfolio-for-endpoint-devices#Corstone-300%20reference%20design:)
* [ARM Custom Instructions](https://developer.arm.com/architectures/instruction-sets/custom-instructions?_ga=2.10765623.2007978017.1581367433-2109137587.1578870633)
* [Cortex-M55 web page, including processor datasheet](https://developer.arm.com/ip-products/processors/cortex-m/cortex-m55?_ga=2.78406039.2007978017.1581367433-2109137587.1578870633)
* [White paper: Introduction to Arm Cortex-M55 Processor](https://pages.arm.com/cortex-m55-introduction?_ga=2.78406039.2007978017.1581367433-2109137587.1578870633)
* [Blog: Get started with early development on the Arm Cortex-M55 processor](https://community.arm.com/developer/tools-software/tools/b/tools-software-ides-blog/posts/start-early-development-on-arm-cortex-m55-processor)
