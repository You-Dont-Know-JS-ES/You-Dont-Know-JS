# You Don't Know JS: Scope & Closures
# Capítulo 1: ¿Qué es el Ámbito(Scope)?

Uno de los paradigmas fundamentales de la mayoría de los lenguajes de programación es el de almacenar valores en variables para posteriormente recuperarlos o modificarlos. De hecho, la capacidad de almacenar y extraer estos valores de las variables es lo que da el *estado* del programa.

Sin dicha capacidad un programa aún podría realizar algunas tareas, pero el mismo sería extremadamente limitado y muy poco interesante.

Sin embargo la inclusión de las variables en nuestro programa deriva en las siguientes preguntas interesantes, las cuales vamos a responder: ¿Dónde *viven* estas variables? Es decir, ¿Dónde están almacenadas? Y sobre todo, ¿Cómo nuestro programa las busca cuando las necesita?

De estas preguntas se llega a la necesidad de tener un conjunto de reglas bien definido para almacenar las variables en algún lugar, y la forma de buscarlas en algún momento posterior. A este set de reglas lo llamaremos el *Ámbito(Scope)*.

Pero, ¿Dónde y cómo se establecen estas reglas del *Scope*?

## Teoría de Compiladores

Puede que sea evidente, o tal vez sorprendente, dependendiendo de su nivel de interacción con distintos lenguajes de programación, pero a pesar de que JavaScript es colocado en la categoría general de lenguajes "dinámicos" o "interpretados", es en realidad un lenguaje compilado. Bueno, *no* es compilado por adelantado, como la mayoría de los lenguajes tradicionalmente compilados, ni produce los resultados de la compilación portátil de los sistemas distribuidos.

Pero, y sin embargo, el motor de JavaScript realiza muchos de los mismos pasos, aunque de una manera más sofisticada de la que comúnmente estamos conscientes, que cualquier compilador de un lenguaje tradicional.

En un proceso de compilación tradicional, un trozo de código fuente, su programa, será sometido a 3 pasos típicos *antes* de su ejecución, aquellos que más o menos llamamos "compilación":

1. **Tokenización/Análisis Léxico:** Dividir una cadena de caracteres en trozos significativos (para el lenguaje), llamados *tokens* o símbolos. Por ejemplo, considera el programa `var a = 2`. Este programa puede ser dividido en los siguientes *tokens*: `var`, `a`, `=`, `2` y `;`. Los espacios en blanco puede o no ser considerados como *tokens* dependiendo de si son significativos o no.

	**Nota:** La diferencia entre tokenización y análisis léxico es sutil y académica, pero se centra en si estos tokens son identificados de una manera *sin estado* o *con estado*. En pocas palabras, si el tokenizador fuese a invocar reglas de análisis con estado para determinar si `a` debe ser considerado como un token o parte de un token, *eso* sería **análisis léxico**.

2. **Análisis Sintáctico(Parsing):** Tomando un flujo (arreglo) de *tokens* y convertiéndolos en un árbol de elementos anidados, que colectivamente representa la estructura gramatical del programa. Este árbol de llama "AST" (<b>A</b>bstract <b>S</b>yntax <b>T</b>ree).

	El árbol para `var a = 2;` puede que inicie con un nodo de alto nivel llamado `VariableDeclaration`, el cual tiene un nodo hijo llamado `Identifier` (cuyo valor es `a`), y otro nodo hijo llamado `AssignmentExpression` que a su vez tiene un nodo hijo llamado `NumericLiteral` (cuyo valor es `2`).

3. **Generación del código:** El proceso de tomar el árbol AST y transformarlo en código ejecutable. Esta parte varia dependiendo del lenguaje, la plataforma, etc.
 
	Entonces, en vez de perdernos en los detalles asumiremons que existe una manera en la que nuestro árbol AST descrito para `var a = 2;` se transforma en un conjunto de instrucciones máquina que realmente *crean* una variable llamada `a` (Incluyendo reservar la memoria, etc.) y luego almacena un valor dentro de `a`.

	**Nota:** Los detalles de como el motor maneja los recursos del sistema es mucho más profundo de lo que indagaremos, asi que daremos por sentado que el motor es capaz de crear y almacenar las variables como necesitemos.

El motor de JavaScript es mucho más complejo que *solo* estos tres pasos, al contrario de la mayoría de los compiladores de lenguajes. En cambio, entre el proceso de análisis sintáctico y generación del código hay ciertos pasos que se emplean para optimizar el rendimiento de la ejecución, incluyendo el colapsar los elementos redundantes, etc.

Entonces, aquí estoy pintando solo los grandes rasgos. Pero creo que usted verá pronto porque *estos* detalles que *cubrimos*, incluso en un alto nivel, son relevantes.

Por un momento, los motores de JavaScript no cuentan con el lujo (como otros compiladores de lenguajes) de tener mucho tiempo para optimizar, porque la compilación de JavaScript no sucede en un paso de construcción, como en otros lenguajes.

Para JavaScript, la compilación ocurre, en muchos casos, pocos microsegundos(o menos!) antes de que el código se ejecute. Para asegurarse de un rendimiento más veloz, los motores de JS usan todo tipo de trucos (como JITs, con compilación perezosa e incluso recompilación en caliente, etc.) que están bastante fuera del "alcance" de nuestra discusión.

Digamos, por el bien de la simplicidad, que cualquier trozo de JavaScript que es compilado (usualmente *justo* antes!) es ejecutado. Entonces, el compilador JS va a tomar el programa `var a = 2;` lo compila *primero*, y luego está preparado para ejecutarlo, usualmente justo al siguiente instante.

## Entendiendo el Scope

La manera en la que abordaremos el aprendizaje sobre el *scope* será pensando sus procesos en términos de una conversación. Pero, ¿*Quién* está teniendo esta conversación?

### The Cast

Let's meet the cast of characters that interact to process the program `var a = 2;`, so we understand their conversations that we'll listen in on shortly:

1. *Engine*: responsible for start-to-finish compilation and execution of our JavaScript program.

2. *Compiler*: one of *Engine*'s friends; handles all the dirty work of parsing and code-generation (see previous section).

3. *Scope*: another friend of *Engine*; collects and maintains a look-up list of all the declared identifiers (variables), and enforces a strict set of rules as to how these are accessible to currently executing code.

For you to *fully understand* how JavaScript works, you need to begin to *think* like *Engine* (and friends) think, ask the questions they ask, and answer those questions the same.

### Back & Forth

When you see the program `var a = 2;`, you most likely think of that as one statement. But that's not how our new friend *Engine* sees it. In fact, *Engine* sees two distinct statements, one which *Compiler* will handle during compilation, and one which *Engine* will handle during execution.

So, let's break down how *Engine* and friends will approach the program `var a = 2;`.

The first thing *Compiler* will do with this program is perform lexing to break it down into tokens, which it will then parse into a tree. But when *Compiler* gets to code-generation, it will treat this program somewhat differently than perhaps assumed.

A reasonable assumption would be that *Compiler* will produce code that could be summed up by this pseudo-code: "Allocate memory for a variable, label it `a`, then stick the value `2` into that variable." Unfortunately, that's not quite accurate.

*Compiler* will instead proceed as:

1. Encountering `var a`, *Compiler* asks *Scope* to see if a variable `a` already exists for that particular scope collection. If so, *Compiler* ignores this declaration and moves on. Otherwise, *Compiler* asks *Scope* to declare a new variable called `a` for that scope collection.

2. *Compiler* then produces code for *Engine* to later execute, to handle the `a = 2` assignment. The code *Engine* runs will first ask *Scope* if there is a variable called `a` accessible in the current scope collection. If so, *Engine* uses that variable. If not, *Engine* looks *elsewhere* (see nested *Scope* section below).

If *Engine* eventually finds a variable, it assigns the value `2` to it. If not, *Engine* will raise its hand and yell out an error!

To summarize: two distinct actions are taken for a variable assignment: First, *Compiler* declares a variable (if not previously declared in the current scope), and second, when executing, *Engine* looks up the variable in *Scope* and assigns to it, if found.

### Compiler Speak

We need a little bit more compiler terminology to proceed further with understanding.

When *Engine* executes the code that *Compiler* produced for step (2), it has to look-up the variable `a` to see if it has been declared, and this look-up is consulting *Scope*. But the type of look-up *Engine* performs affects the outcome of the look-up.

In our case, it is said that *Engine* would be performing an "LHS" look-up for the variable `a`. The other type of look-up is called "RHS".

I bet you can guess what the "L" and "R" mean. These terms stand for "Left-hand Side" and "Right-hand Side".

Side... of what? **Of an assignment operation.**

In other words, an LHS look-up is done when a variable appears on the left-hand side of an assignment operation, and an RHS look-up is done when a variable appears on the right-hand side of an assignment operation.

Actually, let's be a little more precise. An RHS look-up is indistinguishable, for our purposes, from simply a look-up of the value of some variable, whereas the LHS look-up is trying to find the variable container itself, so that it can assign. In this way, RHS doesn't *really* mean "right-hand side of an assignment" per se, it just, more accurately, means "not left-hand side".

Being slightly glib for a moment, you could also think "RHS" instead means "retrieve his/her source (value)", implying that RHS means "go get the value of...".

Let's dig into that deeper.

When I say:

```js
console.log( a );
```

The reference to `a` is an RHS reference, because nothing is being assigned to `a` here. Instead, we're looking-up to retrieve the value of `a`, so that the value can be passed to `console.log(..)`.

By contrast:

```js
a = 2;
```

The reference to `a` here is an LHS reference, because we don't actually care what the current value is, we simply want to find the variable as a target for the `= 2` assignment operation.

**Note:** LHS and RHS meaning "left/right-hand side of an assignment" doesn't necessarily literally mean "left/right side of the `=` assignment operator". There are several other ways that assignments happen, and so it's better to conceptually think about it as: "who's the target of the assignment (LHS)" and "who's the source of the assignment (RHS)".

Consider this program, which has both LHS and RHS references:

```js
function foo(a) {
	console.log( a ); // 2
}

foo( 2 );
```

The last line that invokes `foo(..)` as a function call requires an RHS reference to `foo`, meaning, "go look-up the value of `foo`, and give it to me." Moreover, `(..)` means the value of `foo` should be executed, so it'd better actually be a function!

There's a subtle but important assignment here. **Did you spot it?**

You may have missed the implied `a = 2` in this code snippet. It happens when the value `2` is passed as an argument to the `foo(..)` function, in which case the `2` value is **assigned** to the parameter `a`. To (implicitly) assign to parameter `a`, an LHS look-up is performed.

There's also an RHS reference for the value of `a`, and that resulting value is passed to `console.log(..)`. `console.log(..)` needs a reference to execute. It's an RHS look-up for the `console` object, then a property-resolution occurs to see if it has a method called `log`.

Finally, we can conceptualize that there's an LHS/RHS exchange of passing the value `2` (by way of variable `a`'s RHS look-up) into `log(..)`. Inside of the native implementation of `log(..)`, we can assume it has parameters, the first of which (perhaps called `arg1`) has an LHS reference look-up, before assigning `2` to it.

**Note:** You might be tempted to conceptualize the function declaration `function foo(a) {...` as a normal variable declaration and assignment, such as `var foo` and `foo = function(a){...`. In so doing, it would be tempting to think of this function declaration as involving an LHS look-up.

However, the subtle but important difference is that *Compiler* handles both the declaration and the value definition during code-generation, such that when *Engine* is executing code, there's no processing necessary to "assign" a function value to `foo`. Thus, it's not really appropriate to think of a function declaration as an LHS look-up assignment in the way we're discussing them here.

### Engine/Scope Conversation

```js
function foo(a) {
	console.log( a ); // 2
}

foo( 2 );
```

Let's imagine the above exchange (which processes this code snippet) as a conversation. The conversation would go a little something like this:

> ***Engine***: Hey *Scope*, I have an RHS reference for `foo`. Ever heard of it?

> ***Scope***: Why yes, I have. *Compiler* declared it just a second ago. He's a function. Here you go.

> ***Engine***: Great, thanks! OK, I'm executing `foo`.

> ***Engine***: Hey, *Scope*, I've got an LHS reference for `a`, ever heard of it?

> ***Scope***: Why yes, I have. *Compiler* declared it as a formal parameter to `foo` just recently. Here you go.

> ***Engine***: Helpful as always, *Scope*. Thanks again. Now, time to assign `2` to `a`.

> ***Engine***: Hey, *Scope*, sorry to bother you again. I need an RHS look-up for `console`. Ever heard of it?

> ***Scope***: No problem, *Engine*, this is what I do all day. Yes, I've got `console`. He's built-in. Here ya go.

> ***Engine***: Perfect. Looking up `log(..)`. OK, great, it's a function.

> ***Engine***: Yo, *Scope*. Can you help me out with an RHS reference to `a`. I think I remember it, but just want to double-check.

> ***Scope***: You're right, *Engine*. Same guy, hasn't changed. Here ya go.

> ***Engine***: Cool. Passing the value of `a`, which is `2`, into `log(..)`.

> ...

### Quiz

Check your understanding so far. Make sure to play the part of *Engine* and have a "conversation" with the *Scope*:

```js
function foo(a) {
	var b = a;
	return a + b;
}

var c = foo( 2 );
```

1. Identify all the LHS look-ups (there are 3!).

2. Identify all the RHS look-ups (there are 4!).

**Note:** See the chapter review for the quiz answers!

## Nested Scope

We said that *Scope* is a set of rules for looking up variables by their identifier name. There's usually more than one *Scope* to consider, however.

Just as a block or function is nested inside another block or function, scopes are nested inside other scopes. So, if a variable cannot be found in the immediate scope, *Engine* consults the next outer containing scope, continuing until found or until the outermost (aka, global) scope has been reached.

Consider:

```js
function foo(a) {
	console.log( a + b );
}

var b = 2;

foo( 2 ); // 4
```

The RHS reference for `b` cannot be resolved inside the function `foo`, but it can be resolved in the *Scope* surrounding it (in this case, the global).

So, revisiting the conversations between *Engine* and *Scope*, we'd overhear:

> ***Engine***: "Hey, *Scope* of `foo`, ever heard of `b`? Got an RHS reference for it."

> ***Scope***: "Nope, never heard of it. Go fish."

> ***Engine***: "Hey, *Scope* outside of `foo`, oh you're the global *Scope*, ok cool. Ever heard of `b`? Got an RHS reference for it."

> ***Scope***: "Yep, sure have. Here ya go."

The simple rules for traversing nested *Scope*: *Engine* starts at the currently executing *Scope*, looks for the variable there, then if not found, keeps going up one level, and so on. If the outermost global scope is reached, the search stops, whether it finds the variable or not.

### Building on Metaphors

To visualize the process of nested *Scope* resolution, I want you to think of this tall building.

<img src="fig1.png" width="250">

The building represents our program's nested *Scope* rule set. The first floor of the building represents your currently executing *Scope*, wherever you are. The top level of the building is the global *Scope*.

You resolve LHS and RHS references by looking on your current floor, and if you don't find it, taking the elevator to the next floor, looking there, then the next, and so on. Once you get to the top floor (the global *Scope*), you either find what you're looking for, or you don't. But you have to stop regardless.

## Errors

Why does it matter whether we call it LHS or RHS?

Because these two types of look-ups behave differently in the circumstance where the variable has not yet been declared (is not found in any consulted *Scope*).

Consider:

```js
function foo(a) {
	console.log( a + b );
	b = a;
}

foo( 2 );
```

When the RHS look-up occurs for `b` the first time, it will not be found. This is said to be an "undeclared" variable, because it is not found in the scope.

If an RHS look-up fails to ever find a variable, anywhere in the nested *Scope*s, this results in a `ReferenceError` being thrown by the *Engine*. It's important to note that the error is of the type `ReferenceError`.

By contrast, if the *Engine* is performing an LHS look-up and arrives at the top floor (global *Scope*) without finding it, and if the program is not running in "Strict Mode" [^note-strictmode], then the global *Scope* will create a new variable of that name **in the global scope**, and hand it back to *Engine*.

*"No, there wasn't one before, but I was helpful and created one for you."*

"Strict Mode" [^note-strictmode], which was added in ES5, has a number of different behaviors from normal/relaxed/lazy mode. One such behavior is that it disallows the automatic/implicit global variable creation. In that case, there would be no global *Scope*'d variable to hand back from an LHS look-up, and *Engine* would throw a `ReferenceError` similarly to the RHS case.

Now, if a variable is found for an RHS look-up, but you try to do something with its value that is impossible, such as trying to execute-as-function a non-function value, or reference a property on a `null` or `undefined` value, then *Engine* throws a different kind of error, called a `TypeError`.

`ReferenceError` is *Scope* resolution-failure related, whereas `TypeError` implies that *Scope* resolution was successful, but that there was an illegal/impossible action attempted against the result.

## Review (TL;DR)

Scope is the set of rules that determines where and how a variable (identifier) can be looked-up. This look-up may be for the purposes of assigning to the variable, which is an LHS (left-hand-side) reference, or it may be for the purposes of retrieving its value, which is an RHS (right-hand-side) reference.

LHS references result from assignment operations. *Scope*-related assignments can occur either with the `=` operator or by passing arguments to (assign to) function parameters.

The JavaScript *Engine* first compiles code before it executes, and in so doing, it splits up statements like `var a = 2;` into two separate steps:

1. First, `var a` to declare it in that *Scope*. This is performed at the beginning, before code execution.

2. Later, `a = 2` to look up the variable (LHS reference) and assign to it if found.

Both LHS and RHS reference look-ups start at the currently executing *Scope*, and if need be (that is, they don't find what they're looking for there), they work their way up the nested *Scope*, one scope (floor) at a time, looking for the identifier, until they get to the global (top floor) and stop, and either find it, or don't.

Unfulfilled RHS references result in `ReferenceError`s being thrown. Unfulfilled LHS references result in an automatic, implicitly-created global of that name (if not in "Strict Mode" [^note-strictmode]), or a `ReferenceError` (if in "Strict Mode" [^note-strictmode]).

### Quiz Answers

```js
function foo(a) {
	var b = a;
	return a + b;
}

var c = foo( 2 );
```

1. Identify all the LHS look-ups (there are 3!).

	**`c = ..`, `a = 2` (implicit param assignment) and `b = ..`**

2. Identify all the RHS look-ups (there are 4!).

    **`foo(2..`, `= a;`, `a + ..` and `.. + b`**


[^note-strictmode]: MDN: [Strict Mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions_and_function_scope/Strict_mode)
