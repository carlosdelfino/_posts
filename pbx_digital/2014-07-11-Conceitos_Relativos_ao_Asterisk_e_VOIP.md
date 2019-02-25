---
layout: article
title: Conceitos relacionados ao uso do Asterisk e VOIP
category: pbx_digital
tags: [pbx, pbxdigital, voip, voz sobre ip, telefonia, comunicação, celular, pabx]
share: true
comments: true
feature:
 index: true
 category: true
ads: 
 show: true
coinbase:
  show: true
image:
   teaser: pbx_digital/asterisk-400x250.png
   feature: pbx_digital/asterisk-640x359.png
   credit: Asterisk
---
Em todo contexto de trabalho com TIC (Tecnologia da Informação e Comunicação) temos conceitos que precisam ser assimilados, vejamos neste artigo alguns palavras e conceitos usados no ASTERISK, quais seus significados e relação.

<!--more-->

#### PBX (Private Branch Exchange)

É uma pequena central telefonica que pode ter apenas dois pontos de conexão telefônicos ou até mesmo centenas.

#### Virtual PBX
É o mesmo que PBX porém instalado remotamente em uma operadora ou mesmo criado através de softwares, podendo usar VOIP para comunicação entre os terminas.

#### PABX (Private Automatic Exchange)
É o mesmo que PBX. O acronimo __PABX__ é melhor pois descreve ums istema automáto de troca de canais privados de telefonia.

#### IVR (Interactive Voice Response) ou URA (unidade de resposta audível)
O IVR ou URA é um recurso muito valioso em centrais telefonicas do tipo PBX, já que com este recurso podemos fazer atendimentos automáticos e distribuir a chamada conforme respostas do usuário, além disso podemos integrar a URA com o Arduino permitindo assim uma interação totalmente automática entre o usuário e o Arduino ou o Arduino e o Asterisk para fazer ligações para o usuário.

#### ACD (Automatic Call Distribution)
ACD permite que as ligações que chegam a central PBX sejam distribuídas conformem regras pre-estabelecidas, integrando a central com o Arduino podemos intervir nas regras de chamada conforme parâmetros coletados por ele.

####  FXO e FXS
Foreign Exchange Office e Foreign Exchange Sation, são tipos de canais (Channel), são canais telefônicos como em centrais PBXs, 
A FXO é como um aparelho telefônico, recebe sinais de controle como o sinal de chamada.
Já a FXS é como uma saída da central telefônica gerando sinalização e também oferece alimentação elétrica para funcionamento do aparelho telefônico.  Por exemplo para se conectar uma linha telefônica comum (PSTN) a um computador você precisa de um placa que tenha uma entrada FXO. Já para ligar seu telefone ao computador e este simular uma central telefonica você precisa de um canal FXS.

####  PSTN (PUBLIC SWITCHED TELEPHONE NETWORK)
É a sua operadora telefônica, em especial toda a infraestrutura que a constrõe.

####  POTS (Plain Old Telephone Service)
É o sistema telefônico clássico, amplamente utilizado no mundo, mesmo com o advento de linhas telefônicas como ISDN, ADSL, Fibra Ótica e Telefonica Celular.

####  ATA (Analog Telephone Adapter)
ATA é um adaptador de telefone comum para telefone IP, permitindo que se conecte um telefone padrão a sua rede IP e assim receber e fazer chamadas, um ATA normalmente é um dispositivo FXS.

####  Channel
São canais de comunicação, por onde há trafego de dados ou audio entre por exemplo o Asterisk e um ATA, no Asterisk não se distingue canais originador ou destino de chamada, nem mesmo canais FXO ou FXS.

####  SIP (Session Initiation Protocol)
É um protocolo de inicialização de seção, de código aberto usado em centrais PABX e definido pela RFC2543, podemos dizer que o SIP é similar ao HTTP, também baseado em texto e permite o uso de qualquer tipo de stream entre seções, iniciando e finalizando a seção, sendo ela audio ou video.

#### Agente Utilizador
O Agente utilizador se assemelha ao Browser, com ele é feita e estabelecida as chamadas ao PABX, é um software com inteligência para armazenar a situação da ligação e permite o uso de endereços similares a endereços de e-mail.

#### Endereço SIP

O Endereço SIP se assemelha ao endereço de e-mail, SIP:user@proxy.university.edu

#### Servidor Proxy SIP

O Servidor Proxy da mesma forma que para infraestrutura WWW, é um servidor intermediário, porém não faz cache das comunicações, apenas intermedia a conexão com outros servidores finais.

O Servidor Proxy pode ser usada em centrais onde um ramal tem vários Agentes Utilizadores, fazendo com que todos toquem ao mesmo tempo para uma chamada entrante, e o que atender primeiro tome o controle da ligação.

Um Servidor Proxy SIP pode ser também fundamental no sistema de tarifação de ligações.

#### Servidor de Redirecionamento SIP

O Servidor de Redirecionamento é capaz de permitir dois Agentes se interconectarem diretamente a outro servidor, sem o intermédio de um Servidor Proxy.

#### Servidor Redirecionador

Com este servidor o SIP permite o uso de unidades moveis, que podem estar em qualquer lugar na rede, ele recebe do AGente Utilizador seus dados de localização e assim mantendo um único endereço: SIP:user@proxy.university.edu, é possível localizar o Agente Utilizador mesmo que seu endereço mude na rede.

#### IAX2- IAX (Inter Asterisk eXchange)

Definido atualmente pelo RFC 5456, é um protocolo que substitui o SIP, porém utiliza apenas uma porta (porta 4569) tanto para sinalização como para comunicação, através do protocolo UDP, diferente do SIP e H.323. que usa RTP, o que é interessante para uso em redes que se comunicam através de Firewall e NAT, reduzindo os transtornos.

o IAX foi criado para uso entre instalações ASTERISK, porém hoje já existem telefones VoIP que suportam o protocolo.

Uma característica muito interessante do IAX é o fato de multiplexar multiplas chamadas em um único pacote de dados IP, ou seja um único datagrama IP pode transportar seções de comunicação de versos canais.

IAX-2 é a versão 2 do IAX, ambos suportado pelo Asterisk.

#### H.323

é um padrão de redes para sistemas AudioVisuais e multimídia, que pertence a familia H definida pelo ITU-T (International Telecomunication Union - Telecomunication Standartization sector), é uma recomendação arquitetural que pode ser aplicada sobre qualquer infraestrutura de rede de comunicação. Envolve diversos protocolos da familia H e G do ITU-T.

#### Gatekeeper

Gatekeeper é similar a um proxy, utilizado em sistemas baseados no padrão H.323 é opcional e centraliza os pedidos de chamada e gerencia a banda empregada pelos participantes para evitar que sobrecarreguem a rede com taxas de transmissão muito elevadas.

#### MCU (Multi Control Unit)

Componente que centraliza os pedidos de chamada, possibilitando a conexão de 3 ou mais participantes simultaneamente.


  <a href="/cursoarduino/" class="btn-success">Este trabalho é mantido com os cursos oferecidos no <br />
Curso Arduino Minas!</a>
  
