---
layout: post
title: Una simple guía en español para los prototipos JavaScript
---

*[original en ingles](http://sporto.github.io/blog/2013/02/22/a-plain-english-guide-to-javascript-prototypes/) por Sebastian Porto*

Cuando recién comenzé a aprender sobre el Modelos de objetos en JavaScript, mi reacción fue de horror e incredulidad. Me sorprendió totalmente su naturaleza de prototipos, ya que fue mi primer encuentro con un lenguaje basado en prototipos. No me ayudó que JavaScript tenga una versión única de los prototipos, ya que agrega el concepto de constructores de funciones. Apuesto a que muchos de ustedes han tenido una experiencia similar.

Pero a medida que uso más JavaScript, no solo aprendí a entender su modelo de objetos, sino que también empecé a amarlo. Gracias a JavaScript he descubierto la elegancia y flexibilidad de los lenguajes de prototipos. Ahora me encantan los lenguajes de prototipos porque tienen un modelo de objeto más simple y flexible que los lenguajes basados en clases.

## Prototipos en JavaScript
La mayoria de las guías / tutoriales empiezan explicando los objetos javaScript yendo directamente a los "constructores de funciones", y Yo creo que esto es un error, ya que introducen un concepto bastante complejo desde el principio y hacen que Javascript parezca difícil y confuso desde el principio. Vamos a dejar esto para más tarde. Primero vamos a empezar con los fundamentos de los prototipos.

## Cadena de Prototipos
Cada objeto en Javascript tiene un prototipo. Cuando un mensaje llega a un objeto, JavaScript intentará encontrar una propiedad en ese objeto primero, si no puede encontrarlo, el mensaje se enviará al prototipo del objeto y así sucesivamente. Esto funciona igual que la herencia de un solo padre en un lenguaje basado en clases.

![Cadena de prototipos](https://docs.google.com/drawings/d/1NdiIkHd9Cg2j6W4QcJ1X3DEhGXD2gacMXRuURcoE5T4/pub?w=960&h=720)

Las cadenas de herencia de prototipos pueden durar tanto como desees. Pero en general no es una buena idea hacer cadenas largas, ya que su código se puede volver difícil de entender y mantener.

## El objeto __proto__
Para entender las cadenas de prototipos en JavaScript, no hay nada tan simple como la propiedad `__proto__`. Desafortunadamente, `__proto__` no forma parte de la interfaz estándar de JavaScript, al menos hasta ES6. Así que no debes usarlo en el código de producción. Pero de todos modos hace fácil explicar prototipos.

```javascript
// vamos a crear un objeto alien
var alien = {
  tipo: 'alien'
}

//y un objeto persona
var persona = {
  tipo: 'persona'
}

// y un objeto llamado juan
var juan = {};

// asigna alien como el prototipo de juan
juan.__proto__ = alien

// juan ahora está vinculado a alien
// 'hereda' las propiedades de alien
console.log(juan.tipo); //=> ‘alien’

// Asigna persona como prototipo de juan.
juan.__proto__ = persona

// y ahora juan esta vinculado a persona
console.log(juan.tipo); //=> ‘person’
// [ 1 ]
```

Como puede ver, la propiedad `__proto__` es muy sencilla de entender y usar. Incluso si no deberíamos usar `__proto__` en el código de producción, creo que estos ejemplos proporcionan la mejor base para comprender el modelo de objetos de JavaScript.

Puedes verificar si un objeto es el prototipo de otro escribiendo:
```javascript
console.log(alien.isPrototypeOf(juan))
//=> true
```

## Las búsquedas de prototipos son dinámicas
Puede agregar propiedades al prototipo de un objeto en cualquier momento, la búsqueda de la cadena del prototipo encontrará la nueva propiedad como se esperaba.
```javascript
var persona = {}

var juan = {}
juan.__proto__ = persona

// juan no responde a "tipo" en ese punto
console.log(juan.tipo); //=> undefined

// vamos a agregar la propiedad tipo a persona
person.tipo = 'persona'

// ahora juan responde a persona
// porque encuentra la propiedad "tipo" en persona
console.log(juan.tipo); //=> 'persona'
```

## Las propiedades nuevas / actualizadas se asignan al objeto, no al prototipo
¿Qué sucede si actualiza una propiedad que ya existe en el prototipo? Veamos:

```javascript
var persona = {
  tipo: 'persona'
}

var juan = {}
juan.__proto__ = persona

juan.tipo = 'juan'

console.log(juan.tipo); //=> 'juan'
// juan ahora tiene una propiedad "tipo"

console.log(persona.tipo); //=> 'persona'
// persona no ha sido modificado
```
Ten en cuenta que la propiedad 'tipo' ahora existe tanto en person como en zack.

## Object.create
Como se explicó antes, `__proto__` no es una forma bien soportada de asignar prototipos a objetos. Así que la siguiente forma más simple es usar `Object.create()`. Esto está disponible en ES5, pero los navegadores / motores antiguos pueden parcharse utilizando este es5-shim.

```javascript
var persona = {
  tipo: 'persona'
}

// Crea un nuevo objeto cuyo prototipo es persona.
var juan = Object.create(persona);
  
console.log(juan.tipo); // => ‘persona’
```
Puedes pasar un objeto a Object.create para agregar propiedades específicas para el nuevo objeto

```javascript
var juan = Object.create(persona, {edad: {value:  13} });
console.log(juan.edad); // => ‘13’
```
Sí, el objeto que necesitas pasar es un poco complicado, pero así es como es. Ve la documentación [aquí](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Object/create).

### Object.getPrototype
Puedes obtener el prototipo de un objeto utilizando `Object.getPrototypeOf`

```javascript
var juan = Object.create(persona);
Object.getPrototypeOf(juan); //=> persona
```
No hay tal cosa como `Object.setPrototypeof`.

## Funciones Constructoras
Las funciones constructoras son la forma más utilizada en JavaScript para construir cadenas de prototipos. La popularidad de las funciones constructoras proviene del hecho de que esta era la única forma original de construir tipos. También es una consideración importante el hecho de que muchos motores están altamente optimizados para funciones constructoras.

Desafortunadamente, pueden ser confusos, en mi opinión, son una de las razones principales por las que los recién llegados encuentran JavaScript desconcertante, pero son una gran parte del lenguaje y debemos entenderlos bien.

### Funciones como constructores
En JavaScript creas una instancia de una función como esta:

```javascript
function Foo(){}

var foo = new Foo();

//foo es ahora una instancia de Foo
console.log(foo instanceof Foo ) //=> true
```
En esencia, las funciones cuando se usan con la palabra clave `new` se comportan como fábricas, lo que significa que crean nuevos objetos. El nuevo objeto que se crea está vinculado a la función por su prototipo (hablaremos más sobre esto más adelante). Así que en JavaScript llamamos a esto una instancia de la función.

### ‘this’ se asigna implícitamente
Cuando usamos *"new"*, JavaScript inyecta una referencia implícita al nuevo objeto que se está creando en la forma de la palabra clave *"this"*. También devuelve esta referencia implícitamente al final de la función.

Cuando hacemos esto:

```javascript
function Foo() {
  this.kind = ‘foo’
}

var foo = new Foo(); 
foo.kind //=> ‘foo’
```

Detrás de escena es como hacer algo como esto:


```javascript
function Foo() {
  var this = {}; // esto no es valido, es solo para ilustración
  this.__proto__ = Foo.prototype;
  
  this.kind = ‘foo’
  
  return this;
}
```

Pero ten en cuenta que el `this` implícito solo se asigna a un nuevo objeto cuando se usa `new`. Si olvida la palabra clave `new`, entonces "este" será el objeto global. Por supuesto, olvidar `new` es causa de múltiples errores, así que no olvides `new`.

Una convención que me gusta es poner en mayúscula la primera letra de una función cuando está destinada a ser utilizada como funcion constructora, con lo que sabrás inmediatamente si te falta la palabra clave `new`.

## El 'prototipo de función'
Cada función en JavaScript tiene una propiedad especial llamada _"prototype"_.

```javascript
function Foo(){
}

Foo.prototype

```

Por más confuso que parezca, esta propiedad _"prototype"_ no es el prototipo real (`__proto__`) de la función.

```javascript
Foo.__proto__ === Foo.prototype //=> false

```

Esto, por supuesto, genera mucha confusión, ya que las personas usan el término "prototype" para referirse a cosas diferentes. Creo que una buena aclaración es referirse siempre a la propiedad especial "prototype" de funciones como "prototype de función", nunca solo "prototype".

La propiedad "prototype" apunta al objeto que se asignará como el prototipo de las instancias creadas con esa función cuando se usa `new`. ¿Confuso? Esto es más fácil de explicar con un ejemplo:

```javascript
function Persona(nombre) {
  this.nombre = nombre;
}

// la función persona tiene una propiedad prototype
// podemos agregar propiedades a este prototipo de funcion
Persona.prototype.tipo = ‘persona’

// Cuando creamos un nuevo objeto usando "new"
var juan = new Person(‘Juan’);

// el prototipo del nuevo objeto apunta a Persona.prototype
juan.__proto__ == Persona.prototype //=> true

// en el nuevo objeto tenemos acceso a las propiedades definidas en Persona.prototype
juan.tipo //=> persona
```

Eso es casi todo lo que hay que saber sobre el modelo de objetos de JavaScript. Entender cómo se relacionan `__proto__` y `function.prototype` te dará innumerables horas de alegría y satisfacción, o tal vez no.