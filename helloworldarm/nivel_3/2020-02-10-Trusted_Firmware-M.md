---
title: Trusted Firmware-M
tags: [ARM, Cortex, Cortex-M, TF-M, TrustZone, Trusted Firmware, Trusted Execution Environment, v7-M, v8-M, Cryptografy,PSA, Plataform Security Architecture ]
categories: [arm, TrustZone]
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
  teaser: arm/trustzone/TF-M_2.jpg
  feature: arm/trustzone/TF-M_2.jpg
  credit: ARM Ltd
  creditlink: https://developer.arm.com/tools-and-software/open-source-software/firmware/trusted-firmware/trusted-firmware-m
tagcloud: true
---

Trusted Firmware-M (TF-M) foi lançado no evento Linaro Connect em Hongkong, em Março de 2018. TF-M é desenvolvido como um projeto Open Source sobre o modelo de governança https://www.trustedfirmware.org/

<!--more-->

TF-M fornce um ambiente de execução confiável (TEE - Trusted Execution Environment) para dispositivos (microcontroladores) que adotam a arquiteutra Arm v7-M e v8-M. É uma implementação de referencia para a Plataforma de Arquitetura de segurança (Platform Security Architecture - PSA). PSA é uma receita que permite construir dispositivosconetados seguranços da análise ao desenvolvimento. PSA consite de quatro elementos - Modelos de Ameaçdas e analises de segurança, Especificações de Arquitetura, Implementações de referência Open Source (TF-M) e Certificação.

TF-M fornece um conjunto altamente configurável de componentes de software para criar um ambiente de execução confiável (TEE - Trusted Execution Environment).  Isto é obtído com um conjunto de serviços seguros de tempo de execução tais como **Secure Storage**, **Cryptography**, **Attestation** e etc. Adicionalmente, um boot seguro no TF-M garante a integridade do tempo de execução do software (**Run time Software**) e suporte para o upgrade do firmware.


{% include image.html 
  url="/images/arm/trustzone/TF-M_2.jpg" 
  description="Trusted Firmware - M" 
%}