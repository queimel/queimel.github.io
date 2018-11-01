---
layout: post
title: Guía para principiantes de prototype en JavaScript.
---

*[original en ingles](https://dev.to/tylermcginnis/a-beginners-guide-to-javascripts-prototype-5kk) por Tyler McGinnis*

## El Artículo

No puedes llegar muy lejos en JavaScript sin tener que lidiar con objetos. Son fundamentales para casi todos los aspectos del lenguaje de programación JavaScript. En este post aprenderás una variedad de patrones para instanciar nuevos objetos, y mientras lo haces, gradualmente irás aprendiendo más en profundidad el *prototype* de JavaScript.

No puedes llegar muy lejos en JavaScript sin tener que lidiar con objetos. Son fundamentales para casi todos los aspectos del lenguaje de programación JavaScript. De hecho, aprender como crear objetos es probablemente una de las primeras cosas que estudiaste cuando empezaste con el lenguaje. Con esto dicho, para de aprender sobre *prototypes* en JavaScript de forma mas efectiva, vamos a canalizar nuestro desarrollador junior interno y volver a las bases.

Los Objetos son pares de clave/valor. La forma más común de crear un objeto es con llaves `{}` y agregar propiedades y métodos a un objeto usando la notación de punto (*dot notation*).

```javascript

    let animal = {}
    animal.nombre = 'Leo'
    animal.energia = 10

    animal.comer = function (cantidad) {
    console.log(`${this.nombre} esta comiendo.`)
    this.energia += cantidad
    }

    animal.dormir = function (duracion) {
    console.log(`${this.nombre} esta durmiendo.`)
    this.energia += duracion
    }

    animal.jugar = function (duracion) {
    console.log(`${this.nombre} esta jugando.`)
    this.energia -= duracion
    }
```

Simple. Ahora las probabilidades están en nuestra aplicación, necesitaremos crear más de un animal. Naturalmente el siguiente paso para esto seria encapsular esa logica dentro de una función que podamos invocar cuando lo necesitemos, para crear un nuevo animal. Podemos llamar a este patrón `Instanciación Funcional` y llamaremos a la función en sí misma una "función constructora", ya que es responsable de "construir" un nuevo objeto.

### Instanciación Funcional

```javascript
    function Animal (nombre, energia) {
    let animal = {}
    animal.nombre = nombre
    animal.energia = energia

    animal.comer = function (cantidad) {
        console.log(`${this.nombre} esta comiendo.`)
        this.energia += cantidad
    }

    animal.dormir = function (duracion) {
        console.log(`${this.nombre} esta durmiendo.`)
        this.energia += cantidad
    }

    animal.jugar = function (duracion) {
        console.log(`${this.nombre} está jugando.`)
        this.energia -= duracion
    }

    return animal
    }

    const leo = Animal('Leo', 7)
    const snoop = Animal('Snoop', 10)
```

Ahora cuando queramos crear un nuevo animal (o hablando mas técnicamente, una nueva "instancia"), todo lo que tenemos que hacer es invocar nuestra función `Animal` y pasarle el `nombre` y el nivel de `energia`. Esto funciona genial y es increiblemente simple. Sin embargo, ¿Puedes detectar alguna debilidad en este patrón?. El más grande y el que intentaremos resolver tiene que ver con los tres métodos: `comer`, `dormir` y `jugar`. Cada uno de esos métodos no solo es dinámico, sino que también es completamente genérico. Lo que esto significa es que no hay razón para recrear esos métodos como lo estamos haciendo actualmente cuando creamos un nuevo animal.  Solo estamos desperdiciando la memoria y haciendo que cada objeto "animal" sea más grande de lo que debe ser. ¿Se te ocurre alguna solución? ¿Qué pasaría si en lugar de recrear esos métodos cada vez que creamos un nuevo animal, los movemos a su propio objeto y podamos hacer que cada animal haga referencia a ese objeto? Podemos llamar a este patrón `Instanciación Funcional con Métodos Compartidos`, verboso pero descriptivo.

### Instanciación Funcional con Métodos Compartidos

```javascript
    const metodosDeAnimal = {
        comer(cantidad) {
            console.log(`${this.nombre} esta comiendo.`)
            this.energia += cantidad
        }

        dormir(duracion) {
            console.log(`${this.nombre} esta durmiendo.`)
            this.energia += cantidad
        }

        jugar(duracion) {
            console.log(`${this.nombre} está jugando.`)
            this.energia -= duracion
        }
    }

    function Animal (nombre, energia) {
        let animal = {}
        animal.nombre = nombre
        animal.energia = energia
        animal.comer = metodosDeAnimal.comer
        animal.dormir = metodosDeAnimal.dormir
        animal.jugar = metodosDeAnimal.jugar

        return animal
    }

    const leo = Animal('Leo', 7)
    const snoop = Animal('Snoop', 10)
```

Moviendo los metodos competidos a su propio objeto y referenciando ese objeto dentro de nuestra funcion `Animal`, hemos resuelto el problema del gasto de memoria y de objetos de tipo animal demasiado grandes.

### Object.create
Vamos a mejorar nuestro ejemplo nuevamente usando `Object.create`. En pocas palabras, __Object.create permite crear un objeto que delegará en otro objeto en las búsquedas fallidas__. Dicho de otra manera, Object.create permite crear un objeto y cuando busque una propiedad en ese objeto y no la encuentre, puede consultar otro objeto para ver si ese otro objeto tiene la propiedad. Esto fue un montón de palabras. Veamos algún código.

```javascript
const padre = {
  nombre: 'Stacey',
  edad: 35,
  herencia: 'Irlandesa'
}

const hijo = Object.create(padre)
hijo.nombre = 'Ryan'
hijo.edad = 7

console.log(hijo.nombre) // Ryan
console.log(hijo.edad) // 7
console.log(hijo.herencia) // Irlandesa
```

Entonces, en el ejemplo anterior, debido a que se creó `hijo` con `Object.create(padre)`, siempre que se busque una propiedad en el hijo y esta no se encuentre, JavaScript delegará esa búsqueda en el objeto padre. Lo que esto significa es que, aunque el `hijo` no tiene la propiedad de `herencia`, el objeto `padre` si la tiene, entonces cuando se loguea `hijo.herencia`se obtiene la `herencia` de `padre`que es `Irlandesa`.

Ahora, con `Object.create` en nuestra caja de herramientas, ¿Como podemos usarlo para simplificar nuestro ejemplo anterior de `Animal`? Bueno, en vez de agregar los metodos compartidos uno por uno como ahora estamos haciendo, podemos usar `Object.create`para delegar hacia el objeto `metodosDeAnimal`. Para sonar como si fueramos muy inteligentes, llamaremos a esto `Instanciación funcional con mètodos compartidos y Object.create`

### Instanciación funcional con mètodos compartidos y Object.create

```javascript
    const metodosDeAnimal = {
        comer(cantidad) {
            console.log(`${this.nombre} esta comiendo.`)
            this.energia += cantidad
        }

        dormir(duracion) {
            console.log(`${this.nombre} esta durmiendo.`)
            this.energia += cantidad
        }

        jugar(duracion) {
            console.log(`${this.nombre} está jugando.`)
            this.energia -= duracion
        }
    }

    function Animal (nombre, energia) {
        let animal = Object.create(metodosDeAnimal)
        animal.nombre = nombre
        animal.energia = energia

        return animal
    }

    const leo = Animal('Leo', 7)
    const snoop = Animal('Snoop', 10)

    leo.jugar(10)
    snoop.play(5)
```

Entonces, ahora cuando llamemos a `leo.comer`, JavaScript buscara el metodo `comer`en el objeto `leo`. Esa búsqueda fallará y entonces, a causa de `Object.create`, delegará en el objeto `metodosDeAnimal` que es donde encontrará el metodo `comer`.

Hasta aquí todo bien. Sim embargo, todavía podemos hacer algunas mejoras. Parece un poco rebuscado tener un objeto separado (`metodosDeAnimal`) para compartir métodos a traves de las distintas instancias. Eso parece ser una característica común que deseas implementar en el propio lenguaje. Resulta que existe y es la razón por la que estás aquí: `prototype`.

Entonces, ¿Qué es exactamente `prototype`en JavaScript? Bueno, en pocas palabras, cada función en JavaScript tiene una propiedad `prototype` que hace referencia a un objeto. Decepcionante, ¿verdad? Pruebalo por ti mismo. 

```javascript
    function doThing () {}
    console.log(doThing.prototype) // {}
```

¿Qué tal si en vez de crear un objeto separado para manejar nuestros métodos (como hicimos con `metodosDeAnimal`), colocamos todos esos métodos en el `prototype`de la función `Animal`?. Entonces, todo lo que tendríamos que hacer es que, en lugar de utilizar Object.create para delegar en `metodosDeAnimal`, podríamos usarlo para delegar en `Animal.prototype`. Llamaremos a este patrón `Instanciación prototipada`.

### Instanciación prototipada

```javascript
function Animal (nombre, energia) {
  let animal = Object.create(Animal.prototype)
  animal.nombre = nombre
  animal.energia = energia

  return animal
}

Animal.prototype.comer = function (cantidad) {
  console.log(`${this.nombre} está comiendo.`)
  this.energia += cantidad
}

Animal.prototype.dormir = function (duración) {
  console.log(`${this.nombre} está durmiendo.`)
  this.energia += duración
}

Animal.prototype.jugar = function (duración) {
  console.log(`${this.nombre} está jugando.`)
  this.energia -= duración
}

const leo = Animal('Leo', 7)
const snoop = Animal('Snoop', 10)

leo.comer(10)
snoop.jugar(5)
```
Esperemos que hayas tenido un gran momento "aha". Nuevamente, `prototype` es solo una propiedad que tiene cada función en JavaScript y, como vimos anteriormente, nos permite compartir métodos en todas las instancias de una función. Toda nuestra funcionalidad es la misma de antes, pero ahora en vez de tener un objeto separado con todos los metodos, podemos usar otro objeto que iene incluido dentro de la misma función `Animal`, `Animal.prototype`.


## Veamoslo más en profundidad.

Hasta este punto aprendimos tres cosas:

* Cómo crear una función constructora.
* Cómo agregar métodos al `prototype` de la función constructora.
* Cómo usar `Object.create`para delegar las busquedas fallidas en el `prototype` de la función.

Esas tres tareas parecen bastante fundamentales para cualquier lenguaje de programación. ¿Es JavaScript realmente tan malo como para que no tenga una forma "integrada" más sencilla para lograr lo mismo? Cómo posiblemente ya supondrás, si existe, y es usando la palabra clave `new`.

Lo bueno del enfoque lento y metódico que tomamos para llegar aquí es que ahora tendrás un profundo conocimiento de lo que la palabra clave `new` en JavaScript está haciendo bajo el capó.

Volviendo a nuestro constructor `Animal`, las dos partes más importantes eran crear el objeto y devolverlo. Si no creamos el objeto con `Object.create`, no podremos delagar hacia el `prototype` de la función  en las busquedas fallidas. Sin la declaración `return`, nunca recuperaríamos el objeto creado.

```javascript
function Animal (nombre, energia) {
  let animal = Object.create(Animal.prototype)
  animal.nombre = nombre
  animal.energia = energia

  return animal
}
```

Esto es lo bueno de `new`: cuando invocas a una función usando la palabra clave `new`, esas dos líneas se crean  de manera implícita ("bajo el capó") y el objeto que se crea se llama `this`. 

Usando comentarios para mostrar que sucede bajo el capó y asumiendo que el constructor de `Animal` es llamado usando la palabra clave `new`, puede ser re-escrito así:

```javascript
function Animal (nombre, energia) {
  // const this = Object.create(Animal.prototype)

  this.nombre = nombre
  this.energia = energia

  // return this
}

const leo = new Animal('Leo', 7)
const snoop = new Animal('Snoop', 10)
```

y sin los comentarios de lo que sucede "bajo el capó"

```javascript
function Animal (nombre, energia) {
  let animal = Object.create(Animal.prototype)
  animal.nombre = nombre
  animal.energia = energia

  return animal
}

Animal.prototype.comer = function (cantidad) {
  console.log(`${this.nombre} está comiendo.`)
  this.energia += cantidad
}

Animal.prototype.dormir = function (duración) {
  console.log(`${this.nombre} está durmiendo.`)
  this.energia += duración
}

Animal.prototype.jugar = function (duración) {
  console.log(`${this.nombre} está jugando.`)
  this.energia -= duración
}

const leo = Animal('Leo', 7)
const snoop = Animal('Snoop', 10)

leo.comer(10)
snoop.jugar(5)
```

De nuevo, la razón por la que esto funciona y el objeto `this` es creado implícitamente es porque llamamos a la función constructora usando la palabra clave `new`.  Si no usas `new`cuando invocas una función , el objeto `this` nunca es creado o devuelto de forma implícita. Podemos observar este problema en el ejemplo que sigue.

```javascript
function Animal (nombre, energia) {
  this.nombre = nombre
  this.energia = energia
}

const leo = Animal('Leo', 7)
console.log(leo) // undefined
```
El nombre para este patrón es `Instanciación Seudoclásica`.

Si JavaScript no es tu primer lenguaje de programación, puede que te estés poniendo un poco inquieto.

>"Que onda este tipo? solo esta recreando una version de mierda de una clase" - Tú

Para aquellos que no esten familiarizados, una Clase permite crerar una plantilla para un objeto. Luego, cada vez que se cree una instancia de esa clase, obtendrás un objeto con las propiedades y métodos definidas en la plantilla.

¿Te suena familiar? Eso es lo que hicimos basicamente con nuestra función constructora `Animal` más arriba. Sin embargo, en vez de usar la palabra clave `class`, usamos una vieja y sencilla función JavaScript para recrear la misma funcionalidad. Por supuesto, tomó un poco de trabajo extra, así como algunos conocimientos sobre lo que sucede "bajo el capó" de JavaScript, pero los resultados son los mismos.

Pero hay buenas noticias. javaScript no es un lenguaje muerto. El comité TC-39 esta constantemente mejorando y añadiendo funcionalidades al lenguaje. Lo que es to significa es que aunque la versión inicial de JavaScript no 
soportaba clases, no existe ninguna razón para que no puedan ser añadidas a la especificación oficial. De hecho, es exactamente lo que el comité TC-30 hizo. En 2015, EcmaScript 6 (la especificación JavaScript oficial) fue lanzada con soporte para clases y la palabra reservada `class`. Veamos como nuestra función constructora `Animal` de más arriba luce con la nueva sintaxis de clase.

```javascript
class Animal {
  constructor(nombre, energia) {
    this.nombre = nombre
    this.energia = energia
  }
  comer(cantidad) {
    console.log(`${this.nombre} esta comiendo.`)
    this.energia += cantidad
  }
  dormir(duracion) {
    console.log(`${this.nombre} esta durmiendo.`)
    this.energia += duracion
  }
  jugar(duracion) {
    console.log(`${this.nombre} esta jugando.`)
    this.energia -= duracion
  }
}

const leo = new Animal('Leo', 7)
const snoop = new Animal('Snoop', 10)
```

Bastante limpio, ¿verdad?.

Entonces, si esta es la nueva forma de crear clases, ¿porqué gastamos tanto tiempo haciendolo de la antigüa forma?. La razón es que la nueva forma (con la palabra reservada `class`) es principalmente "azúcar sintáctica" sobre la forma existente que hemos llamado patrón pseudoclásico. Para entender completamente la conveniencia sintactica de las clases ES6, primero debes comprender el patrón pseudoclásico.

hasta este punto hemos cubierto las bases fundamentales de `prototype`en JavaScript. El resto de este artículo esta dedicado a entender otros topicos que es bueno saber, que estan relacionados. En otro artículo veremos como tomar estas bases y sarlas para entender como funciona la herencia en JavaScript.

## Métodos de Array

Ya hablamos en profundidad sobre cómo si deseas compartir métodos a través de instancias de una clase, debes agregar esos métodos en el `prototype` de la clase (o función). Podemos ver este mismo patrón demostrado si nos fijamos en la clase Array. Históricamente, probablemente has creado tus arreglos como este.

```javascript
const friends = []
```
Resulta que eso no es más que crear una nueva instancia de la clase Array.

```javascript
const friendsWithSugar = []

const friendsWithoutSugar = new Array()
```
Una cosa en la que quizas nunca hayas pensado, es como es posible que todas las instancias de un array tenga todos esos metodos por defecto (`splice``slice`, `pop` etc)?

Bueno, como ahora sabrás, es porque todos esos métodos viven en `Array,prototype`y cuando creas una nueva instancia de `Array`, estás usando la palabra reservada `new` que setea la delegación hacia `Array.prototype`en las busquedas fallidas.

Puedes ver todos los metodos de `Array` simplemente logueando `Array.proptotype`.

```javascript
console.log(Array.prototype)

/*
  concat: ƒn concat()
  constructor: ƒn Array()
  copyWithin: ƒn copyWithin()
  entries: ƒn entries()
  every: ƒn every()
  fill: ƒn fill()
  filter: ƒn filter()
  find: ƒn find()
  findIndex: ƒn findIndex()
  forEach: ƒn forEach()
  includes: ƒn includes()
  indexOf: ƒn indexOf()
  join: ƒn join()
  keys: ƒn keys()
  lastIndexOf: ƒn lastIndexOf()
  length: 0n
  map: ƒn map()
  pop: ƒn pop()
  push: ƒn push()
  reduce: ƒn reduce()
  reduceRight: ƒn reduceRight()
  reverse: ƒn reverse()
  shift: ƒn shift()
  slice: ƒn slice()
  some: ƒn some()
  sort: ƒn sort()
  splice: ƒn splice()
  toLocaleString: ƒn toLocaleString()
  toString: ƒn toString()
  unshift: ƒn unshift()
  values: ƒn values()
*/
```

La misma lógica que existe para los objetos. Todos los objetos delegaran hacia `Objects.prototype` en las busquedas fallidas, lo cual es la razón por la cual todos los objetos tienen métodos como `toString`y `hasOwnProperty`.

## Métodos estáticos

Hasta este punto, hemos cubierto el por qué y cómo compartir métodos entre instancias de una Clase. Sin embargo, ¿qué pasaría si tuvieramos un método que fuera importante para la Clase, pero no fuese necesario compartirlo entre instancias? Por ejemplo, ¿qué pasaría si tuvieramos una función que tomara un Array de instancias de `Animal` y determinara cual es la siguiente que debe ser alimentada?. La llamaremos `nextToEat`.

```javascript
function nextToEat (animals) {
  const sortedByLeastEnergy = animals.sort((a,b) => {
    return a.energy - b.energy
  })

  return sortedByLeastEnergy[0].name
}
```