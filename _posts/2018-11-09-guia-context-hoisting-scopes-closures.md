---
layout: post
title: La Guía Definitiva de Contextos de Ejecución, Hoisting, Scopes, and Closures in JavaScript.
---

*[original en ingles](https://dev.to/tylermcginnis/the-ultimate-guide-to-execution-contexts-hoisting-scopes-and-closures-in-javascript-4bih) por Tyler McGinnis*

Puede parecer sorprendente, pero en mi opinión, el concepto más importante y fundamental para entender el lenguaje JavaScript es entender el contexto de ejecución. Al aprenderlo correctamente, estarás bien posicionado para aprender temas más avanzados como *hoisting*, *scope chains*, y *closures*. Con eso en mente, ¿qué es exactamente un "contexto de ejecución"? Para entenderlo mejor, primero veamos cómo escribimos software.

Una estrategia para escribir software es dividir nuestro código en partes separadas. Si bien estas "partes" tienen muchos nombres diferentes (funciones, módulos, paquetes, etc.), todas existen para un solo propósito: separar y administrar la complejidad de nuestras aplicaciones. Ahora, en lugar de pensar como alguien que escribe un código, piensa como lo hace el motor de JavaScript cuyo trabajo es interpretar el código. ¿Podemos usar esa misma estrategia, separando el código en partes, para administrar la complejidad de interpretar el código, tal y como lo hicimos nosotros para escribirlo? Resulta que podemos y estas "partes" se denominan contextos de ejecución. __Al igual que las funciones / módulos / paquetes te permiten administrar la complejidad de la escritura de código, los Contextos de ejecución permiten al motor de JavaScript administrar la complejidad de interpretar y ejecutar tu código__. Así que ahora que conocemos el propósito de los Contextos de ejecución, las siguientes preguntas que debemos responder son ¿cómo se crean? y ¿en qué consisten?.

El primer Contexto de Ejecución que se crea cuando el motor de JavaScript ejecuta tu código se llama "Contexto de Ejecución Global". Inicialmente, este contexto de ejecución constará de dos cosas: un objeto global y una variable llamada `this`. `this` hará referencia al objeto global que será `window` si estás ejecutando JavaScript en el navegador o `global` si lo estás ejecutando en un entorno Node.

![Contexto de Ejecución Global](/images/no-code.png)

Arriba podemos ver que incluso sin ningún código, el Contexto de Ejecución Global todavía constará de dos cosas: `window` y `this`. Este es el contexto de ejecución global en su forma más básica.

Demos un paso adelante y veamos qué sucede cuando comenzamos a agregar código a nuestro programa. Comencemos agregando algunas variables.

![global-variables-in-creation-phase](/images/global-variables-in-creation-phase.png)

![global-variables-in-execution-phase](/images/global-variables-in-execution-phase.png)

¿Puedes ver las diferencias entre las dos imágenes de arriba? Lo más importante es que cada contexto de ejecución tiene dos fases separadas, una fase de creación y una fase de ejecución, y cada fase tiene sus propias responsabilidades únicas.

En la fase de Creación Global, el motor de JavaScript
1. Creará un objeto global.
2. Creará un objeto llamado "this".
3. Asignará espacio de memoria para variables y funciones.
4. Asignará a las declaraciones de variables un valor predeterminado de "undefined" mientras coloca las declaraciones de funciones en la memoria.

No es hasta la fase de ejecución donde el motor de JavaScript comienza a correr tu código línea por línea y lo ejecuta.

Podemos ver este flujo desde la fase de Creación hasta la fase de Ejecución en el GIF a continuación.

![global-execution-context-gif.gif](/images/global-execution-context-gif.gif)

Durante la fase de creación `window`y `this` son creados, a las declaraciones de variables (`name`y `handle`) se les asigna por defecto un valor de `undefined`  y cualquier declaración de función (`getUser`) es colocada completamente en la memoria. Luego, una vez que ingresamos a la fase de Ejecución, el motor de JavaScript comienza a ejecutar el código línea por línea y asigna los valores reales a las variables que ya viven en la memoria.

> Los GIF son geniales, pero no tan geniales como recorrer el código y ver el proceso por ti mismo. Porque tú lo mereces, creé el [visualizador de JavaScript](https://tylermcginnis.com/javascript-visualizer) solo para ti. Si deseas recorrer el código anterior, usa [ESTE ENLACE](https://tylermcginnis.com/javascript-visualizer/?code=var%20name%20%3D%20%27Tyler%27%0Avar%20handle%20%3D%20%27%40tylermcginnis%27%0A%0Afunction%20getUser%20%28%29%20%7B%0A%20%20return%20%7B%0A%20%20%20%20name%3A%20name%2C%0A%20%20%20%20handle%3A%20handle%0A%20%20%7D%0A%7D).

Para consolidar realmente esta idea de la fase de creación versus la fase de ejecución, registremos algunos valores después de la fase de creación y antes de la fase de ejecución.

```javascript
    console.log('name: ', name)
    console.log('handle: ', handle)
    console.log('getUser :', getUser)

    var name = 'Tyler'
    var handle = '@tylermcginnis'

    function getUser () {
    return {
        name: name,
        handle: handle
    }
    }
```

En el código anterior, ¿qué esperas que se registre en la consola? Cuando el motor de JavaScript comienza a ejecutar nuestro código línea por línea e invoca nuestros console.logs, la fase de creación ya ha pasado. Lo que eso significa es que, como vimos anteriormente, a las declaraciones de variables se les debería haber asignado un valor `undefined`, mientras que la declaración de la función ya debería estar completamente en la memoria. Entonces, tal como deberíamos esperar, el `name` y `handle` no están definidos y `getUser` es una referencia a la función en la memoria.

```javascript
console.log('name: ', name) // name: undefined
console.log('handle: ', handle) // handle: undefined
console.log('getUser :', getUser) // getUser: ƒ getUser () {}

var name = 'Tyler'
var handle = '@tylermcginnis'

function getUser () {
  return {
    name: name,
    handle: handle
  }
}

```

> Este proceso de asignar a declaraciones de variables un valor predeterminado de `undefined` durante la fase de creación es llamado __*Hoisting*__ (elevar, levantar, mover una declaracion de variable al principio del contexto).

Esperemos que hayas pensado '¡Ajá! ". Es posible que te hayan explicado el "hoisting" previamente sin mucho éxito. Lo que es confuso acerca del *"hoisting"* es que nada es realmente *"elevado"* o movido de alguna forma. Ahora que comprendes los Contextos de ejecución y que a las declaraciones de variables se les asigna un valor predeterminado de `undefined` durante la fase de Creación, deberias entender el concepto de *"hoisting"* porque eso es, literalmente, todo lo que es.

- - - -

En este punto, deberías sentirse bastante cómodo con el Contexto de ejecución global y sus dos fases, `Creación` y `Ejecución`. La buena noticia es que solo hay otro Contexto de Ejecución que necesita aprender y es casi exactamente idéntico al Contexto de Ejecución Global. Se llama el __Contexto de Ejecución de la Función__ y se crea cada vez que se __invoca__ una función.

Esto es clave. La única vez que se crea un Contexto de Ejecución es cuando el motor de JavaScript comienza a interpretar tu código (Contexto de Ejecución Global) y cada vez que se invoca una función.

Ahora, la pregunta principal que debemos responder es cuál es la diferencia entre el contexto de ejecución global y el contexto de ejecución de una función. Si recordamos lo anterior, dijimos que en la fase de Creación Global, el motor de JavaScript:

1. Creará un objeto global.
2. Creará un objeto llamado `this`.
3. Asiganara espacio de memoria a las variables y funciones.
4. Asignará a las declaraciones de variables un valor predeterminado de "undefined" mientras coloca las declaraciones de funciones en la memoria.

¿Cuál de esos pasos no tiene sentido cuando estamos hablando de un contexto de ejecución de funciones? Es el paso #1. Solo debemos tener un objeto global que se cree durante la fase de Creación del Contexto de Ejecución Global, no cada vez que se invoca una función y el motor de JavaScript crea un Contexto de Ejecución de Funciones. En vez de crear un objeto global, una cosa de la que debe preocuparse un Contexto de Ejecución de Función es que el Contexto de Ejecución Global no sean argumentos. Con esto en mente, podemos adaptar nuestra lista de antes. Cada vez que se crea un contexto de ejecución de funciones, el motor de JavaScript:

1. ~~Creará un objeto global.~~
1. Creará un objeto `arguments`.
2. Creará un objeto llamado `this`.
3. Asiganara espacio de memoria a las variables y funciones.
4. Asignará a las declaraciones de variables un valor predeterminado de "undefined" mientras coloca las declaraciones de funciones en la memoria.

Para ver esto en acción, volvamos al código que teníamos anteriormente, pero esta vez, en lugar de simplemente definir `getUser`, veamos qué sucede cuando lo invocamos.

>[Visualiza el código tú mismo](https://tylermcginnis.com/javascript-visualizer/?code=var%20name%20%3D%20%27Tyler%27%0Avar%20handle%20%3D%20%27%40tylermcginnis%27%0A%0Afunction%20getUser%20%28%29%20%7B%0A%20%20return%20%7B%0A%20%20%20%20name%3A%20name%2C%0A%20%20%20%20handle%3A%20handle%0A%20%20%7D%0A%7D%0A%0AgetUser%28%29)

![function-execution-context-gif.gif](/images/function-execution-context-gif.gif)

Tal como hablamos, cuando invocamos la función `getUser` un nuevo contexto de ejecución es creado. Durante la fase de creación del contexto de ejecución de `getUser`, el motor de JavaScript crea un objeto `this` asi como también un objeto `arguments`. Debido a que `getUser` no tiene ninguna variable, el motor de JavaScript no necesita asignar ningún espacio de memoria o "levantar" (hoist) cualquier declaración de variable.

Es posible que también hayas notado que cuando la función `getUser` termina de ejecutarse, se elimina de la visualización. En realidad, el motor de JavaScript crea lo que se llama una "Pila de ejecución" (también conocida como la "Pila de llamadas"). Cada vez que se invoca una función, se crea un nuevo Contexto de Ejecución y se agrega a la Pila de Ejecución. Cuando una función termina de ejecutarse en la fase de Creación y Ejecución, se extrae de la Pila de Ejecución. Debido a que JavaScript es de un solo hilo (lo que significa que solo se puede ejecutar una tarea a la vez), esto es fácil de visualizar. Con "JavaScript Visualizer", la pila de ejecución se muestra de forma anidada, y cada elemento anidado es un nuevo contexto de ejecución en la pila de ejecución.

>[Visualiza el código tú mismo](https://tylermcginnis.com/javascript-visualizer/?code=function%20a%20%28%29%20%7B%0A%20%20console.log%28%27In%20fn%20a%27%29%0A%20%20%0A%20%20function%20b%20%28%29%20%7B%0A%20%20%20%20console.log%28%27In%20fn%20b%27%29%0A%20%20%20%20%0A%20%20%20%20function%20c%20%28%29%20%7B%0A%20%20%20%20%20%20console.log%28%27In%20fn%20c%27%29%0A%20%20%20%20%7D%0A%20%20%20%20%0A%20%20%20%20c%28%29%0A%20%20%7D%0A%0A%20%20b%28%29%0A%7D%0A%0Aa%28%29)

![javascript-execution-stack.gif](/images/javascript-execution-stack.gif)

En este punto, hemos visto cómo las invocaciones de funciones crean su propio Contexto de Ejecución que se coloca en la Pila de Ejecución. Lo que no hemos visto todavía es cómo las variables locales juegan en eso. Cambiemos nuestro código para que nuestras funciones tengan variables locales.

>[Visualiza el código tú mismo](https://tylermcginnis.com/javascript-visualizer/?code=var%20name%20%3D%20%27Tyler%27%0Avar%20handle%20%3D%20%27%40tylermcginnis%27%0A%0Afunction%20getURL%20%28handle%29%20%7B%0A%20%20var%20twitterURL%20%3D%20%27https%3A%2F%2Ftwitter.com%2F%27%0A%0A%20%20return%20twitterURL%20%2B%20handle%0A%7D%0A%0AgetURL%28handle%29)

![local-variables.gif](/images/local-variables.gif)

Hay algunos detalles importantes para notar aquí. Primero es que cualquier argumento que pase se agregará como una variable local en el contexto de ejecución de esa función. En el ejemplo `handle` existe tanto como una variable en el Contexto de ejecución `Global` (ya que es donde se definió) como en el Contexto de ejecución getURL porque lo pasamos como un argumento. Lo siguiente es que las variables declaradas dentro de una función viven dentro del contexto de ejecución de esa función. Así que cuando creamos `twitterURL`, vivió dentro del contexto de ejecución `getURL` ya que ahí es donde se definió, no en el contexto de ejecución `Global`. Esto puede parecer obvio, pero es fundamental para nuestro próximo tema, Scopes.

- - - -

En el pasado, probablemente escuchaste una definición de "Scope" (alcance) mas o menos en la línea de "donde las variables son accesibles". Independientemente de si eso tuvo o no sentido en ese momento, con tu nuevo conocimiento sobre Contextos de ejecución y la herramienta Visualizadora de JavaScript, *Scopes* te quedará más claro que nunca. De hecho, MDN define "Scope" como "El contexto actual de ejecución". ¿Suena familiar? Podemos pensar en "Scope" o "donde las variables son accesibles" de una manera muy similar a como hemos estado pensando en los contextos de ejecución.

Aquí hay una prueba para ti. ¿Qué arrojará `bar` cuando sea logueado en el código de abajo?

```javascript
function foo () {
  var bar = 'Declared in foo'
}

foo()

console.log(bar)
```
Vamos a verlo en JavaScript Visualizer.

>[Visualiza el código tú mismo](https://tylermcginnis.com/javascript-visualizer/?code=function%20foo%20%28%29%20%7B%0A%20%20var%20bar%20%3D%20%27Declared%20in%20foo%27%0A%7D%0A%0Afoo%28%29%0A%0Aconsole.log%28bar%29)

![scope.gif](/images/scope.gif)

Cuando se invoca foo, creamos un nuevo Contexto de Ejecución en la Pila de Ejecución. La fase de `creación` crea `this`, `arguments` y le asigna a `bar` un `undefined`. Entonces pasa a la fase de `ejecución` y asigna la cadena de texto `Declared in foo` a la variable `bar`.  Despues de esto la fase de `ejecución` termina y el Contexto de Ejecución foo se saca de la pila de ejecución. Una vez que se elimina `foo` de la pila de ejecución, intentamos loguear `bar` en la consola. En ese momento, según JavaScript Visualizer, es como si `bar` ni siquiera existiera, por lo que arroja un `undefined`. Lo que esto nos demuestra es que las variables creadas dentro de una función tienen un ámbito local. Esto significa que (en su mayor parte, veremos una excepción más adelante) no se puede acceder a ellas una vez que el Contexto de Ejecución de la función haya salido de la Pila de Ejecución.

Aquí está otro. ¿Qué se registrará en la consola una vez que el código haya terminado de ejecutarse?

```javascript
function first () {
  var name = 'Jordyn'

  console.log(name)
}

function second () {
  var name = 'Jake'

  console.log(name)
}

console.log(name)
var name = 'Tyler'
first()
second()
console.log(name)
```

De nuevo, echemos un vistazo a JavaScript Visualizer.

>[Visualiza el código tú mismo](https://tylermcginnis.com/javascript-visualizer?code=function%20first%20%28%29%20%7B%0A%20%20var%20name%20%3D%20%27Jordyn%27%0A%0A%20%20console.log%28name%29%0A%7D%0A%0Afunction%20second%20%28%29%20%7B%0A%20%20var%20name%20%3D%20%27Jake%27%0A%0A%20%20console.log%28name%29%0A%7D%0A%0Aconsole.log%28name%29%0Avar%20name%20%3D%20%27Tyler%27%0Afirst%28%29%0Asecond%28%29%0Aconsole.log%28name%29)


![unique-scopes.gif](/images/unique-scopes.gif)

Nos arroja `undefined`, `Jordyn`, `Jake` y luego `Tyler`. Lo que esto nos muestra, es que puedes pensar en esto como si cada contexto de ejecución tuviese su propio y único entorno de variables. Aunque hayan otros contextos de ejecución que contengan el nombre de la variable, el motor de JavaScript buscará la variable primero en el contexto actual.

Esto plantea la pregunta, ¿qué sucede si la variable no existe en el contexto de ejecución actual? ¿El motor de JavaScript dejará de intentar buscar esa variable? Veamos un ejemplo que responderá a esta pregunta. En el siguiente código, ¿qué se va a registrar?

```javascript
var name = 'Tyler'

function logName () {
  console.log(name)
}

logName()
```

>[Visualiza el código tú mismo](https://tylermcginnis.com/javascript-visualizer/?code=var%20name%20%3D%20%27Tyler%27%0A%0Afunction%20logName%20%28%29%20%7B%0A%20%20console.log%28name%29%0A%7D%0A%0AlogName%28%29)

![parent-lookup.gif](/images/parent-lookup.gif)

Su intuición podría decir que se registrará `undefined` ya que el contexto de ejecución `logName` no tiene una variable `name` en su *scope*. Eso es justo pero está mal. Lo que sucede es que si el motor de JavaScript no puede encontrar una variable local en el contexto de ejecución de la función, buscará en el contexto de ejecución padre más cercano para esa variable. Esta cadena de búsqueda continuará hasta que el motor alcance el Contexto de ejecución global. En ese caso, si el contexto de ejecución global no tiene la variable, generará un error de referencia.

> Este proceso en el cual el motor de JavaScript revisa uno por uno y verifica en cada Contexto de Ejecución padre si una variable no existe en el Contexto de Ejecución local, se llama `Scope Chain`. El visualizador de JavaScript muestra la cadena de alcance al tener cada nuevo contexto de ejecución indentado y con un fondo de color único. Visualmente se puede ver que cualquier Contexto de Ejecución hijo puede hacer referencia a cualquier variable ubicada en cualquiera de sus Contextos de Ejecución padres, pero no al revés.

- - - -

Anteriormente, aprendimos que las variables creadas dentro de una función tienen un alcance local y que no se puede acceder (__en su mayor parte__) una vez que el Contexto de ejecución de la función ha salido de la pila de ejecución. Es hora de sumergirse en eso de "__en su mayor parte__". El único escenario donde esto no es cierto es si tienes una función anidada dentro de otra función. En este caso, la función secundaria seguirá teniendo acceso al alcance de la función externa, incluso después de que el Contexto de ejecución de la función principal se haya eliminado de la Pila de ejecución. Eso fue un montón de palabras. Como siempre, JavaScript Visualizer puede ayudarnos aquí.

>[Visualiza el código tú mismo](https://tylermcginnis.com/javascript-visualizer/?code=var%20count%20%3D%200%0A%0Afunction%20makeAdder%28x%29%20%7B%0A%20%20return%20function%20inner%20%28y%29%20%7B%0A%20%20%20%20return%20x%20%2B%20y%3B%0A%20%20%7D%3B%0A%7D%0A%0Avar%20add5%20%3D%20makeAdder%285%29%3B%0Acount%20%2B%3D%20add5%282%29)

![closure-scope.gif](/images/closure-scope.gif)

Tenga en cuenta que después de que el contexto de ejecución de `makeAdder` se haya extraído de la pila de ejecución, el visualizador de JavaScript crea lo que se llama un `Closure scope`. Dentro de ese `Closure scope` se encuentra el mismo entorno de variables que existía en el contexto de ejecución de `makeAdder`. La razón por la que esto sucedió es porque tenemos una función anidada dentro de otra función. En nuestro ejemplo, la función interna está anidada dentro de la función makeAdder, por lo que la función interna crea un cierre sobre el entorno de variable makeAdder. Incluso después de que el entorno de ejecución de `makeAdder` se haya extraído de la pila de ejecución, debido a que se creó el `Closure Scope`, la función interna tiene acceso a la variable `x` (a través del `Scope chain`).

Como supondrás, este concepto de una función secundaria que se "encierra" el entorno de variables de su función padre se denomina `Closures`.

- - - - 

## Bonus

Aquí hay algunos otros temas relacionados que sé que si no menciono que alguien lo notará.

### Variables Globales
En el navegador, cada vez que cree una variable en el contexto de ejecución global (fuera de cualquier función), esa variable se agregará como una propiedad en el objeto `window`.

Tanto en el navegador como en Node.js, si crea una variable sin una declaración (es decir, sin `var`, `let` o `const`), esa variable también se agregará como una propiedad en el objeto global.

```javascript
// In the browser
var name = 'Tyler'

function foo () {
  bar = 'Created in foo without declaration'
}

foo()

console.log(window.name) // Tyler
console.log(window.bar) // Created in foo without declaration
```

### let y const

[![](http://img.youtube.com/vi/6vBYfLCE9-Q/0.jpg)](http://www.youtube.com/watch?v=6vBYfLCE9-Q "let and const")

### La palabra clave `this`
En este artículo aprendimos que en la fase de creación de cada contexto de ejecución, el motor de JavaScript crea un objeto llamado `this`. Si deseas obtener más información acerca de por qué es importante y cómo determinar qué es la palabra clave `this`, sugiero leer: [WTF es `this`- Entendiendo la palabra clave this, call, apply y bind en JavaScript](https://tylermcginnis.com/this-keyword-call-apply-bind-javascript/).