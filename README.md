# ECMAScript a fondo — De ES6 a la versión más reciente (en progreso)

Bienvenido al repositorio donde estaremos documentando, explicando y desmenuzando **cada versión de ECMAScript** desde **ES6 (2015)** hasta la **versión más actual** disponible.

Nuestro objetivo es crear una serie de guías claras, técnicas y progresivas que sirvan tanto como referencia para desarrolladores intermedios/avanzados, como material de consulta para equipos, cursos o mentoring técnico.

---

## i. Introducción

En varias stories y publicaciones he mencionado conceptos como **ES5**, **ECMA 5**, **ES6**, **ECMA 6**, **ECMAScript 2015**… pero aún no he explicado qué son exactamente.

Antes de entrar al detalle sobre qué nuevas funcionalidades trajo cada versión, necesitamos conocer **cómo nació JavaScript** y por qué existe el estándar ECMAScript.

---

## ii. Breve historia de JavaScript

Al principio la web era puramente estática: solo HTML con texto, imágenes y enlaces. Dos navegadores dominaban la escena: **Internet Explorer** (Microsoft) y **Netscape Navigator**. Ambos querían liderar la innovación web.

En este contexto, en **1995**, dentro de Netscape, **Brendan Eich** creó un lenguaje de scripting llamado **Mocha**, luego renombrado a **LiveScript**, y finalmente a **JavaScript** (para aprovechar el boom de Java).

Pese a la confusión inicial (JavaScript no es Java), el lenguaje creció rápidamente. Microsoft creó una copia llamada **JScript**, y para evitar fragmentación, Netscape envió el lenguaje a estandarizar al organismo **ECMA (European Computer Manufacturers Association)**.

Así nació el estándar **ECMAScript**, que busca definir un lenguaje **multiplataforma, independiente y compatible entre navegadores**.

---

## iii. ECMAScript (ES1, ES2, ES3… ES.Next)

El comité **TC39** de ECMA es el encargado de definir nuevas versiones del estándar. A partir de **ES6 (2015)** se adopta la convención de nombrar las ediciones por año:

| Versión | Año  | Nombre alternativo | Estado                |
| ------- | ---- | ------------------ | --------------------- |
| ES1     | 1997 | ECMAScript 1       | Primera edición       |
| ES2     | 1998 | ECMAScript 2       | ISO compliance        |
| ES3     | 1999 | ECMAScript 3       | Base de navegadores   |
| ES4     | ✘    | Cancelada          | Conflictos políticos  |
| ES5     | 2009 | ECMAScript 5       | Aún ampliamente usado |
| ES6     | 2015 | ECMAScript 2015    | Punto de inflexión    |
| ES7     | 2016 | ECMAScript 2016    | Incremental           |
| ES8     | 2017 | ECMAScript 2017    | Async/await           |
| ES9     | 2018 | ECMAScript 2018    | Regex avanzado        |
| ES10    | 2019 | ECMAScript 2019    | Flattening, trimStart |
| ...     | ...  | ...                | ...                   |

---

## Documentación por versión

Aquí encontrarás los enlaces a las guías técnicas, estructuradas en partes por claridad y profundidad. Iremos subiendo cada una de forma progresiva.

| Versión                                                                              | ECMAScript      | Parte    | Contenido principal                                                                                                                                                                                 |
| ------------------------------------------------------------------------------------ | --------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [**ES8**](https://github.com/mauriciogc/ECMAScript/blob/main/readme/ECMA2017-pI.md)  | ECMAScript 2017 | Parte I  | <ul><li>`Object.values`</li><li>`Object.entries`</li><li>`Object.getOwnPropertyDescriptors`</li></ul>                                                                                               |
| [**ES9**](https://github.com/mauriciogc/ECMAScript/blob/main/readme/ECMA2018-pI.md)  | ECMAScript 2018 | Parte I  | <ul><li>Rest y Spread Properties para Objetos</li><li> Asynchronous Iteration (`for await...of`)</li><li> `Promise.prototype.finally`</li></ul>                                                     |
| [**ES9**](https://github.com/mauriciogc/ECMAScript/blob/main/readme/ECMA2018-pII.md) | ECMAScript 2018 | Parte II | <ul><li>RegExp improvements<ul><li>`s` (dotAll)</li><li>Named Capturing Groups</li><li>Lookbehind Assertions</li><li>Unicode Property</li><li>Escapes</li></ul></li><li>Template Literals</li></ul> |
| En proceso...                                                                        | ...             | ...      | ...                                                                                                                                                                                                 |

---

## ¿Qué encontrarás en cada guía?

Cada entrada contiene:

- Definición técnica y contexto

- Sintaxis y funcionamiento
- Ventajas reales
- Ejemplos: básico, intermedio, avanzado
- Buenas prácticas y consideraciones
- Bonus: comparativas, casos de uso, mitos, soporte

---

## Contribuciones

¿Quieres colaborar, proponer mejoras o ampliar ejemplos?  
**¡Bienvenido! Abre un issue o haz un pull request.**

---

Creado con por [Mauricio García](https://medium.com/@mauriciogc)
