---
layout: post
title: Entendiendo patrones de diseño en JavaScript
---

*[original en ingles](https://blog.bitsrc.io/understanding-design-patterns-in-javascript-13345223f2dd) por Sukhjinder Arora*


Cuando empiezas con un proyecto nuevo, no empiezas a codear inmediatamente. Primero debes definir el  propósito y el alcance del proyecto, para entonces listar las características o especificaciones del proyecto. Antes de que puedas empezar a codear o si estás trabajando en proyecto más complejo, debes elegir el patrón de diseño que mejor se adapte a su proyecto.

## ¿Qué es un patrón de diseño? ##
En ingeniería de software un patrón de diseño es una solución reusable para problemas que ocurren comúnmente en el diseño de software. Los patrones de diseño representan las mejores prácticas usadas por los desarrolladores más experimentados. Un patrón de diseño puede considerarse como una plantilla de programación.

### ¿Porqué usar patrones de diseño?
Muchos programadores piensan que los patrones de diseño son una pérdida de tiempo o no saben cómo aplicarlos adecuadamente. Pero usar un patrón de diseño apropiado puede ayudarte a escribir un código mejor y más comprensible, y un código que puede ser fácilmente mantenible porque es fácil de entender.

Pero más importante aún, los patrones de diseño entregan a los desarrolladores un vocabulario común con el cual comunicarse. Muestran la intención del código instantáneamente a alguien que está aprendiendo ese código.

Por ejemplo, si estás usando el patrón decorador en tu proyecto, entonces un nuevo programador podría saber inmediatamente que esta haciendo ese trozo de código, y se podrá concentrar en resolver el problema del negocio, más que en tratar de entender el código.

## Patrón de módulos
Un Módulo es una pieza de código autónomo, por lo que podemos actualizar el Módulo sin afectar las otras partes del código. Los módulos también nos permiten evitar la contaminación del espacio de nombres creando un ámbito separado para nuestras variables. También podemos reutilizar módulos en otros proyectos cuando están desacoplados de otras piezas de código.

Los Módulos son una parte integral de cualquier aplicación JavaScript moderna, y ayuda a mantener nuestro código limpio, separado y organizado. Existen muchas maneras de crear módulos en JavaScript, una de ellas es el patrón de módulos.

A diferencia de otros lenguajes de programación, JavaScript no tiene modificadores de acceso, o sea, no puedes declarar si una variable es pública o privada.  Por lo tanto, el patrón de Módulos también se utiliza para emular el concepto de encapsulación.

Este patrón una las IIFE (*immediately-invoked function expression*), *closures* y *function scope* para simular este concepto, por ejemplo:

```javascript
const miModulo = (function() {
  
  const variablePrivada = 'Hola Mundo';
  
  function metodoPrivado() {
    console.log(variablePrivada);
  }
  return {
    metodoPublico: function() {
      metodoPrivado();
    }
  }
})();
miModulo.metodoPublico();
```

Al ser una *IIFE*, el código se ejecuta de inmediato y el objeto devuelto se asigna a la variable `miModulo`. Debido a los *closures*, el objeto devuelto todavía puede acceder a las funciones y variables definidas dentro de la *IIFE* incluso después de que la *IIFE* haya terminado.

Así que las variables y funciones definidas dentro del *IIFE* estan basicamente *ocultas* al *scope* externo y por lo tanto son privadas dentro de la variable `miModulo`.

Después de que el código es ejecutado, la variable `miModulo` luce como esto:

```javascript
const miModulo = {
  metodoPublico: function() {
    metodoPrivado();
  }
};
```

Entonces podemos llamar al método `metodoPublico()` que a su vez llamará al `metodoPrivado()`. Por ejemplo:

```javascript
// Imprime 'Hola Mundo'
miModulo.metodoPublico();
```

## Patrón de Módulo Revelado.
El patrón de módulo revelado es una versión ligeramente mejorada del patrón del módulo de Christian Heilmann. El problema con el patrón de módulo es que tenemos que crear funciones públicas nuevas solo para llamar a las funciones privadas y variables.

En este patrón, asignamos las propiedades del objeto devuelto a las funciones privadas que queremos revelar cómo públicas. Es por esto que se llama patrón de módulo revelador. Por ejemplo:

```javascript
const myRevealingModule = (function() {
  
  let privateVar = 'Peter';
  const publicVar  = 'Hello World';
  function privateFunction() {
    console.log('Name: '+ privateVar);
  }
  
  function publicSetName(name) {
    privateVar = name;
  }
  function publicGetName() {
    privateFunction();
  }
  /** reveal methods and variables by assigning them to object     properties */
return {
    setName: publicSetName,
    greeting: publicVar,
    getName: publicGetName
  };
})();
myRevealingModule.setName('Mark');
// prints Name: Mark
myRevealingModule.getName();
```

Este patrón hace más fácil entender cuáles de nuestras funciones y variables pueden ser accesadas públicamente, lo que ayuda en la legibilidad del código.

Después de ejecutar el código, el módulo `myRevealingModule` luce así:

```javascript
const myRevealingModule = {
  setName: publicSetName,
  greeting: publicVar,
  getName: publicGetName
};
```

Podemos llamar a `myRevealingModule.setName('Mark')`, que es una referencia al `publicSetName` interno y a `myRevealingModule.getName()`, que es una referencia al `publicGetName` interno. Por ejemplo:

```javascript
myRevealingModule.setName('Mark');

// prints Name: Mark
myRevealingModule.getName();
```

#### Ventajas del patrón de módulo revelado versus el patrón de módulo.

* Podemos cambiar los miembros de público a privado y viceversa modificando una sola línea en la sentencia `return`.

* El objeto devuelto no contiene definiciones de funciones, todas las expresiones del lado derecho están definidas dentro del *IIFE*, lo que hace que el código sea claro y fácil de leer.

## Módulos ES6
Antes de ES6, JavaScript no tenía módulos integrados, por lo que los desarrolladores tenían que confiar en bibliotecas de terceros o en el patrón de módulos para implementar módulos. Pero con ES6, JavaScript tiene módulos nativos.

Los módulos ES6 se guardan en archivos. Sólo puede haber un módulo por archivo. Todo lo que está dentro de un módulo es privado por defecto. Las funciones, variables y clases se exponen utilizando la palabra clave export. El código dentro de un módulo siempre se ejecuta en modo estricto.

### Exportando un módulo
Existen dos maneras de exportar una función y una declaración de variable:

* Agregando la palabra clave `export` delante de una declaración de función o variable. Por ejemplo:

```javascript
// utils.js
export const greeting = 'Hello World';
export function sum(num1, num2) {
  console.log('Sum:', num1, num2);
  return num1 + num2;
}
export function subtract(num1, num2) {
  console.log('Subtract:', num1, num2);
  return num1 - num2;
}
// This is a private function
function privateLog() {
  console.log('Private Function');
}
```

* Agregando la palabra `export` al final del código conteniendo los nombres de las funciones y variables que queremos exportar. Por ejemplo:

```javascript
// utils.js
function multiply(num1, num2) {
  console.log('Multiply:', num1, num2);
  return num1 * num2;
}
function divide(num1, num2) {
  console.log('Divide:', num1, num2);
  return num1 / num2;
}
// This is a private function
function privateLog() {
  console.log('Private Function');
}
export {multiply, divide};
```

### Importando un Módulo
De forma similar a exportar un módulo, existen dos formas de importar un módulo usando la palabra clave import. Por ejemplo:

* Importando múltiples items de una sola vez:
```javascript
// main.js
// importing multiple items
import { sum, multiply } from './utils.js';
console.log(sum(3, 7));
console.log(multiply(3, 7));
```

* Importando todo el módulo:
```javascript
// main.js
// importing all of module
import * as utils from './utils.js';
console.log(utils.sum(3, 7));
console.log(utils.multiply(3, 7));
```

### Las importaciones y exportaciones pueden hacerse con alias

Si quieres evitar conflictos de nombres, puedes cambiar el nombre de exportación tanto durante la exportación como la importación. Por ejemplo:

* Renombrando una exportación
```javascript
// utils.js
function sum(num1, num2) {
  console.log('Sum:', num1, num2);
  return num1 + num2;
}
function multiply(num1, num2) {
  console.log('Multiply:', num1, num2);
  return num1 * num2;
}
export {sum as add, multiply};
```

* Renombrando una importación
```javascript
// main.js
import { add, multiply as mult } from './utils.js';
console.log(add(3, 7));
console.log(mult(3, 7));
```


## Patrón Singleton
Un Singleton es una objeto que puede ser instanciado solo una vez. El patrón singleton crea una nueva instancia de una clase si es que no existe ninguna. Si una instancia ya existe, simplemente devolverá una referencia a ese objeto. Cualquier llamada repetida al constructor siempre buscará el mismo objeto.

JavaScript siempre ha tenido singletons incorporados al lenguaje. Simplemente no los llamamos singletons, los llamamos objeto literal. Por ejemplo:

```javascript
const user = {
  name: 'Peter',
  age: 25,
  job: 'Teacher',
  greet: function() {
    console.log('Hello!');
  }
};
```

Debido a que cada objeto en JavaScript ocupa una ubicación de memoria única y cuando llamamos al objeto `user`, esencialmente estamos devolviendo la referencia a este objeto.

El patrón singleton puede ser implementado usando la función constructora. Por ejemplo:

```javascript
let instance = null;
function User(name, age) {
  if(!instance) {
    instance = this;
  }
  this.name = name;
  this.age = age;
  
  return instance;
}
const user1 = new User('Peter', 25);
const user2 = new User('Mark', 24);
// prints true
console.log(user1 === user2);
``` 

Cuando esta función constructora es llamada, revisa si el objeto `instance` existe o no. Si el objeto no existe, le asigna la variable `this` a la variable `instance`. Y si el objeto existe, sólo devuelve ese objeto.

Los Singletons también pueden ser implementados usando el patrón de módulos. Por ejemplo:

```javascript
const singleton = (function() {
  let instance;
  
  function User(name, age) {
    this.name = name;
    this.age = age;
  }
  return {
    getInstance: function(name, age) {
      if(!instance) {
        instance = new User(name, age);
      }
      return instance;
    }
  }
})();
const user1 = singleton.getInstance('Peter', 24);
const user2 = singleton.getInstance('Mark', 26);
// prints true
console.log(user1 === user2);
```

En el código anterior, estamos creando una nueva instancia de user llamando al método `singleton.getInstance`. Si ya existe una instancia, este método simplemente devuelve esa instancia, si la instancia no existe, crea una nueva instancia llamando a la función de constructor de `user`.

## Patrón Factory
El Patrón factory es un patrón que utiliza métodos de fábrica para crear objetos sin especificar la clase exacta o la función constructora a partir de la cual se creará el objeto.

El Patrón factory es usado para crear objetos sin exponer la lógica de instanciación. Esto patrón puede ser usado cuando necesitemos generar diferentes objetos dependiendo de una condición específica. Por ejemplo:

```javascript
class Car{
  constructor(options) {
    this.doors = options.doors || 4;
    this.state = options.state || 'brand new';
    this.color = options.color || 'white';
  }
}
class Truck {
  constructor(options) {
    this.doors = options.doors || 4;
    this.state = options.state || 'used';
    this.color = options.color || 'black';
  }
}
class VehicleFactory {
  createVehicle(options) {
    if(options.vehicleType === 'car') {
      return new Car(options);
    } else if(options.vehicleType === 'truck') {
      return new Truck(options);
      }
  }
}
```

Aquí he creado una clase `car` y otra `Truck` (con algunos valores por defecto). Y definí una clase `Vehiclefactory` para crear y devolver un nuevo objeto basado en la propiedad `vechicleType` recibida como parámetro en el objeto `options`.

```javascript
const factory = new VehicleFactory();
const car = factory.createVehicle({
  vehicleType: 'car',
  doors: 4,
  color: 'silver',
  state: 'Brand New'
});
const truck= factory.createVehicle({
  vehicleType: 'truck',
  doors: 2,
  color: 'white',
  state: 'used'
});
// Prints Car {doors: 4, state: "Brand New", color: "silver"}
console.log(car);
// Prints Truck {doors: 2, state: "used", color: "white"}
console.log(truck);
```

He creado un nuevo objeto `factory` de la clase `VehicleFactory`. Después de eso podemos crear un nuevo objeto `Car` o `Truck` llamando a `factory.createVehicle` y pasándole un objeto `options` con una propiedad `vehicleType` con el valor de `car` o `truck`.


## Patrón Decorador
El patrón decorador es usado para extender la funcionalidad de un objeto sin tener que modificar la clase existente o la función constructora. Este patrón se puede usar para agregar características a un objeto sin tener modificar el código subyacente que los usa.

Un ejemplo sencillo de este patrón podría ser:
```javascript
function Car(name) {
  this.name = name;
  // Default values
  this.color = 'White';
}
// Creating a new Object to decorate
const tesla= new Car('Tesla Model 3');
// Decorating the object with new functionality
tesla.setColor = function(color) {
  this.color = color;
}
tesla.setPrice = function(price) {
  this.price = price;
}
tesla.setColor('black');
tesla.setPrice(49000);
// prints black
console.log(tesla.color);
```
Un ejemplo más práctico de este patrón podría ser:

Vamos a suponer, que el costo de un auto difiere dependiendo del número de características que posea. Sin el patrón decorador, tendríamos que crear diferentes clases para diferentes combinaciones de características, y cada una tendría un método `cost` para calcular el costo. por ejemplo:
```javascript
class Car() {
}
class CarWithAC() {
}
class CarWithAutoTransmission {
}
class CarWithPowerLocks {
}
class CarWithACandPowerLocks {
}
```

Pero con el patrón decorador, podemos crear un automóvil de clase base y agregar el costo de una configuración diferente a su objeto mediante las funciones de decorador. Por ejemplo:

```javascript
class Car {
  constructor() {
  // Default Cost
  this.cost = function() {
  return 20000;
  }
}
}
// Decorator function
function carWithAC(car) {
  car.hasAC = true;
  const prevCost = car.cost();
  car.cost = function() {
    return prevCost + 500;
  }
}
// Decorator function
function carWithAutoTransmission(car) {
  car.hasAutoTransmission = true;
   const prevCost = car.cost();
  car.cost = function() {
    return prevCost + 2000;
  }
}
// Decorator function
function carWithPowerLocks(car) {
  car.hasPowerLocks = true;
  const prevCost = car.cost();
  car.cost = function() {
    return prevCost + 500;
  }
}
```

Primero, creamos una clase base `Car` para crear los objetos `Car`. Entonces, creamos el decorador para la característica que queramos agregarle y le pasamos el objeto `Car` como parámetro. Entonces sobreescribimos la función `cost` de ese objeto que devuelve el costo actualizado del auto y agrega una nueva propiedad a ese objeto para indicar que característica ha sido agregada.

Para agregar una nueva característica, podemos hacer algo como esto:

```javascript
const car = new Car();
console.log(car.cost());
carWithAC(car);
carWithAutoTransmission(car);
carWithPowerLocks(car);
```

Al final, podemos calcular el costo del auto de esta manera:
```javascript
// Calculating total cost of the car
console.log(car.cost());
```


## Conclusión
Hemos aprendido varios patrones de diseño usados en JavaScript, pero hay patrones que no se han cubierto aquí, que pueden ser implementados en JavaScript.

Si bien es importante conocer varios patrones de diseño, también es igualmente importante no abusar de ellos. Antes de usar un patrón de diseño, debes considerar cuidadosamente si tu problema se ajusta o no a ese patrón de diseño. Para saber si un patrón se ajusta a tu problema, debes estudiar el patrón de diseño así como las aplicaciones de ese patrón de diseño.






