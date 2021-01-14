---
redirect_from:
   - /mapas/georeferenciamento/Aprendendo a Calcular Coordenadas
   - /mapas/Aprendendo a Calcular Coordenadas
layout: article
title: "Calculando Distâncias com base em coordenadas de GPS - Distâncias Maiores que 7 graus"
date: "2016-08-11 09:19:03 -0300"
tags: [Como Fazer, Como Calcular, GPS, Milhas Náuticas, Milhas Terrestres, Longitude, Latitude, Coordenadas, Distância, Cálculo]
categories: [cursoarduino, geoprocessamento]
share: true
toc: true
comments: true
feature:
 category: true
 index: false
tagcloud: true
ads:
 show: true
image:
  teaser: cursoarduino/geoprocessamento/GPS_UP501_teaser.jpg
  feature: cursoarduino/geoprocessamento/GPS_UP501_feature.jpg
math:
   enable: true
   align: "left"
---

Iremos ver neste artigo como proceder o cálculo para grandes distâncias, consideraremos então distâncias maiores que 7°, seja na Longitude ou Latitude ou ambos.

<!--more-->

Usaremos algoritmos de exemplos em PHP e C/C++.

## Códigos de exemplo

### Código em PHP

Abaixo é apresentado um código em PHP que foi obtido no site Stackoverflow e que é muito simples, este código pode ser usado para se calcular a distância entre dois pontos com base em sua latitude e longitude.

{% highlight php %}
function calcDistancia($lat_inicial, $long_inicial, $lat_final, $long_final)
{
    $d2r = 0.017453292519943295769236;

    $dlong = ($long_final - $long_inicial) * $d2r;
    $dlat = ($lat_final - $lat_inicial) * $d2r;

    $temp_sin = sin($dlat/2.0);
    $temp_cos = cos($lat_inicial * $d2r);
    $temp_sin2 = sin($dlong/2.0);

    $a = ($temp_sin * $temp_sin) + ($temp_cos * $temp_cos) * ($temp_sin2 * $temp_sin2);
    $c = 2.0 * atan2(sqrt($a), sqrt(1.0 - $a));

    return 6368.1 * $c;
}
{% endhighlight %}


### Código em C e C++

O Código abaixo é o código simplificado considerando a curvatura da terra, veja que ele não difere muito do código usado na linguagem PHP, e este código pode ser usado diretamente no Arduino UNO, Mega, DUE ou qualquer outro, lembrando que o Arduino UNO e Mega não tem funções nativas para trigonometria, portanto pode ser um pouco lento sua execução, mas veremos logo a frente um código que é alternativa para este problema quando usamos distâncias menores que 7°.

{% highlight C %}
double calcDistancia(double lat_inicial, double long_inicial, double lat_final, double long_final) {

    double d2r = 0.017453292519943295769236;

    double dlong = (long_final - long_inicial) * d2r;
    double dlat = (lat_final - lat_inicial) * d2r;

    double temp_sin = sin(dlat/2.0);
    double temp_cos = cos(lat_inicial * d2r);
    double temp_sin2 = sin(dlong/2.0);

    double a = (temp_sin * temp_sin) + (temp_cos * temp_cos) * (temp_sin2 * temp_sin2);
    double c = 2.0 * atan2(sqrt(a), sqrt(1.0 - a));

    return 6368.1 * c;
}
{% endhighlight %}

## Verificando o resultado de seus calculos

Para reduzir o número de scripts e o tamanho da página dividi este artigo em outros, assim o script de teste e calculo foi transferido para o artigo: []({% post_url geoprocessamento/2021-01-14-script-calculo-distancias-com-base-em-coordenadas-de-gps %})

## Fontes:

* http://www.inaccess.com.br/?p=1163
* https://www.lapintagalapagoscruise.com/blog/nautical-miles-vs-miles/#:~:text=A%20nautical%20mile%20is%20based,Earth%20is%201%20nautical%20mile.

* https://www.wolframalpha.com/

* https://mundoeducacao.uol.com.br/geografia/latitudes-longitudes.htm
* http://mundoestranho.abril.com.br/materia/por-que-a-milha-nautica-ediferente-da-milha-terrestre
* https://super.abril.com.br/mundo-estranho/por-que-a-milha-nautica-e-diferente-da-milha-terrestre/
* https://pt.wikipedia.org/wiki/Milha_n%C3%A1utica
* https://pt.wikipedia.org/wiki/Tr%C3%B3pico
* https://pt.wikipedia.org/wiki/C%C3%ADrculo_Polar_%C3%81rtico
* https://pt.wikipedia.org/wiki/C%C3%ADrculo_Polar_Ant%C3%A1rtico
* https://en.wikipedia.org/wiki/Mendenhall_Order
* http://www.pilotopolicial.com.br/calculando-distancias-e-direcoes-utilizando-coordenadas-geograficas/
* http://www.gpsy.com/gpsinfo/
