---
layout: post
title: WTF is this - Enteniendo que es this, call, apply, and bind en JavaScript.
---

*[original en ingles](https://tylermcginnis.com/this-keyword-call-apply-bind-javascript/) por Tyler McGinnis*

Uno de los aspectos más incomprendidos de JavaScript es la palabra clave . En esta publicación, aprenderás cinco reglas para descubrir a qué se refiere esta palabra clave `this`. Binding implícito, binding explícito, el nuevo binding, el window binding, y el binding léxico. Al cubrir estas técnicas, también aprenderás otras partes confusas de JavaScript, como `.call`, `.apply`, `.bind` y la palabra clave `new`.

## Post

Antes de zambullirse en los detalles de la palabra clave `this` en JavaScript, es importante dar un paso atrás y ver primero por qué a palabra clave `this` existe en primer lugar. La palabra clave `this` te permite reutilizar funciones con diferentes contextos. Dicho de otra manera, La palabra clave `this` te permite decidir qué objeto debe ser focal cuando se invoca una función o un método. Todo de lo que hablemos después de esto se basará en esa idea. Queremos poder reutilizar funciones o métodos en diferentes contextos o con diferentes objetos.

Lo rpimero que veremos es cómo decir a que esta referenciando la palabra clave `this`. La primera y más importante pregunta que uno debe hacerse cuando intenta responder a esta pregunta es "¿Dónde se esta invocando esta función?". La única manera de saber a qué esta referenciando la palabra clave `this` es mirar dónde se invoca la función que usa la palabra clave `this`.

Para demostrar esto con un ejemplo con el que ya estás familiarizado, digamos que tuvimos una función de `greet` que recibió un nombre y un mensaje de bienvenida.

```javascript
function greet (name) {
  alert(`Hello, my name is ${name}`)
}
```

Si te preguntaran que imprimirá `greet` en el alert, ¿Cuál sería tu respuesta?. Dada solo la definición de la función, es imposible saberlo. Para saber qué es `name`, tendrías que mirar la invocación de la función `greet`.

```javascript
greet('Tyler')
```

Es exactamente la misma idea para descubrir a qué esta referenciando la palabra clave "this". Incluso puedes pensar en la palabra clave "this" como lo harías con un argumento normal de una función: cambiará según la forma en que se invoca la función.

Ahora que conoces el primer paso para averiguar a qué se refiere la palabra clave "this" es mirar dónde se invoca la función, ¿qué sigue? Para ayudarnos con el próximo paso, vamos a establecer 5 reglas o pautas.

1. Implicit Binding
2. Explicit Binding
3. new Binding
3. Lexical Binding
4. window Binding