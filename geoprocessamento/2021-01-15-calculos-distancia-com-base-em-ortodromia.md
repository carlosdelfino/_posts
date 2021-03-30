var Latitude1;
var Longitude1;
var Latitude2;
var Longitude2;
var radLatitude1 = (Math.PI * Latitude1) / 180;
var radLatitude2 = (Math.PI * Latitude2) / 180;
var theta = Longitude1 - Longitude2;
var radtheta = (Math.PI * theta) / 180;
var distancia = Math.sin(radLatitude1) * Math.sin(radLatitude2) + Math.cos(radLatitude1) * Math.cos(radLatitude2) * Math.cos(radtheta);
distancia = Math.acos(distancia);
distancia = (distancia * 180) / Math.PI;
distancia = distancia * 60 * 1.8531596160000001; // Distancia em Kilometros


## Fontes:

* https://pt.wikipedia.org/wiki/Ortodromia
