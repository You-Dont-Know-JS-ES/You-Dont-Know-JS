
[![All Contributors](https://img.shields.io/badge/all_contributors-7-orange.svg?style=flat-square)](#contributors)
# ¿Qué es You-Dont-Know-JS-ES/Traduccion ?

Es un repositorio que almacena la éxitosa serie de libros de JavaScript "You Don't Know JS" de Kyle Simpson un fork del repositorio original, con la intención de que estos sean traducidos al idioma español.

Aunque este es un repo personal, hace parte del repo de `Staging`, proceso previo para hacer merge a el repo original. Esta decisión se tomó conjuntamente con @getify para reducir el ruido en el repo master, [siga conversación acá](https://github.com/getify/You-Dont-Know-JS/pull/1378).

Sin embargo, para todas las contribuciones habrá un `Contributors` pages en Español. Siéntase libre de empezar a traducir secciones que aún no hayan sido traducidas, ya sea que conozca o no el lenguaje es un excelente ejercicio para aprender y entender detalles sobre JavaScript que quizás no se haya imaginado antes.

# ¿Cómo empezar a ser un colaborador?

1. Fork este proyecto y clonar su propio repositorio.
2. Hacer un branch nuevo `git checkout -b pr/branch-name`

> Tip: Mantega el `master` apuntando al repo original (Esto aplica para todos los repos de código libre)
> Así también se pueden hacer Pull Request desde su branches locales
>
> ```
> git remote add upstream https://github.com/You-Dont-Know-JS-ES/Traduccion.git
> git fetch upstream
> git branch --set-upstream-to=upstream/master master
> ```
>
> Esto agregará el repositorio original como "remote" y lo llama "upstream". Luego
> hará un "Fetch" del git y toda la info que esté remota, haciendo que su rama `master`
> local cuando se haga un `git pull` tome toda la información del repositorio remoto.
> Luego puede sacar todos los branches del `master` actualizado.
> De tal manera que siempre recomendamos actualizar su rama `master`.`git pull`

## Recomendaciones básicas para el proceso de aprobación de un pull request

`Por favor hacer un PR antes de comenzar una traducción` Esto es un indicador para la comunidad de que usted va a trabajar en una traducción y evitar que cuando usted termine se de cuenta de que ya alguién trabajó en eso antes.

La unidad mínima de traducción es en Markdown un bloque de `###` ó `##`.

### Sobre el nombre de su(s) branch(es):

En lo posible, recomendamos nombrar los branches con el siguiente formato: 
  `pr/título_del_libro/nombre_de_capitulo/#titulo/##subtema/###subtema` según sea el caso. 

### Sobre el nombre de su pull request: 

 Una vez se haya decidido a traducir una sección de cualquiera de los libros, por favor asignar un título a su pull request [_*Como Draft PR*_](https://github.blog/2019-02-14-introducing-draft-pull-requests/) de la siguiente forma
 
  `[InProgress]título_del_libro/nombre_de_capitulo/#titulo/##subtema/###subtema` según sea el caso. 

Si luego de realizar los cambios necesarios, considera que su traducción definitivamente se encuentra lista para revisión por favor renombrar su pull request como
  
  `[Done]título_del_libro/nombre_de_capitulo/#titulo/##subtema/###subtema` según sea el caso.

### Importante: 

La filosofía de trabajo es: traducir el archivo, y todo trabajo pendiente se deja en inglés, para que sea claro para los colaboradores lo que hace falta por traducir.

Para este proceso se debe tener en cuenta la seccion que se tradujo con los siguientes parametros:

1. Revisar si el branch ya se encuntre de manera remota para evitar multiples ramas con nombres iguales `git branch -r`. Si el branch existe y falta terminar la traducción o desea corregirlo, hacer el pull correspondiente al branch a editar `git pull origin pr/../..`
2. Si el branch no existe, al crearlo, el nombre debe seguir la siguiente estructura teniendo en cuenta los títulos que se representan con el signo numeral (#) en el .md. Con base en lo anterior, se espera lo siguiente: `pr/título_del_libro/nombre_de_capitulo/#titulo/##subtema/###subtema` según sea el caso.
3. Realizar el debido push al repositorio de su cuenta. Si ya su traducción definitivamente se encuentra lista para revisión, renombrar  el PR como `[Done]título_del_libro/nombre_de_capitulo/#titulo/##subtema/###subtema` para que el revisor quede notificado.
4. Si ya ha hecho varios commits y piensa subir su versión final, por favor hacer un rebase interactivo en su local a master y dejar un solo commit con el mismo nombre del branch. Para mayor información sobre como realizar esto, visite este [enlace](https://www.atlassian.com/git/tutorials/rewriting-history/git-rebase)

### Aclaraciones finales sobre la traducción

Esta es una traducción hecha por contribuidores por lo que su interpretacion puede variar de acuerdo con el traductor. Es importante tener en cuenta que las anecdotas o pensamientos que el autor mencione, traducirlos en primera persona y los demas textos en tercera persona. El autor se refiere constantemente al lector con la palabra "you", por lo cual debemos referirnos al lector como "usted" para mantener el nivel de formalismo del los libros originales

## Titles

NOTA: no hay ningún orden para la traducción, como la filosofía es traducir solo pequeños párrafos, puede escoger cualquiera. Abajo los links para cada libro

- ["Up & Going"](https://github.com/You-Dont-Know-JS-ES/Traduccion/tree/master/up%20%26%20going), 
- ["Scope & Closures"](https://github.com/You-Dont-Know-JS-ES/Traduccion/tree/master/scope%20%26%20closures)
- ["this & Object Prototypes"](https://github.com/You-Dont-Know-JS-ES/Traduccion/tree/master/this%20%26%20object%20prototypes)
- ["Types & Grammar"](https://github.com/You-Dont-Know-JS-ES/Traduccion/tree/master/types%20%26%20grammar)
- ["Async & Performance"](https://github.com/You-Dont-Know-JS-ES/Traduccion/tree/master/async%20%26%20performance)
- ["ES6 & Beyond"](https://github.com/You-Dont-Know-JS-ES/Traduccion/tree/master/es6%20%26%20beyond)

## Contributors


Thanks goes to these wonderful people ([emoji key](https://github.com/all-contributors/all-contributors#emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
| [<img src="https://avatars3.githubusercontent.com/u/13613037?v=4" width="100px;" alt="EstebanMarin"/><br /><sub><b>EstebanMarin</b></sub>](https://github.com/EstebanMarin)<br />[💻](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=EstebanMarin "Code") [📖](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=EstebanMarin "Documentation") [🚇](#infra-EstebanMarin "Infrastructure (Hosting, Build-Tools, etc)") [⚠️](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=EstebanMarin "Tests") | [<img src="https://avatars2.githubusercontent.com/u/7406472?v=4" width="100px;" alt="Miguel Ballén"/><br /><sub><b>Miguel Ballén</b></sub>](https://github.com/mballeng91)<br />[📖](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=mballeng91 "Documentation") | [<img src="https://avatars0.githubusercontent.com/u/1877244?v=4" width="100px;" alt="Darwing Hernández"/><br /><sub><b>Darwing Hernández</b></sub>](https://github.com/Zenfeuer)<br />[📖](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=Zenfeuer "Documentation") | [<img src="https://avatars0.githubusercontent.com/u/2787465?v=4" width="100px;" alt="Ivan Amortegui"/><br /><sub><b>Ivan Amortegui</b></sub>](https://github.com/usedtobeme)<br />[📖](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=usedtobeme "Documentation") | [<img src="https://avatars0.githubusercontent.com/u/28151395?v=4" width="100px;" alt="Polcran"/><br /><sub><b>Polcran</b></sub>](https://github.com/Polcran)<br />[📖](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=Polcran "Documentation") | [<img src="https://avatars2.githubusercontent.com/u/15127221?v=4" width="100px;" alt="Jess Casas"/><br /><sub><b>Jess Casas</b></sub>](https://github.com/jlcasasg)<br />[📖](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=jlcasasg "Documentation") | [<img src="https://avatars2.githubusercontent.com/u/1443557?v=4" width="100px;" alt="Gio"/><br /><sub><b>Gio</b></sub>](http://www.linkedin.com/in/giovanymoreno)<br />[📖](https://github.com/You-Dont-Know-JS-ES/Traduccion/commits?author=giovanymoreno "Documentation") |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
