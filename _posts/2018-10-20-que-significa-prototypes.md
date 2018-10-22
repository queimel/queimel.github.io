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
2. **La propiedad prototipo no enumerable en las funciones de JavaScript:** *Una comodidad para facilitar un patrón de diseño* (¡este patrón de diseño se explicará en profundidad en breve!). 
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

Una pregunta que no pude responder previamente:

* "¿Por qué estamos buscando `slice` en el prototipo del constructor del `Array`? ¿No debería el propio constructor del `Array` contener el método `slice`, y su prototipo simplemente contener algunos métodos de bajo nivel que comparten todos los objetos?"

Estas preguntas quedaron totalmente claras cuando empeze a entender el patrón de diseño que existe para habilitar la propiedad `prototype` en las funciones del constructor de Array.

## 3 pasos para comprender la propiedad prototype en las funciones JS

Para comprender la propiedad `prototype` en las funciones JS, debes entender el patrón de diseño que las habilita. Desarrollaré una comprensión de este patrón trabajando primero con dos alternativas menos preferibles.

### Implementacuion 1: El patrón de clase funcional
Imagina que queremos crear un juego en el cual queremos interactuar con dos perros. Queremos crear rápidamente muchos perros que tengan acceso a métodos comunes como *acariciar* y *darPremio*.

Podemos empezar la implementación de nuestro juego usando el patrón de clase funcional como sigue:

```javascript
var crearPerro = function(nombre) {
  var nuevoPerro = {};
  nuevoPerro.nombre = nombre;
  nuevoPerro.darPremio = function() { console.log("bark bark bark") };
  nuevoPerro.acariciar = function() { console.log("*wags tail*") };
  return nuevoPerro;
}

var zeus = crearPerro("zeus");
var casey = crearPerro("casey");

zeus.darPremio(); 
// "bark bark bark"
casey.darPremio();
// "bark bark bark"
```

Vamos a limpiar esto un poco almacenando esos métodos en su propio objeto. Luego extiéndelos dentro de la función de fábrica `crearPerro`.

Aunque esta implementación es fácil de razonar y refleja convenientemente la herencia basada en clases en otros idiomas, tiene al menos un problema importante: estamos copiando nuestras definiciones de métodos a cada objeto *perro* que creamos usando nuestra función de fábrica
`crearPerro`.

Esto ocupa más memoria de la  necesaria y no se apega al principio *DRY*. ¿No sería bueno si en lugar de copiar las definiciones de los métodos a `zeus` y `casey`, pudiéramos definir nuestros métodos en un solo lugar, y entonces, `zeus` y `casey` apuntaran a ese lugar?

### Refactorización 1: Implementación de un patrón de diseño de "clase de prototipo"
La herencia prototípica nos da exactamente lo que pedimos anteriormente. Nos permite definir nuestros métodos en un objeto prototipo. Entonces tendremos a `zeus`, `casey` y una infinidad de objetos como ellos apuntando a ese prototipo. `zeus` y `casey` tendran acceso a todos los métodos y propiedades de ese prototipo por referencia.

>NOTA: Para los que no estan familiarizados, existen [muchos excelentes tutoriales](https://hackernoon.com/prototypes-in-javascript-5bba2990e04b) que explican el concepto de La herencia prototípica mucho mas en profundidad de lo que yo hago aquí.

>UNA NOTA SOBRE MIS EJEMPLOS A CONTINUACIÓN: Por claridad pedagógica, usé [una función de fábrica](https://stackoverflow.com/questions/8698726/constructor-function-vs-factory-functions) llamada `crearPerro`, en lugar de funciones constructoras ES5, para implementar un [modelo prototípico](https://medium.com/javascript-scene/3-different-kinds-of-prototypal-inheritance-es6-edition-32d777fa16c9) de herencia. Preferí usar funciones de fábrica porque tienen menos *‘magic going on under the hood’* y "*azúcar sintáctica*" que los constructores ES5. ¡Con suerte, esto hace que sea más fácil mantenerse enfocado en el tema en cuestión!.

¡Genial! Ahora los objetos correspondientes a `zeus` y `casey` no contienen copias de los métodos `darPremio` y `acariciar`. En su lugar, los objetos buscan esos métodos en su  prototipo `metodosParaDemostrarCarino`.

![imagen](https://cdn-images-1.medium.com/max/1200/1*fFxKUWpR7gkaIkoiHG4i3w.png)

Pero ¿no estaria bueno si `metodosParaDemostrarCarino` estuviese encapsulado en la funcion de fábrica `crearPerro`?

haciendo esto, dejaría en claro que estos métodos están diseñados para ser utilizados únicamente con esa función. Así que un simple refactor nos deja con: