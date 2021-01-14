---
redirect_from:
   - /mapas/georeferenciamento/Verificando o resultado de seus calculos
layout: article
title: "Verificando o resultado de seus calculos"
tags: [GPS, Milhas Náuticas, Milhas Terrestres, Longitude, Latitude, Coordenadas, Distância, Cálculo]
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
Formulário que permite calcular a distância entre dois pontos, este formulário foi obtido no site [https://www.nhc.noaa.gov/gccalc.shtml](https://www.nhc.noaa.gov/gccalc.shtml).

<!--more-->

<form name="InputFormCD" method="post">
   <table border="0" cellspace="0" cellpadding="0">
      <caption class="hdr">Dados de Localização</caption>
      <tbody>
         <tr>
            <th colspan="2" id="a1">Latitude Ponto A</th>
            <td width="10">&nbsp;</td>
            <th colspan="2" id="a2">Longitude Ponto A</th>
         </tr>
         <tr>
            <td><input name="lat1" size="10" value="0.0" type="text" style="text-align: center"></td>
            <td><select name="NS1">
                  <option>N</option>
                  <option selected="selected">S</option>
               </select></td>
            <td width="10">&nbsp;</td>
            <td><input name="lon1" size="10" value="0.0" type="text" style="text-align: center"></td>
            <td><select name="EW1">
                  <option selected="selected">W</option>
                  <option>E</option>
               </select></td>
         </tr>
         <tr>
            <th colspan="2" id="b1">Latitude Ponto B</th>
            <td width="10">&nbsp;</td>
            <th colspan="2" id="b2">Longitude Ponto B</th>
         </tr>
         <tr>
            <td><input name="lat2" size="10" value="0.0" type="text" style="text-align: center"></td>
            <td><select name="NS2">
                  <option>N</option>
                  <option selected="selected">S</option>
               </select></td>
            <td width="10">&nbsp;</td>
            <td><input name="lon2" size="10" value="0.0" type="text" style="text-align: center"></td>
            <td><select name="EW2">
                  <option selected="selected">W</option>
                  <option>E</option>
               </select></td>
         </tr>
      </tbody>
   </table>
</form>
<form name="OutputFormCD" method="post">
   <center>
      <table border="0" cellspacing="10" cellpadding="0">
         <tbody>
            <tr>
               <th id="c1" colspan="2">Distância</th>
            </tr>
            <tr>
               <td align="center" colspan="2">(Arredondado para a unidade inteira mais próxima)</td>
            </tr>
            <tr>
               <td align="center"><input name="d12" size="12" type="text" style="text-align: center"></td>
               <td>
                  <select name="Dunit">
                     <option>n mi</option>
                     <option selected="selected">km</option>
                     <option>sm</option>
                  </select>
               </td>
            </tr>
         </tbody>
      </table>
      <p class="reg">
         <input style="font-weight: bold" value="Calcular" onclick="ComputeFormCD()" type="button">
         <input style="font-weight: bold" value="Limpar" onclick="ClearFormCD()" type="button">
      </p>
      <p class="reg">
         Adaptado de <a href="http://edwilliams.org/gccalc.htm"> Great Circle Calculator</a><br>
         escrito por Ed Williams<br>
         (com permissão)</p>
      <p class="reg">
         <a href="http://edwilliams.org/avform.htm">Mais infromações sobre navegação no Grande Circulo pode ser encontrada clicando aqui.</a>
      </p>
   </center>
</form>
<script language="JavaScript" type="text/javascript" src="/js/gccalc/gccalc.js"></script>

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
