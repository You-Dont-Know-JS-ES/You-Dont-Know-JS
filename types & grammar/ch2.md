# You Don't Know JS: Types & Grammar
# Capitulo 2: Valores

`array`s, `string`s, y `number`s son los bloques más básicos de cualquier programa, pero JavaScript tiene algunas características únicas por las que estos tipos de valores pueden deleitarlo o confundirlo.

Echemos un vistazo a varios de los tipos de valores incorporados en JS, y exploremos cómo podemos comprender mejor y aprovechar mejor sus comportamientos.

## Arrays

En comparación con otros lenguajes fuertemente tipados, Los `array`s en JavaScript son sólo contenedores para cualquier tipo de valor, pueden pasar de contener `string`s a `number`s a `object`a o incluso otro `array` (Así es como creas `array`s multidimensionales).

```js
var a = [ 1, "2", [3] ];

a.length;		// 3
a[0] === 1;		// true
a[2][0] === 3;	// true
```

No es necesario que declare un tamaño determinado para sus `array`s (consulte "Arrays"en el Capítulo 3), simplemente puede declararlos y agregar valores como mejor le convenga:

```js
var a = [ ];

a.length;	// 0

a[0] = 1;
a[1] = "2";
a[2] = [ 3 ];

a.length;	// 3
```

**Advertencia:** El uso de `delete` en un valor de un `array` eliminará esa posición del `array`, pero incluso si elimina el elemento que contiene, **no** actualiza la propiedad` length`, entonces ¡ten cuidado!. Cubriremos el operador `delete` con más detalle en el Capítulo 5.

Tenga cuidado al crear `array` "dispersos" (dejando o creando espacios vacíos / faltantes):

```js
var a = [ ];

a[0] = 1;
// No se ha declarado el valor del espacio `a[1]` aquí
a[2] = [ 3 ];

a[1];		// undefined

a.length;	// 3
```

Si bien eso funciona, puede conducir a un comportamiento confuso con los "espacios vacíos" que dejas en el medio. Si bien el espacio parece tener el valor `undefined`, no se comportará de la misma manera que si la posición se definiera explícitamente (` a [1] = undefined`). Consulte "Arrays" en el Capítulo 3 para
más información.

Los `array`s están indexados numéricamente (como es de esperarse), pero lo complicado es que también son objetos que pueden tener claves/valor con `string`s agregadas (pero que no cuentan para la `longitud` de la `matriz`):

```js
var a = [ ];

a[0] = 1;
a["foobar"] = 2;

a.length;		// 1
a["foobar"];	// 2
a.foobar;		// 2
```

Sin embargo, hay que tener en cuenta que si un valor de "cadena" destinado a ser una clave se puede coercionar o convertir a un `número` de base 10 estándar, entonces se interpreta como que desea utilizarlo como un índice de `número` en lugar de que como una llave en formato de `string`!

```js
var a = [ ];

a["13"] = 42;

a.length; // 14
```

En general, no es una buena idea agregar claves/propiedades con `string`s a los `array`s. Utilice `object`s para mantener valores en claves/propiedades, y guarde` array`s para valores estrictamente indexados numéricamente.

### Lo bueno de los Arrays

Habrá ocasiones en las que necesite convertir un valor similar a un `array` (una colección de valores indexados numéricamente) en un verdadero `array`, generalmente para que pueda llamar a las utilidades del array como (como `indexOf (..)`, `concat (..)`,`forEach (..)`, etc.) y utilizarlas en su colección de valores.

Por ejemplo, varias operaciones de consulta DOM devuelven listas de elementos DOM que no son verdaderos `array`s pero que son lo suficientemente similares a los `array`s para nuestros propósitos de conversión. Otro ejemplo común es cuando las funciones utilizan el objeto `arguments` (similar a un `array`) (a partir de ES6, en desuso) para acceder a los argumentos en forma de una lista.

Una forma muy común de hacer tal conversión es tomar prestada la utilidad de los arrays `slice(..)` contra dicho valor:

```js
function foo() {
	var arr = Array.prototype.slice.call( arguments );
	arr.push( "bam" );
	console.log( arr );
}

foo( "bar", "baz" ); // ["bar","baz","bam"]
```

Si el método `slice()` es llamado sin ningún parámetro, como fue realizado en la parte de arriba, la operación default tiene el efecto de duplicar el `àrray` o en este caso el objeto similar a un array.

A partir de ES6, hay también una utilidad integrada llamada `Array.from(..)` que puede realizar la misma tarea.

```js
...
var arr = Array.from( arguments );
...
```

**Note:** `Array.from(..)` tiene muchas utilidades poderosas y será cubierto en detalle en el libro *ES6 & Beyond* de está serie.

## Strings

Es una creencia muy común que los `string`s son esencialmente sólo `array`s de caracteres. Si bien la implementación bajo las cubiertas puede o no usar `array`s, es importante darse cuenta de que las` string`s de JavaScript realmente no son lo mismo que `array`s de caracteres. La similitud es principalmente superficial.

Por ejemplo, consideremos estos dos valores:

```js
var a = "foo";
var b = ["f","o","o"];
```

Los Strings tienen una semejanza superficial con los `array`s y los objetos similares a los `array`s mencionados arriba - por ejemplo, ambos tienen una propiedad `length`, un método` indexOf(..) `(Los `array`s lo tienen sólo a partir de ES5), y un método `concat(..)`:

```js
a.length;							// 3
b.length;							// 3

a.indexOf( "o" );					// 1
b.indexOf( "o" );					// 1

var c = a.concat( "bar" );			// "foobar"
var d = b.concat( ["b","a","r"] );	// ["f","o","o","b","a","r"]

a === c;							// false
b === d;							// false

a;									// "foo"
b;									// ["f","o","o"]
```

Entonces, ambos son básicamente "arrays de caracteres", ¿verdad? **No exactamente**:

```js
a[1] = "O";
b[1] = "O";

a; // "foo"
b; // ["f","O","o"]
```

Los `string`s de JavaScript son inmutables, mientras que los `array`s son mutables. Además, la forma de acceso a la posición de carácter `a[1]` no siempre ha sido JavaScript válido. Las versiones anteriores de IE no permitían esa sintaxis (pero ahora sí). En cambio, el enfoque *correcto* ha sido `a.charAt (1)`.

Una consecuencia adicional de los `string`s inmutables es que ninguno de los métodos` string` que alteran su contenido pueden modificarlos "in situ", sino que debe crear y devolver nuevos `string`s. Por el contrario, muchos de los métodos que cambian el contenido de `array` en realidad *hacen* modificaciones "in situ".

```js
c = a.toUpperCase();
a === c;	// false
a;			// "foo"
c;			// "FOO"

b.push( "!" );
b;			// ["f","O","o","!"]
```

Además, muchos de los métodos de los `array`s que podrían ser útiles cuando se trata de `string`s no están realmente disponibles para ellos, sólo podemos "tomar prestados" los métodos de los `array` que no hacen modificaciones in situ y pasarles nuestra` string`:

```js
a.join;			// undefined
a.map;			// undefined

const a = 'foo'
const c = Array.prototype.map.call(a, letter => letter.toUpperCase() + '.').join('')

var c = Array.prototype.join.call( a, "-" );
var d = Array.prototype.map.call( a, function(v){
	return v.toUpperCase() + ".";
} ).join( "" );

c;				// "f-o-o"
d;				// "F.O.O."
```

Tomemos otro ejemplo: invertir un `string` (Casualmente, ¡una pregunta capciosa de entrevista de JavaScript común!). `array`s tiene un método de transformación in situ: `reverse()`, pero los `string`s no:

```js
a.reverse;		// undefined

b.reverse();	// ["!","o","O","f"]
b;				// ["!","o","O","f"]
```

Desafortunadamente, este "préstamo" no funciona con las funciones de los `array`s, porque los` string`s son inmutables y, por lo tanto, no pueden modificarse in situ:

```js
Array.prototype.reverse.call( a );
// Todavía devuelve un valor de tipo string (mirar Capitulo 3)
// for "foo" :(
```

Otra solución (o hack) es convertir el `string` a un `array`, realizar la operación deseada y luego convertirlo de nuevo a un `string`.

```js
var c = a
	// split `a` into an array of characters
	.split( "" )
	// reverse the array of characters
	.reverse()
	// join the array of characters back to a string
	.join( "" );

c; // "oof"
```

Si eso se siente feo, lo es. Sin embargo, funciona* para simples `string`s, por lo que si necesita algo rápido y sucio, a menudo este enfoque hace el trabajo.

**Advertencia:** ¡Ten cuidado! Este enfoque **no funciona** para `string`s con caracteres complejos (únicode) en ellas (símbolos astrales, caracteres multibyte, etc.). Necesita utilidades de biblioteca más sofisticadas que sean compatibles con Unicode para que dichas operaciones se manejen con precisión. Consulte el trabajo de Mathias Bynens sobre el tema: *Esrever* (https://github.com/mathiasbynens/esrever).

La otra forma de ver esto es: Si está haciendo más tareas en sus "strings" que las tratan básicamente como *arrays de caracteres* comúnmente, tal vez sea mejor simplemente almacenarlas como `arrays` directamente en lugar de almacenarlas como `string`s. Probablemente se ahorrará mucha molestia de convertir de 'string' a 'array' cada vez. Siempre puede llamar a `join (" ")` en el `array` *de caracteres* cuando quiera que necesite la representación `string`.

## Numbers

JavaScript tiene un sólo tipo numérico: `number`. Este tipo incluye valores "enteros" o "integers" y números decimales fracciónarios. Digo "integers" entre comillas porque durante mucho tiempo ha sido una crítica de JS que no tiene enteros verdaderos, como los hay en otros lenguajes. Eso puede cambiar en algún momento en el futuro, pero por ahora, sólo tenemos `number`s para todo.

Entonces, en JS, un "integer" es sólo un valor que no tiene un valor decimal fracciónario. Es decir, `42.0` es tanto un" número entero "como` 42`.

Como la mayoría de los lenguajes modernos, que incluyen prácticamente todos los lenguajes de scripting, la implementación del `número` de JavaScript se basa en el estándar "IEEE 754", a menudo llamado "punto flotante". JavaScript utiliza específicamente el formato de "doble precisión" (también conocido como "binario de 64 bits") del estándar.

Hay muchos comentarios excelentes en la Web sobre los detalles esenciales de cómo se almacenan los números binarios de punto flotante en la memoria, y las implicaciones de esa elección. Debido a que no es estrictamente necesario comprender los patrones de bits en la memoria para comprender cómo usar correctamente los `number`s en JS, lo dejaremos como un ejercicio para el lector interesado si desea profundizar en los detalles de IEEE 754.

### Sintaxis Numérica

Los números son expresados generalmente en JavaScript como decimales de base diez.

```js
var a = 42;
var b = 42.3;
```

La parte inicial de un valor decimal, si es `0`, es opcional:

```js
var a = 0.42;
var b = .42;
```

Del mismo modo, la parte final (el fracciónal) de un valor decimal después del `.`, si` 0`, es opcional:

```js
var a = 42.0;
var b = 42.;
```

**Warning:** `42.` es bastante infrecuente, y probablemente no sea una buena idea si está tratando de evitar confusiones cuando otras personas leen su código. Pero es, sin embargo, válido.

Por defecto, la mayoría de los `números` se generarán como decimales de base 10, con los` 0`s fracciónales finales eliminados. Entonces:

```js
var a = 42.300;
var b = 42.0;

a; // 42.3
b; // 42
```

Los `number`s muy grandes o muy pequeños serán mostrados en forma exponencial por defecto, el mismo resultado que el método `toExponential()` devuelve.

```js
var a = 5E10;
a;					// 50000000000
a.toExponential();	// "5e+10"

var b = a * a;
b;					// 2.5e+21

var c = 1 / a;
c;					// 2e-11
```

Debido a que los valores `number` se pueden "envolverse" con el contenedor de objetos` Number` (consulte el Capítulo 3), los valores `number` pueden acceder a los métodos integrados en el` Number.prototype` (consulte el Capítulo 3). Por ejemplo, el método `toFixed(..)` le permite especificar con cuántos decimales fracciónarios le gustaría que se representará el valor:

```js
var a = 42.59;

a.toFixed( 0 ); // "43"
a.toFixed( 1 ); // "42.6"
a.toFixed( 2 ); // "42.59"
a.toFixed( 3 ); // "42.590"
a.toFixed( 4 ); // "42.5900"
```

Note que el resultado es en realidad un `string` que representa un `number`, y que el valor está rellenado con '0' en el lado derecho si solicita más decimales de los que contiene el valor.

`toPrecision(..)` es similar, pero indica cuántos *dígitos significativos* deben usarse para representar el valor:

```js
var a = 42.59;

a.toPrecision( 1 ); // "4e+1"
a.toPrecision( 2 ); // "43"
a.toPrecision( 3 ); // "42.6"
a.toPrecision( 4 ); // "42.59"
a.toPrecision( 5 ); // "42.590"
a.toPrecision( 6 ); // "42.5900"
```

No tiene que usar una variable que contenga el valor para acceder a estos métodos; puede acceder a estos métodos directamente en literales `number`. Pero debe tener cuidado con el operador `.`. Dado que `.` es un carácter numérico válido, primero se interpretará como parte del literal` number`, si es posible, en lugar de interpretarse como un indicador de acceso a la propiedad.

```js
// invalid syntax:
42.toFixed( 3 );	// SyntaxError

// these are all valid:
(42).toFixed( 3 );	// "42.000"
0.42.toFixed( 3 );	// "0.420"
42..toFixed( 3 );	// "42.000"
```

`42.toFixed(3)` es un error de sintaxis por que él `.`  es interpretado como parte del literal `42.` (lo que es válido), por lo que no hay un `.` que sirva como operador de propiedad para acceder a la función `.toFixed()`.

`42..toFixed(3)` funciona porque el primer `.` es parte del `number` y el segundo `.` es el operador para acceder la propiedad. Pero probablemente se vea extraño, y de hecho es muy raro ver algo así en el código JavaScript real. De hecho, es bastante raro acceder a métodos directamente en cualquiera de los valores primitivos. Poco común no significa *malo* o *incorrecto*.

**Nota:** Hay bibliotecas que amplían el `Number.prototype` incorporado (consulte el Capítulo 3) para proporcionar operaciones adicionales en/con `number`s, por lo que en esos casos, es perfectamente válido usar algo como `10..makeItRain()` para activar una animación de lluvia de dinero de 10 segundos, o algo más tonto como eso.

Esto también es técnicamente válido (observe el espacio):

```js
42 .toFixed(3); // "42.000"
```

Sin embargo, con el literal `number`, funciona, **este es un estilo de codificación particularmente confuso** y no tendrá otro propósito que confundir a otros desarrolladores (y a su futuro yo). Evítalo.

Los `number`s también se pueden especificar en forma de exponente, lo cuál es común cuando se representan `number`s mayores, como:

```js
var onethousand = 1E3;						// Significa 1 * 10^3
var onemilliononehundredthousand = 1.1E6;	// Significa 1.1 * 10^6
```

Los `number`s literales también puede ser expresados en otras bases, como binaria, octal o decimal.

Estos formatos funcionan en las versiones actuales de JavaScript:

```js
0xf3; // hexadecimal para: 243
0Xf3; // ditto

0363; // octal para: 243
```

**Nota:** A partir de ES6 y el uso del modo `strict` o `estricto`, la sintaxis `0363` para literales en base octal no es permitida (mire abajo la nueva manera.) La sintaxis `0363` es aún permitida sin el modo `strict`, pero debes dejar de usarlo de todos modos, para ser amigable con el futuro (¡y porque ya deberías estar usando el modo `strict`!).

A partir de ES6, los siguientes nuevos formularios también son válidos:

```js
0o363;		// octal para: 243
0O363;		// ditto

0b11110011;	// binario para: 243
0B11110011; // ditto
```

Haga un favor a sus compañeros desarrolladores: nunca use la forma `0O363`, un `0` al lado de una `O` mayúscula es muy confuso. Siempre use minúsculas `0x`, `0b`, y `0o`.

### Valores decimales pequeños

El efecto secundario más famoso (o infame) de usar números binarios de punto flotante (que, recuerde, es cierto para **todos** los lenguajes que usan IEEE 754 - no *sólo* JavaScript como muchos asumen o pretenden) es:

```js
0.1 + 0.2 === 0.3; // false
```

Matemáticamente, sabemos que la declaración debería de ser `true`. ¿Porqué devuelve `false`?

En pocas palabras, las representaciones para `0.1` y` 0.2` en punto flotante binario no son exactas, por lo que cuando se agregan, el resultado no es exactamente `0.3`. Está **realmente** cerca: `0.30000000000000004`, pero si su comparación falla, "cerca"es irrelevante.

**Nota:** ¿Debería JavaScript cambiar a una implementación de `number` diferente que tenga representaciones exactas para todos los valores? Algunos piensan que sí. Se han presentado muchas alternativas a lo largo de los años. Ninguna de ellas ha sido aceptada todavía, y tal vez nunca lo sean. Tan fácil como puede parecer sólo agitar una mano y decir: "¡corrija ese error ya!", no es tan fácil. Si lo fuera, definitivamente habría cambiado hace mucho tiempo.

Ahora, la pregunta es, si algunos `números` no pueden ser *confiables* para ser exactos, ¿eso significa que no podemos usar `numbers` en absoluto? **Por supuesto no.**

Hay algunas aplicaciónes donde necesitas ser más cuidadoso, especialmente cuando lidias con valores decimales. También hay muchas (¿Quizá la mayoría?) aplicaciónes que sólo lidian con números enteros ("integers") y más exactamente sólo lidian con números hasta los millones o los trillones como máximo. Estas aplicaciónes serán **perfectamente seguras** para utilizar operaciones numéricas en JS.

¿Qué sucede si *necesitamos* comparar dos `number`'s, como `0.1 + 0.2` y `0.3` sabiendo que una comparación de igualdad fallaría?

La práctica más aceptada es usar un pequeño valor de "margen de error" como la *tolerancia* para la comparación. Este pequeño valor es frecuentemente llamado "machine epsilon", que es comúnmente `2^-52` (`2.220446049250313e-16`) para el tipo `number` in JavaScript.

Como en ES6, `Number.EPSILON` está predefinido con este valor de tolerancia, así podría querer usarlo, sin embargo puede agregar de manera segura la definición para que funcione en versiones previas a ES6.

```js
if (!Number.EPSILON) {
	Number.EPSILON = Math.pow(2,-52);
}
```

Podemos utilizar `Number.EPSILON` para comparar dos valores (con un pequeño margen de tolerancia).

```js
function numbersCloseEnoughToEqual(n1,n2) {
	return Math.abs( n1 - n2 ) < Number.EPSILON;
}

var a = 0.1 + 0.2;
var b = 0.3;

numbersCloseEnoughToEqual( a, b );					// true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 );	// false
```

El máximo valor que puede ser representado es cercano a `1.798e+308` (que es realmente, realmente enorme!), predefinido para usted como `Number.MAX_VALUE`. En el extremo más pequeño, `Number.MIN_VALUE` es cercano a `5e-324` que no es número negativo pero es muy cercano a cero!

### Rangos enteros seguros

Dada la forma de representar `number`s, hay un rango de valores "seguros" para el grupo de los números enteros y es significativamente menor que `Number.MAX_VALUE`.

Hay un máximo entero que puede ser representado de manera "segura" (Esto es, hay garantía de que el valor será representado de manera exacta) el número es `2^53 - 1`, que es `9007199254740991`, Si inserta las comas, verá que sorprendente está sobre 9 cuatrillones. Así que es un gran número para definir el rango máximo.

Este es un valor que está predefinido en ES6, al que puedes acceder como `Number.MAX_SAFE_INTEGER`. Predeciblemente, existe un mínimo número seguro, `-9007199254740991`, que está definido en ES6 como `Number.MIN_SAFE_INTEGER`.

La manera estándar en la que los programas de JS lidian con números tan grandes es cuando lidian con ID's de bases de datos de 64-bit, etc. Los números de 64-bit no pueden ser representados de manera segura con el tipo `number`, asi que deben ser almacenados y transportados en Javascript representados como `strings`.

Las operaciones numéricas de semejantes valores de ID's numéricas (Más allá de la comparación, que será precisa utilizando `strings`s) afortunadamente no son muy comunes. Pero si necesitas realizar operaciones matemáticas en este tipo de grandes valores, por ahora vas a tener que utilizar alguna utilidad de *grandes números*. Los grandes números quizá tengan soporte oficial en alguna versión futura de JavaScript.

### Evaluando enteros

Para evaluar si un valor es un entero, puedes utilizar la herramienta de ES6 `Number.isInteger(..)`:

```js
Number.isInteger( 42 );		// true
Number.isInteger( 42.000 );	// true
Number.isInteger( 42.3 );	// false
```

Para transformar `Number.isInteger(..)` para versiones anteriores a ES6:

```js
if (!Number.isInteger) {
	Number.isInteger = function(num) {
		return typeof num == "number" && num % 1 == 0;
	};
}
```

Para evaluar si un valor es un *entero seguro*, use la utilidad de ES6 `Number.isSafeInteger(..)`:

```js
Number.isSafeInteger( Number.MAX_SAFE_INTEGER );	// true
Number.isSafeInteger( Math.pow( 2, 53 ) );			// false
Number.isSafeInteger( Math.pow( 2, 53 ) - 1 );		// true
```

Para transformar `Number.isSafeInteger(..)` para navegadores con versiones anteriores a ES6:

```js
if (!Number.isSafeInteger) {
	Number.isSafeInteger = function(num) {
		return Number.isInteger( num ) &&
			Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
	};
}
```

### Enteros de 32 bits (firmados)

Si bien los números enteros pueden alcanzar hasta aproximadamente 9 billones de manera segura (53 bits), hay algunas operaciones numéricas (como los operadores bit a bit) que sólo se definen para los `numbers` de 32 bits, por lo que el "rango seguro" para los "números" usado de esa manera debe ser mucho más pequeño.

El rango entonces es `Math.pow (-2,31)` (`-2147483648`, aproximadamente -2.1 billones) hasta` Math.pow (2,31) -1` (`2147483647`, aproximadamente +2.1 billones) .

Para forzar un valor de `number` en `a` a un valor entero con signo de 32 bits, use `a | 0`. Esto funciona porque el operador bit a bit `|` sólo funciona para valores enteros de 32 bits (lo que significa que sólo tomara a 32 bits y cualquier otro bit se perderá). Entonces, "o|cerca" de cero es esencialmente una operación bit a bit no operativa.

**Nota:** Ciertos valores especiales (que cubriremos en la siguiente sección) cómo `NaN` e` Infinity` no son valores "seguros de 32 bits", ya que esos valores cuando se pasan a un operador bit a bit pasarán la operación abstracta `ToInt32` (ver Capítulo 4) y se convierte simplemente en el valor` + 0` para el propósito de esa operación bit a bit.

## Valores Especiales

Hay muchos valores especiales a lo largo de los diferentes tipos de los que el *gran* desarrollador JS necesita estar al tanto y utilizarlos apropiadamente.

### Los valores Non-value (sin valor)

Para el tipo `undefined` solamente hay un valor: `undefined`. Para el tipo `null` solamente hay un valor: `null` . Así que para ambos su nombre es su tipo y su valor.

`undefined` y `null` son frecuentemente tomados como si fueran intercambiables ya sea como valores "vacíos" o como valores "inexistentes". Otros desarrolladores prefieren distinguirlos con pequeños matices. Por ejemplo:

* `null` es un valor vacío
* `undefined` es un valor inexistente

O:

* `undefined` nunca ha tenido un valor
* `null` tuvo un valor pero ya no está ahí.

Independientemente de cómo elija "definir" y utilizar estos dos valores, `null` es un "special keyword", no un identificador, por lo que no puede tratarla como una variable a la que asignar (¿por qué lo haría?). Sin embargo, `undefined` *es* (desafortunadamente) un identificador. Uh oh.

### Undefined

Fuera del modo estricto, en realidad es posible (sin embargo increíblemente tonto y no recomendado) asignar un valor de manera global al identificador `undefined`,

```js
function foo() {
	undefined = 2; // Muy mala idea!
}

foo();
```

```js
function foo() {
	"use strict";
	undefined = 2; // TypeError!
}

foo();
```

Tanto en el modo estricto como fuera de él, puede crear una variable local de nombre `undefined`. Pero le repito que está es una terrible idea!

```js
function foo() {
	"use strict";
	var undefined = 2;
	console.log( undefined ); // 2
}

foo();
```

**Los amigos no dejan que los amigos sobrescriban `undefined`.** ¡Jamás!.

#### Operador `void`

Mientras que `undefined` es un identificador ya incorporado que contiene (a menos que se modifique, ¡vea más arriba!) El valor `undefined`, otra forma de obtener este valor es el operador `void`.

La expresión `void ___` "anula" cualquier valor, de modo que el resultado de la expresión es siempre el valor `undefined`. No modifica el valor existente; sólo asegura que no regrese ningún valor de la expresión del operador.

```js
var a = 42;

console.log( void a, a ); // undefined 42
```

Por convención (principalmente de la programación en lenguaje C), para representar el valor `undefined` de forma independiente mediante el uso de `void`, usaría `void 0` (aunque claramente incluso `void true` o cualquier otra expresión` void` hace lo mismo) No hay diferencia práctica entre `void 0`, `void 1` y `undefined`.

Pero el operador `void` puede ser útil en algunas otras circunstancias, si necesita asegurarse de que una expresión no tenga valor de resultado (incluso si tiene efectos secundarios).

Por ejemplo:

```js
function doSomething() {
	// nota: `APP.ready` es mandada por nuestra aplicación
	if (!APP.ready) {
		// intentalo más tarde
		return void setTimeout( doSomething, 100 );
	}

	var result;

	// realiza otra acción
	return result;
}

// ¿pudimos hacerlo de inmediato?
if (doSomething()) {
	// maneja las siguientes tareas de inmediato
}
```

Aquí la función `setTimeout(..)` devuelve un valor numérico (el identificador único del intervalo del temporizador, si desea cancelarlo), pero queremos `anularlo` para que el valor de retorno de nuestra función no dé un falso positivo con el condicional` if`.

Muchos desarrolladores prefieren simplemente hacer estas acciónes por separado, lo que funciona igual pero no usa el operador `void`:

```js
if (!APP.ready) {
	// intentalo más tarde
	setTimeout( doSomething, 100 );
	return;
}
```

En general, si alguna vez hay un lugar donde existe un valor (de alguna expresión) y le resulta útil que el valor sea `undefined`, use el operador` void`. Eso probablemente no será terriblemente común en sus programas, pero en los raros casos en que lo necesite, puede ser bastante útil.

### Números especiales

El tipo `número` incluye varios valores especiales. Echaremos un vistazo a cada uno en detalle.

#### NaN, Not a Number o No número

Cualquier operación matemática que realice sin que ambos operandos sean `number`s (o valores que pueden interpretarse como `number`s regulares en base 10 o base 16) dará como resultado que la operación no produzca un `number` válido, en cuyo caso obtendrá el valor `NaN`.

`NaN` literalmente significa `not a number`, aunque está etiqueta / descripción es muy pobre y engañosa, como veremos en breve. Sería mucho más preciso pensar que `NaN` es un "número inválido", un "número fallido" o incluso un "número incorrecto" que pensar que no es un número.

Por ejemplo:

```js
var a = 2 / "foo";		// NaN

typeof a === "number";	// true
```

En otras palabras: "el tipo de no-número es un `number`!" ¡Hurra por los nombres confusos y la semántica!

`NaN` es un tipo de "valor centinela" (un valor normal que se le asigna un significado especial) que representa un tipo especial de condición de error dentro del conjunto `number`. La condición de error es, en esencia: "Traté de realizar una operación matemática pero fallé, así que aquí está el resultado fallido de `number`".

Por lo tanto, si tiene un valor en alguna variable y desea probar para ver si se trata de este número especial de error `NaN`, podría pensar que podría compararlo directamente con `NaN`, como puede hacerlo con cualquier otro valor, como `null` o `undefined`. Pero no

```js
var a = 2 / "foo";

a == NaN;	// false
a === NaN;	// false
```

`NaN` es un valor muy especial porque nunca es igual a otro valor `NaN` (es decir, nunca es igual a sí mismo). Es el único valor, de hecho, que no es reflexivo (sin la característica de identidad `x === x`). Entonces, `NaN! == NaN`. Un poco extraño, ¿eh?

Entonces, ¿cómo *lo* probamos si no podemos compararlo con `NaN` (ya que esa comparación siempre fallará)?

```js
var a = 2 / "foo";

isNaN( a ); // true
```

Bastante fácil, ¿verdad? Utilizamos la utilidad global incorporada llamada `isNaN (..)` y nos dice si el valor es `NaN` o no. ¡Problema resuelto!

No tan rápido.

La utilidad `isNaN (..)` tiene una falla fatal. Parece que trató de tomar el significado de `NaN` ("No es un número ") demasiado literalmente, que su trabajo es básicamente: "probar si lo que pasó no es un `number` o es un `number`" Pero eso no es del todo exacto.

```js
var a = 2 / "foo";
var b = "foo";

a; // NaN
b; // "foo"
window.isNaN( a ); // true
window.isNaN( b ); // true -- ouch!
```

Claramente, `"foo"` es literalmente *not a `number`*, ¡pero definitivamente tampoco es el valor` NaN`! Este error ha estado en JS desde el principio (más de 19 años de *ouch*).

A partir de ES6, finalmente se ha proporcionado una utilidad de reemplazo: `Number.isNaN (..)`. Un simple polyfill para que pueda verificar de forma segura los valores `NaN` *ahora* incluso en navegadores anteriores a ES6 es:

```js
if (!Number.isNaN) {
	Number.isNaN = function(n) {
		return (
			typeof n === "number" &&
			window.isNaN( n )
		);
	};
}

var a = 2 / "foo";
var b = "foo";

Number.isNaN( a ); // true
Number.isNaN( b ); // false -- phew!
```

De echo, podemos implementar un polyfill de `Number.isNaN(..)` mucho más fácilmente, tomando ventaja del peculiar detalle de que `NaN` no es igual a si mismo. `NaN` es el *único* valor en el lenguaje donde esa afirmación es verdadera, cualquier otro valor siempre es **igual a si mismo**

Así que:

```js
if (!Number.isNaN) {
	Number.isNaN = function(n) {
		return n !== n;
	};
}
```

Raro, ¿verdad? Pero funciona!

Los `NaN`s probablemente están en muchos programas JS del mundo real, ya sea a propósito o por accidente. Es una muy buena idea usar una prueba confiable, como `Number.isNaN (..)` como se proporciona (o polirellenado), para reconocerlos correctamente.

Si actualmente está usando sólo `isNaN (...)` en un programa, la triste realidad es que su programa *tiene un error*, ¡incluso si aún no lo ha mordido!

#### Infinitos

Los desarrolladores de lenguajes compilados tradicionales como C probablemente estén acostumbrados a ver un error del compilador o una excepción de tiempo de ejecución, como "Divide by zero", para una operación como:

```js
var a = 1 / 0;
```

Sin embargo, en JS, este operador está bien definido y retorna el valor `Infinity` (alias `Number.POSITIVE_INFINITY`). Sorprendentemente:

```js
var a = 1 / 0;	// Infinity
var b = -1 / 0;	// -Infinity
```

Como puedes ver, `-Infinity` (alias `Number.NEGATIVE_INFINITY`) es el resultado de dividir por cero donde cualquiera de los dos operandos (pero no ambos) de la división es negativo.

JS utiliza representaciones numéricas finitas (punto flotante en el estándar IEEE 754, que cubrimos anteriormente), por lo que al contrario de las matemáticas puras, parece que *es posible sobrepasar incluso con una operación como suma o resta, en cuyo caso obtendría `Infinity` o `-Infinity`.

Por ejemplo:

```js
var a = Number.MAX_VALUE;	// 1.7976931348623157e+308
a + a;						// Infinity
a + Math.pow( 2, 970 );		// Infinity
a + Math.pow( 2, 969 );		// 1.7976931348623157e+308
```

De acuerdo a la especificacion, si una operación como una suma devuelve un valor que es demasiado grande para ser representado, el modo del estándar IEEE 754 "redondear-al-más-cercano" especifica cuál debe ser el resultado. Entonces, en un sentido crudo, `Number.MAX_VALUE + Math.pow (2, 969)` está más cerca de `Number.MAX_VALUE` que de` Infinity`, por lo que "se redondea hacia abajo", mientras que `Number.MAX_VALUE + Math. pow (2, 970) `está más cerca de` Infinity` por lo que "se redondea hacia arriba".

Si piensas demasiado en eso, te va a doler la cabeza. Entonces no lo hagas. En serio, para!

Una vez que sobrepasa a cualquiera de los *infinitos*, sin embargo, no hay vuelta atrás. En otras palabras, en un sentido casi poético, puedes pasar de finito a infinito pero no de infinito a finito.

Es casi filosófico preguntar: "¿Qué es el infinito dividido por el infinito". Nuestros cerebros ingenuos probablemente dirían "1" o tal vez "infinito". Resulta que ninguno es cierto. Tanto matemáticamente como en JavaScript, `Infinity / Infinity` no es una operación definida. En JS, esto da como resultado `NaN`.

Pero, ¿qué pasa con cualquier `number` finito positivo dividido por `Infinity`? ¡Eso es fácil! `0`. ¿Y qué hay de un `number` finito negativo dividido por` Infinito`? ¡Sigue leyendo!

#### Ceros

Si bien puede confundir al lector con mentalidad matemática, JavaScript tiene un cero normal `0` (también conocido como cero positivo `+ 0`) *y* un cero negativo `-0`. Antes de explicar por qué existe el `-0`, deberíamos examinar cómo lo maneja JS, porque puede ser bastante confuso.

Además de especificarse literalmente como `-0`, el cero negativo también resulta de ciertas operaciones matemáticas. Por ejemplo:

```js
var a = 0 / -3; // -0
var b = 0 * -3; // -0
```

De la suma y la resta no puede resultar un cero negativo.

Un cero negativo cuando se examina en la consola del desarrollador generalmente revelará `-0`, aunque ese no era el caso común hasta hace relativamente poco tiempo, por lo que algunos navegadores más antiguos que encuentres aún pueden mostrarlo como` 0`.

Sin embargo, si intenta convertir en `string` un valor cero negativo, siempre se informará como "0", según la especificación.

```js
var a = 0 / -3;

// (en algunos navegadores) las consolas al menos lo hacen bien
a;							// -0

// ¡pero la especificación insiste en mentirte!
a.toString();				// "0"
a + "";						// "0"
String( a );				// "0"

// extrañamente, incluso JSON entra en el engaño
JSON.stringify( a );		// "0"
```

Curiosamente, las operaciones inversas (pasar de `string` a` number`) no mienten:

```js
+"-0";				// -0
Number( "-0" );		// -0
JSON.parse( "-0" );	// -0
```

**Advertencia:** El comportamiento `JSON.stringify (-0)` de `"0"` es particularmente extraño cuando observa que es inconsistente con la operación contraria: `JSON.parse ("-0 ")` informa `- 0` como cabría esperar correctamente.

Además de que la conversión a `string` del cero negativo es engañosa para ocultar su verdadero valor, los operadores de comparación también están configurados (intencionalmente) para *mentir*.

```js
var a = 0;
var b = 0 / -3;

a == b;		// true
-0 == 0;	// true

a === b;	// true
-0 === 0;	// true

0 > -0;		// false
a > b;		// false
```

Claramente, si desea distinguir un `-0` de un` 0` en su código, no puede simplemente confiar en lo que genera la consola del desarrollador, por lo que tendrá que ser un poco más inteligente:

```js
function isNegZero(n) {
	n = Number( n );
	return (n === 0) && (1 / n === -Infinity);
}

isNegZero( -0 );		// true
isNegZero( 0 / -3 );	// true
isNegZero( 0 );			// false
```

Ahora, ¿por qué necesitamos un cero negativo, además de para curiosidades académicas?

Hay ciertas aplicaciónes en las que los desarrolladores usan la magnitud de un valor para representar una pieza de información (como la velocidad de movimiento por cuadro de animación) y el signo de ese `number` para representar otra pieza de información (como la dirección de ese movimiento).

En esas aplicaciónes, como un ejemplo, si una variable llega a cero y pierde su signo, entonces perdería la información de en qué dirección se movía antes de llegar a cero. Preservar el signo del cero evita la pérdida de información potencialmente no deseada.

### Igualdad especial

Como vimos anteriormente, el valor `NaN` y el valor` -0` tienen un comportamiento especial cuando se trata de la comparación de igualdad. `NaN` nunca es igual a sí mismo, por lo que debe usar `Number.isNaN(..)`(o un polyfill) de ES6. Del mismo modo, `-0` miente y finge que es igual (incluso con `===` | igual estricto - vea el Capítulo 4) al positivo regular `0`, por lo que debe usar la utilidad algo extraña `isNegZero (..)` que sugerimos arriba.

A partir de ES6, hay una nueva utilidad que se puede utilizar para probar dos valores de igualdad absoluta, sin ninguna de estas excepciones. Se llama `Object.is (..)`:

```js
var a = 2 / "foo";
var b = -3 * 0;

Object.is( a, NaN );	// true
Object.is( b, -0 );		// true

Object.is( b, 0 );		// false
```

Hay un polyfill bastante simple para `Object.is (..)` para entornos anteriores a ES6:

```js
if (!Object.is) {
	Object.is = function(v1, v2) {
		// test for `-0`
		if (v1 === 0 && v2 === 0) {
			return 1 / v1 === 1 / v2;
		}
		// test for `NaN`
		if (v1 !== v1) {
			return v2 !== v2;
		}
		// everything else
		return v1 === v2;
	};
}
```

`Object.is (..)` probablemente no debería usarse en los casos en que se sabe que `==` o `===` son *seguros* (mira el capitulo 4 "Coerción"), ya que es probable que estos operadores sean mucho más eficientes y ciertamente son más idiomáticos/comunes. `Object.is (..)` es principalmente para estos casos especiales de igualdad.

## Valor vs. Referencia

En muchos otros lenguajes, los valores pueden asignarse/pasarse mediante una copia de valor o una copia de referencia, según la sintaxis que utilice.

Por ejemplo, en C++ si desea pasar una variable `number` a una función y actualizar el valor de esa variable, puede declarar el parámetro de la función como `int & myNum`, y cuando pasa una variable como `x`, `myNum` será una **referencia a `x`**; Las referencias son como una forma especial de punteros, donde se obtiene un puntero a otra variable (como un *alias*). Si no declara un parámetro de referencia, el valor pasado *siempre* se copiará, incluso si se trata de un objeto complejo.

En JavaScript, no hay punteros, y las referencias funcionan de manera un poco diferente. No puede tener una referencia de una variable JS a otra variable. Eso simplemente no es posible.

Una referencia en JS apunta a un valor **(compartido)**, por lo que si tiene 10 referencias diferentes, todas son referencias distintas a un único valor compartido; **ninguno de ellos son referencias/punteros entre sí**

Además, en JavaScript, no hay sugerencias sintácticas que controlen el valor frente a la asignación/paso de referencia. En cambio, el *tipo* del valor *únicamente* controla si ese valor se asignará por copia de valor o por copia de referencia.

Vamos a ilustrar esto:

```js
var a = 2;
var b = a; // `b` es siempre una copia del valor de `a`
b++;
a; // 2
b; // 3

var c = [1,2,3];
var d = c; // `d` es una referencia a ser compartida con el valor `[1,2,3]`
d.push( 4 );
c; // [1,2,3,4]
d; // [1,2,3,4]
```

Los valores sencillos (alias primitivos escalares) son *siempre* asignados o pasados by copia-valor: `null`, `undefined`, `string`, `number`, `boolean`, y el añadido en ES6 `symbol`.

Los Valores compuestos: `object`s (incluidos` array`s y todos los contenedores de objetos en caja - consulte el Capítulo 3) y `function`s - *siempre* crean una copia de la referencia al asignarla o pasarla.

En el fragmento anterior, debido a que `2` es una primitiva escalar, `a` contiene una copia inicial de ese valor, y a `b` se le asigna otra *copia* del valor. Al cambiar `b`, no está cambiando el valor en` a`.

Pero **tanto `c` como `d`** son referencias separadas al mismo valor compartido `[1,2,3]`, que es un valor compuesto. Es importante tener en cuenta que ni `c` ni` d` más "poseen" el valor `[1,2,3]`; ambos son referencias iguales iguales al valor. Por lo tanto, cuando se usa cualquiera de las referencias para modificar (`.push(4)`) el valor real del `array` compartido en sí, sólo afecta al valor compartido, y ambas referencias harán referencia al valor recién modificado` [1,2,3 , 4] `.

Como las referencias apuntan a los valores en sí mismos y no a las variables, no puede usar una referencia para cambiar el lugar donde apunta otra referencia.

```js
var a = [1,2,3];
var b = a;
a; // [1,2,3]
b; // [1,2,3]

// posteriormente
b = [4,5,6];
a; // [1,2,3]
b; // [4,5,6]
```

Cuando hacemos la asignación `b = [4,5,6]`, no estamos haciendo absolutamente nada para afectar *donde* `a` todavía hace referencia (` [1,2,3] `). Para hacer eso, `b` tendría que ser un puntero a` a` en lugar de una referencia al `array`, ¡pero tal capacidad no existe en JS!

La forma más común de tal confusión ocurre con los parámetros de la función:

```js
function foo(x) {
	x.push( 4 );
	x; // [1,2,3,4]

	// posteriormente
	x = [4,5,6];
	x.push( 7 );
	x; // [4,5,6,7]
}

var a = [1,2,3];

foo( a );

a; // [1,2,3,4]  no  [4,5,6,7]
```

Cuando pasamos el argumento `a`, asigna una copia de la referencia` a` a `x`. `x` y` a` son referencias separadas que apuntan al mismo valor `[1,2,3]`. Ahora, dentro de la función, podemos usar esa referencia para mutar el valor en sí (`push (4)`). Pero cuando hacemos la asignación `x = [4,5,6]`, esto no afecta de ninguna manera a dónde apunta la referencia inicial `a` - todavía apunta al (ahora modificado)` [1,2,3,4] `valor.

No hay forma de usar la referencia `x` para cambiar donde apunta` a`. Solo pudimos modificar el contenido del valor compartido al que apuntan `a` y` x`.

Para lograr cambiar `a` para tener el contenido del valor` [4,5,6,7] `, no puede crear un nuevo `array` y asignarla; debe modificar el valor existente del `array`:


```js
function foo(x) {
	x.push( 4 );
	x; // [1,2,3,4]

	// later
	x.length = 0; // empty existing array in-place
	x.push( 4, 5, 6, 7 );
	x; // [4,5,6,7]
}

var a = [1,2,3];

foo( a );

a; // [4,5,6,7]  not  [1,2,3,4]
```

Como puede ver, `x.length = 0` y` x.push (4,5,6,7) `no estaban creando un nuevo `array`, sino que modificaban el `array` compartido existente. Por supuesto, `a` hace referencia a los nuevos contenidos` [4,5,6,7] `.

Recuerde: no puede controlar / anular directamente la copia de valor frente a la referencia: esa semántica está controlada completamente por el tipo del valor subyacente.

Para pasar efectivamente un valor compuesto (como un `array`) por copia-valor, debe hacer una copia manualmente de él, de modo que la referencia pasada no apunte al original. Por ejemplo:

```js
foo( a.slice() );
```

`slice()..)` sin parámetros por defecto hace una copia completamente nueva (superficial) del `array`. Entonces, pasamos una referencia sólo al `array` copiado, y por lo tanto `foo(..)`no puede afectar el contenido de `a`.

Para hacer lo contrario, pasar un valor primitivo escalar de manera que se puedan ver sus actualizaciones de valor, como una referencia, debe envolver el valor en otro valor compuesto (`object`,` array`, etc.) que *se puede* pasar por copia de referencia:

```js
function foo(wrapper) {
	wrapper.a = 42;
}

var obj = {
	a: 2
};

foo( obj );

obj.a; // 42
```

Aquí, `obj` actúa como un contenedor para la propiedad primitiva escalar `a`. Cuando se pasa a `foo(..)`, se pasa una copia de la referencia `obj` y se establece en el parámetro `wrapper`. Ahora podemos usar la referencia `wrapper` para acceder al objeto compartido y actualizar su propiedad. Una vez que finaliza la función, `obj.a` verá el valor actualizado` 42`.

Se le puede ocurrir que si quisiera pasar una referencia a un valor primitivo escalar como `2`, podría simplemente colocar el valor en su envoltorio de objeto `Number` (consulte el Capítulo 3).

Es *verdadero* una copia de la referencia a este objeto `Number` *se* pasará a la función, pero desafortunadamente, tener una referencia al objeto compartido no le dará la capacidad de modificar el valor primitivo compartido, como puede esperar:

```js
function foo(x) {
	x = x + 1;
	x; // 3
}

var a = 2;
var b = new Number( a ); // o el `Object(a)` equivalente

foo( b );
console.log( b ); // 2, no 3
```

El problema es que el valor primitivo escalar subyacente es *no mutable*  (lo mismo ocurre con `String` y `Boolean`). Si un objeto `Number` contiene el valor primitivo escalar `2`, ese objeto exacto `Number` nunca puede cambiarse para contener otro valor; sólo puede crear un nuevo objeto `Number` con un valor diferente.

Cuando se usa `x` en la expresión `x + 1`, el valor primitivo escalar subyacente `2` se desempaqueta (extrae) del objeto` Number` automáticamente, por lo que la línea `x = x + 1` cambia muy sutilmente `x` de ser una referencia compartida al objeto `Number`, simplemente mantener el valor primitivo escalar` 3` como resultado de la operación de suma `2 + 1`. Por lo tanto, `b` en el exterior todavía hace referencia al objeto `Number` original no modificado/inmutable que contiene el valor `2`.

Usted *puede* agregar propiedades sobre el objeto `Number` (simplemente no cambia su valor primitivo interno), por lo que puede intercambiar información indirectamente a través de esas propiedades adicionales.

Sin embargo, esto no es tan común; probablemente la mayoría de los desarrolladores no lo considerarían una buena práctica.

En lugar de usar el objeto contenedor `Number` de está manera, probablemente sea mucho mejor usar el enfoque manual del contenedor de objetos (`obj`) como en el fragmento anterior. Eso no quiere decir que no haya usos inteligentes para los contenedores de objetos en caja como `Number`, sólo que probablemente debería preferir la forma de valor primitivo escalar en la mayoría de los casos.

Las referencias son muy poderosas pero a veces pueden interponerse en su camino, y a veces las va a necesitar donde no existen. El único control que usted tiene sobre las referencias vs el comportamiento de copia-valor es el tipo del valor por si mismo, asi que usted debe influenciar directamente el comportamiento de asignacion/pase definiendo los tipode de valores que usted desea utilizar.

## Revisión

En JavaScript, los `array`s son simplemente colecciones indexadas numéricamente de cualquier tipo de valor. Los `string`s son algo así como un "array", pero tienen comportamientos distintos y se debe tener cuidado si desea tratarlos como `array`s. Los números en JavaScript incluyen tanto "enteros" como los valores de punto flotante.

Se definen varios valores especiales dentro de los tipos primitivos.

El tipo `null` tiene sólo un valor: `null`, y del mismo modo el tipo `undefined` tiene sólo el valor `undefined`. `undefined` es básicamente el valor predeterminado en cualquier variable o propiedad si no hay otro valor presente. El operador `void` le permite crear el valor `undefined` a partir de cualquier otro valor.

`number`s incluye varios valores especiales, como` NaN` (supuestamente "Not a Number", pero realmente más apropiadamente "número inválido"); `+ nfinity` e` -Infinity`; y `-0`.

Las primitivas escalares simples (`string`s,` number`s, etc.) se asignan/pasan por copia del valor, pero los valores compuestos (`object`s, etc.) se asignan/pasan por copia de referencia. Las referencias no son como referencias/punteros en otros idiomas: nunca apuntan a otras variables/referencias, sólo a los valores subyacentes.
