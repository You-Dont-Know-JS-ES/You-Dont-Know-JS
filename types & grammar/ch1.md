# You Don't Know JS: Types & Grammar

# Capítulo 1: Tipos

La mayoría de desarrolladores dirían que un lenguaje dinámico (como JS) no tiene _tipos_. Revisemos lo que la especificación ES5.1 (http://www.ecma-international.org/ecma-262/5.1/) tiene que decir al respecto:

> Los algoritmos en esta especificación manipulan valores para los cuales cada uno tiene un tipo asociado. Los tipos de valor posibles son exactamente los definidos en esta cláusula. Además, los tipos están sub-clasificados entre tipos del lenguaje ECMAScript y tipos de la especificación.
>
> Un tipo del lenguaje ECMAScript corresponde a valores que son manipulados directamente por un programador de ECMASCript usando el lenguaje ECMAScript. Los tipos del lenguaje ECMAScript son Undefined, Null, Boolean, String y Object.

Ahora bien, si usted es un fan de los lenguajes fuertemente tipados (tipados estáticamente), usted podría objetar el uso de la palabra "tipo". En esos lenguajes, "tipo" significa _mucho_ más que lo que significan acá en JS.

Algunas personas dicen que JS no debería decir que tiene "tipos", en lugar de ello deberían llamarlos "etiquetas" o, tal vez, "subtipos".

Bah! Usaremos esta definición a grandes rasgos (la misma que parece seguir la redacción de la especificación): un _tipo_ es un conjunto de características intrínsecas e incorporadas que identifican la conducta de un valor particular y lo distingue de otros valores, tanto para el motor **como para el desarrollador**.

En otras palabras, si tanto el motor como el desarrolador tratan el valor `42` (el número) diferente de como tratan el valor `"42"` (la cadena), entonces esos dos valores tienen _tipos_ distintos -- `number` y `string`, resepctivamente. Cuando usted usa `42`, usted tiene la _intención_ de hacer algo numérico, cómo matemáticas. Pero cuando usa `"42"`, usted tiene la _intención_ de hacer algo alusivo a la cadena, como generarlo en pantalla, etc. **Estos dos valores tienen tipos distintos.**

De ninguna forma esto es una definición perfecta. Pero es suficientemente buena para esta discusión. Y es consistente con como JS se describe a sí mismo.

# Un tipo con cualquier otro nombre...

Más allá de los desacuerdos academicos al definir un tipo, ¿Por que es importante si JavaScript tiene _tipos_ o no?

Tener un entendimiento apropiado de cada tipo _tipo_ y su comportamiento es escencial para entender como convertir de manera precisa y apropiada valores de diferentes tipos (ver Coercion, Capitulo 4). Casi cualquier programa escrito en JS va a necesitar manejar la coerción del valor de alguna forma, por lo tanto es importante que lo haga responsablemente y con confianza.

Si tiene un `number` con valor de `42`, pero quieres tratarlo como un `string`, como extraer el caracter 2 de la posición 1, obviamente primero debes convertir (coercionar) el valor de `number` a `string`.

Eso parece bastante simple

Pero hay muchas maneras diferentes en las que la  coerción puede ocurrir. Algunas son explicitas, faciles de entender y predecibles pero si no es cuidadoso, la coerción puede ocurrir en maneras muy extrañas y sorprendentes

Confundirse con la coerción es una de las mas produndas frustraciones de los desarrolladores JavaScript. A menudo ha sido criticada por ser tan peligrosa al grado de ser considerada un defecto en el diseño del lenguaje por el cual deberia ser rechazado y evitado

Armados con un entendimiento completo de los Tipos en JavaScript, nuestro objetivo es demostrar por que la mala reputacion de la coerción es exagerada y algo inmerecida. Buscamos cambiar su perspectiva para que aprecie el poder y la usabilidad de la coerción pero primero debemos tener mayor conocimiento sobre valores y tipos

## Tipos de datos

JavaScript define 7 tipos de datos incorporados:

- `null`
- `undefined`
- `boolean`
- `number`
- `string`
- `object`
- `symbol` -- añadido en ES6!

**Nota:** Todos estos tipos a excepcion de `object` son llamados "primitivos".

El operador `typeof` retorna el tipo del valor dado y siempre devuelve un de los siete valores como un string -- sorprendente, no hay una coincidencia exacta de 1 a 1 con los siete tipos integrados que acabamos de mencionar.

```js
typeof undefined === "undefined"; // true
typeof true === "boolean"; // true
typeof 42 === "number"; // true
typeof "42" === "string"; // true
typeof { life: 42 } === "object"; // true

// Añadido en ES6!
typeof Symbol() === "symbol"; // true
```

Estos seis tipos mostrados tienen valores del tipo correspondiente y devuelven un string con el del mismo nombre, `Symbol` es un nuevo tipo de dato añadido en ES6, y va a ser tratado en el capitulo 3.

Como habra notado, excluí `null` del listado anterior. Es _especial_ -- especial en el sentido de que se comporta de manera defectuosa si se combina con el operador `typeof`:

```js
typeof null === "object"; // true
```

Hubiera sido bueno (y correcto) si retornara `"null"`
pero este bug in JS ha persistido por decadas y muy probablemente jamas sera resuelto por que hay mucho contenido en la web que se apoya en este comportamiento defectuoso que al ser "resuelto" _crearía_ montones de "bugs" que romperian mucho softaware.

Si necesita revisar si un valor es `null` usando su tipo necesita utilizar la siguiente condicion.

```js
var a = null;

!a && typeof a === "object"; // true
```

`null` es el único valor primitivo que es "falsy" (aka false-like; mira el Capitulo 4) pero tambien devuelve `"object"` si usas el operador `typeof`.

Entonces, ¿cuál es el séptimo `string` que puede devolver `typeof`?

```js
typeof function a() {
  /* .. */
} === "function"; // true
```

Es fácil pensar que `function` es un tipo de nivel superior en JS, especialmente dado este comportamiento del operador `typeof`. Sin embargo, si lee la especificación, verá que en realidad es un "subtipo" de objeto. Específicamente, una función se conoce como un "objeto invocable": un objeto que tiene una propiedad interna `[[Call]]` que permite que sea llamado.

El hecho de que las funciones sean en realidad objetos es bastante útil. Lo más importante, es que pueden tener propiedades. Por ejemplo:

```js
function a(b, c) {
  /* .. */
}
```

El objeto de función tiene una propiedad `length` que devuelve el número de parámetros formales con los que se declara.

```js
a.length; // 2
```

Como declaró la función con dos parámetros con nombre formales (`b` y` c`), el "length" de la función es `2`.

¿Qué pasa con los "Arrays"? ¿Son nativos de JS o son un tipo especial?

```js
typeof [1, 2, 3] === "object"; // true
```

No, son solo objetos. Es más apropiado pensar en ellos como un "subtipo" de objeto (vea el Capítulo 3), en este caso con las características adicionales de ser indexados numéricamente (en lugar de simplemente ser codificados en cadena como objetos simples) y mantener actualizada automaticamente la propiedad `.length`.

## Valores como Tipos

En JavaScript, las variables no tienen tipos: ** los valores tienen tipos **. Las variables pueden contener cualquier valor, en cualquier momento.

Otra forma de pensar acerca de los tipos JS es que JS no tiene "tipado forzado", ya que el motor no insiste en que una variable siempre tenga valores del mismo tipo con el que comienza. Una variable puede, en una declaración de asignación, contener un `string`, y en la siguiente contener un `number`, y así sucesivamente.

El _valor_ `42` tiene un tipo intrínseco de `number`, y su _tipo_ no se puede cambiar. Se puede crear otro valor, como `" 42 "` con el tipo `string`, a partir del valor del `number` `42` a través de un proceso llamado **coerción** (ver Capítulo 4).

Si usa `typeof` con una variable, no se pregunta "¿cuál es el tipo de la variable?" como puede parecer, ya que las variables JS no tienen tipos. En cambio, se pregunta "¿cuál es el tipo de valor _en_ la variable?"

```js
var a = 42;
typeof a; // "number"

a = true;
typeof a; // "boolean"
```

El operador `typeof` siempre devuelve un string. Asi:

```js
typeof typeof 42; // "string"
```

El primer `typeof 42` devuele `"number"`, y `typeof "number"` es `"string"`.

### `undefined` vs "undeclared"

Las variables que no tienen un valor _actualmente_, en realidad tiene el valor `undefined`. Llamar `typeof` con esas variables devolvera `"undefined"`:

```js
var a;

typeof a; // "undefined"

var b = 42;
var c;

// later
b = c;

typeof b; // "undefined"
typeof c; // "undefined"
```

Es tentador para la mayoría de los desarrolladores pensar en la palabra "indefinido" y considerarlo como un sinónimo de "no declarado". Sin embargo, en JS, estos dos conceptos son bastante diferentes.

Una variable "indefinida" es aquella que se ha declarado en el ámbito o "scope" accesible, pero _en este momento_ no tiene ningún valor. Por el contrario, una variable "no declarada" es aquella que no se ha declarado formalmente en el alcance accesible.

Considera:

```js
var a;

a; // undefined
b; // ReferenceError: b is not defined
```

Una confusión molesta es el mensaje de error que los navegadores asignan a esta condición. Como puede ver, el mensaje es "b is not defined", que por supuesto es muy fácil y razonable confundir con "b is undefined". Una vez más, "undefined" y "is not defined" son cosas muy diferentes. ¡Sería bueno que los navegadores dijeran algo como "no se encuentra b" o "no se ha declarado b", para reducir la confusión!

También hay un comportamiento especial asociado con `typeof` ya que se relaciona con variables no declaradas que refuerza aún más la confusión. Observa:

```js
var a;

typeof a; // "undefined"

typeof b; // "undefined"
```

El operador `typeof` devuelve `undefined` incluso para variables "no declaradas" (o "not defined"). Observe que no se produjo ningún error cuando ejecutamos `typeof b`, a pesar de que` b` es una variable no declarada. Este es un candado de seguridad especial en el comportamiento de `typeof`.

Similar a lo anterior, hubiera sido bueno si `typeof` usado con una variable no declarada devolviera "undeclared" en lugar de combinar el valor del resultado con el caso diferente `undefined`.

### `typeof` Undeclared

Sin embargo, esta protección de seguridad es una característica útil cuando se trata de JavaScript corriendo en el navegador, donde múltiples archivos de texto pueden cargar variables al espacio global compartido.

**Nota:** Muchos desarrolladores creen que nunca debería haber ninguna variable en el espacio de nombres global, y que todo debería estar contenido en módulos y en espacios privados o separados. Esto es genial en teoría pero casi imposible en la practica; ¡todavía es un buen objetivo por el que luchar! Afortunadamente, ES6 agregó soporte para utilizar módulos, lo que eventualmente lo hará mucho más práctico.

Como un ejemplo simple, imagine tener un "modo de depuración" en su programa que está controlado por una variable global llamada `DEBUG`. Debería verificar si esa variable se declaró antes de realizar una tarea de depuración, como registrar un mensaje en la consola. Una declaración global `var DEBUG = true` solo se incluiría en un archivo" debug.js ", que solo se carga en el navegador cuando está en desarrollo / prueba, pero no en producción.

Sin embargo, debe tener cuidado en cómo verifica la variable global `DEBUG` en el resto del código de su aplicación, para que no arroje un `Reference Error`. El candado de seguridad en `typeof` es nuestro amigo en este caso.

```js
// oops, esto deberia arrojar un error!
if (DEBUG) {
  console.log("Debugging is starting");
}

// esta es una manera segura de checar su existencia
if (typeof DEBUG !== "undefined") {
  console.log("Debugging is starting");
}
```

Este tipo de verificación es útil incluso si no se trata de variables definidas por el usuario (como `DEBUG`). Si está revisando las características de una API incorporada, también puede ser útil para verificarla sin lanzar un error:

```js
if (typeof atob === "undefined") {
  atob = function() {
    /*..*/
  };
}
```

**Nota:** Si está usando un "polyfill" para una característica que aun no es estandar, probablemente desee evitar usar `var` para hacer la declaración `atob`. Si declara `var atob` dentro de la declaración` if`, esta declaración se iza (vea el título _Scope & Closures_ de esta serie) en el alcance o scope global, incluso si la condición `if` no pasa (¡porque el global `atob` ya existe!). En algunos navegadores y para algunos tipos especiales de variables globales (a menudo llamadas "objetos host"), esta declaración duplicada puede arrojar un error. La omisión de `var` evita esta declaración de elevación.

Otra forma de hacer estas comprobaciones contra variables globales pero sin la protección de seguridad de `typeof` es observar que todas las variables globales también son propiedades del objeto global, que en el navegador es básicamente el objeto `window`. Entonces, las comprobaciones anteriores podrían haberse realizado (con bastante seguridad) como:

```js
if (window.DEBUG) {
  // ..
}

if (!window.atob) {
  // ..
}
```

A diferencia de hacer referencia a variables no declaradas, no se genera 'ReferenceError' si intenta acceder a una propiedad de un objeto (incluso en el objeto global `window`) que no existe.

Por otro lado, hacer referencia manual a la variable global con una referencia de `window` es algo que algunos desarrolladores prefieren evitar, especialmente si su código necesita ejecutarse en múltiples entornos JS (no solo navegadores, sino también node.js del lado del servidor, por ejemplo), donde el objeto global no siempre se puede llamar `window`.

Técnicamente, esta protección de seguridad en `typeof` es útil incluso si no está utilizando variables globales, aunque estas circunstancias son menos comunes, y algunos desarrolladores pueden encontrar este enfoque de diseño menos deseable. Imagine una función de utilidad que desea que otros copien y peguen en sus programas o módulos, en la que desea verificar si el programa incluido ha definido una determinada variable (de la que puede disponer) o no:


```js
function doSomethingCool() {
  var helper =
    typeof FeatureXYZ !== "undefined"
      ? FeatureXYZ
      : function() {
          /*.. default feature ..*/
        };

  var val = helper();
  // ..
}
```

`doSomethingCool()` revisa la existencia de la variable `FeatureXYZ`, y si la encuentra, la usa, pero si no, usa la propia. Ahora, si alguien incluye esta utilidad en su modulo checa de manera segura si se ha definido `FeatureXYZ` o no:

```js
// A IIFE (ver el tema "Immediately Invoked Function Expressions"
// en el libro *Scope & Closures* de estas series)
(function() {
  function FeatureXYZ() {
    /*.. my XYZ feature ..*/
  }

  // include `doSomethingCool(..)`
  function doSomethingCool() {
    var helper =
      typeof FeatureXYZ !== "undefined"
        ? FeatureXYZ
        : function() {
            /*.. default feature ..*/
          };

    var val = helper();
    // ..
  }

  doSomethingCool();
})();
```

Aquí, `FeatureXYZ` no es una variable global, pero todavía estamos usando la protección de seguridad de `typeof` para que sea seguro verificarlo. Y lo más importante, aquí  _no_ hay un objeto que podemos usar (como lo hicimos con las variables globales con `window .___`) para hacer la verificación, por lo que `typeof` es bastante útil.

Otros desarrolladores preferirían un patrón de diseño llamado "inyección de dependencia", donde en lugar de que `doSomethingCool()` inspeccione implícitamente que `FeatureXYZ` este definida a su alrededor, necesitaría que la dependencia se pasara explícitamente, como:

```js
function doSomethingCool(FeatureXYZ) {
  var helper =
    FeatureXYZ ||
    function() {
      /*.. default feature ..*/
    };

  var val = helper();
  // ..
}
```

Hay muchas alternativas para diseñar dicha funcionalidad. Ningún patrón aquí es "correcto" o "incorrecto": hay varios pros y contras para cada enfoque. Pero en general, es bueno que el candado de seguridad de `typeof` con una variable no declarada nos brinde más opciones.

## Resumen

JavaScript tiene siete _tipos_ incorporados: `null`,` undefined`, `boolean`,` number`, `string`,` object`, `symbol`. Pueden ser identificados por el operador `typeof`.

Las variables no tienen tipos, pero los valores guardados en ellas sí. Estos tipos definen el comportamiento intrínseco de los valores.

Muchos desarrolladores supondrán que "undefined" y "undeclared" son más o menos lo mismo, pero en JavaScript son bastante diferentes. `undefined` es un valor que puede contener una variable declarada. "undeclared" significa que una variable nunca ha sido declarada.

Desafortunadamente, JavaScript combina estos dos términos, no solo en sus mensajes de error que arroja la consola ("ReferenceError: a is not defined") sino también en los valores que devuelve `typeof`, que es `"undefined"` para ambos casos.

Sin embargo, la protección o candado de seguridad (que evita un error) en `typeof` cuando se usa contra una variable no declarada puede ser útil en ciertos casos.
