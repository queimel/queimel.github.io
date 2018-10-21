---
layout: post
title: Lo que realmente queremos decir cuando hablamos de prototipos.
---

*[original en ingles](https://medium.freecodecamp.org/what-we-really-mean-when-we-talk-about-prototypes-165586f29fa9) por Hayden Betts*

Los desarrolladores de JavaScript que comienzan a menudo usan erróneamente una palabra, "prototipo", para referirse a dos conceptos diferentes. Pero, ¿cuál es exactamente la diferencia entre un "prototipo de objeto" y la "propiedad de prototipo" de las funciones de JavaScript?


![Prototype image](https://cdn-images-1.medium.com/max/1600/1*L04gw3FTaj-fQE6b-bY2Ug.png)

Pensé que entendía el concepto de "prototipos" y la herencia prototípica en JavaScript. Pero seguí encontrándome confundido por las referencias al "prototipo" en el código y en la documentación.

Gran parte de mi confusión desapareció cuando me di cuenta de que al escribir sobre JavaScript, las personas a menudo usan "prototipos" para describir dos conceptos distintos pero relacionados.

1. **El prototipo de un objeto:** el objeto plantilla del cual otro objeto JavaScript hereda métodos y propiedades.
2. **La propiedad prototipo no enumerable en las funciones de JavaScript:** *Una comodidad para facilitar un patrón de diseño* (¡ese patrón de diseño se explicará en profundidad en breve!). 
No tiene sentido en sí mismo, hasta que se establece deliberadamente para tener alguna función relacionada con la herencia. Más útil cuando se usa con funciones de constructor y *factory functions* (¡la explicación ya viene!) Aunque todas las funciones de JS tienen esta propiedad por defecto. Contiene una propiedad `constructor`, que se refiere a la función original.

![Prototype image](https://cdn-images-1.medium.com/max/1600/1*Vmv0NSt-8jA_qOcbPuCzxA.png)
>Hasta las funciones triviales tienen una propiedad de prototipo por defecto.

Por un largo tiempo estuve conforme con la primera definicion, pero no con la segunda.

## ¿Porqué esta distincion importa?
Antes de entender la diferencia entre el "prototipo de un objeto" y la "propiedad prototipo no enumerable en las funciones" me encontré confundido por expresiones como las siguientes:

```javascript
Array.prototype.slice.call([1, 2], 0, 1);
// [ 1 ]
```