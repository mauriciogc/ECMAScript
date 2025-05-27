# JavaScript — ES9 (ECMAScript 2018): Parte II

![](https://cdn-images-1.medium.com/max/1600/1*8h6ofVulCeWv-oh5PKrHqQ.png)

## i. Introducción

Vamos a ver la segunda parte de **novedades** que nos trae la edición **ES9** (_ES2018_) de _JavaScript_.

> Ten en cuenta que **no todos los navegadores modernos admiten de forma nativa todas las funcionalidades “nuevas” de JavaScript**.

> Antes de utilizar alguna de ellas en entornos productivos, **verifica la compatibilidad en los navegadores objetivo**.

> Si estás utilizando un **transpilador como Babel** o algún sistema de build moderno (por ejemplo, Vite, Webpack con configuración adecuada o Next.js con configuración predeterminada), **no deberías preocuparte**, ya que estas herramientas se encargan de transpilar el código a versiones compatibles con navegadores más antiguos.

> Aunque la mayoría de estas funcionalidades ya cuentan con un **amplio soporte en los navegadores actuales**, siempre es recomendable hacer una validación explícita antes de lanzar a producción.

---

## ii. **_RegExp improvements_**

### 01. `s` (dotAll)

Por defecto, en JavaScript el carácter `.` (punto) en una RegExp **no coincide con saltos de línea** (`\n`, `\r`, `\u2028`, `\u2029`). Con **ES2018**, se introduce el flag `s` (dotAll), que cambia ese comportamiento y permite que `.` **coincida literalmente con cualquier carácter**, incluyendo líneas nuevas.

#### Principales características — 

- Permite usar `.` como cualquier carácter.
- Se habilita con `/exp/s`.

#### Ventajas — 

- Mayor claridad al hacer coincidir bloques multilínea.

- Evita clases complejas como `[\s\S]` o hacks como `[^]`.
- El `.` se comporta como en la mayoría de lenguajes de RegExp

#### ¿Dónde se usa? — 

- **Extracción de bloques de texto multilínea**: Capturar contenido que abarca varias línea.

- **Parsers, extractores o scrapers**: Plantillas de emails, páginas web, o archivos de configuración con bloques anidados entre delimitadores, donde puede haber saltos de línea.
- **Validación y análisis de cadenas con formato libre**: Mensajes largos en JSON, Markdown, o documentos generados por usuarios.

#### Sintaxis — 

El flag `s` se añade al final de una expresión regular como cualquier otro modificador (`g`, `i`, `m`, etc.).

const regex = /expresión/s;

- Por **defecto**, el metacarácter `.` (punto) en una RegExp **coincide con cualquier carácter excepto saltos de línea** (`\n`, `\r`, `\u2028`, `\u2029`).

- Al activar `s`, el punto **también coincide con saltos de línea**, es decir, **con cualquier carácter sin excepción**.

#### Ejemplo 1  — Comparando dos cadenas

```js
const str = 'línea1\nlínea2';

console.log(/línea1.línea2/.test(str)); // false
console.log(/línea1.línea2/s.test(str)); // true
```

Sin el flag `s`, el `.` falla porque no incluye el salto de línea. Al añadir `s`, se comporta como "coincide con _cualquier carácter real_, incluyendo `\n`".

#### Ejemplo 2 — Extraer contenido entre etiquetas

```js
const html = `  
  <section>  
    Línea uno  
    Línea dos  
  </section>  
`;

const regexWithoutS = /<section>(.*)<\/section>/;
console.log(regexWithoutS.test(html)); // false

const regexWithS = /<section>(.*)<\/section>/s; // Con dotAll
const res = html.match(regexWithS);

console.log(res[1]);
// Salida:
// "\n    Línea uno\n    Línea dos\n"
```

Sin el flag `s`, el punto `.` no atraviesa el salto de línea, y por eso no encuentra nada. Con `s`, puedes capturar correctamente bloques multilínea.

Si lo combinas con `?` (lazy matching), puedes evitar capturar de más:

```js
const html = `  
  <section>  
    Línea uno  
    Línea dos  
  </section>  
  <section>  
    Línea tres  
    Línea cuatro  
  </section>  
`;

const regexWithS = /<section>(.*?)<\/section>/s; // Con dotAll y lazy matching
const res = html.match(regexWithS);

console.log(res[1]);
// Salida:
// "\n    Línea uno\n    Línea dos\n"
```

> **Útil cuando:** Si tienes múltiples secciones en el mismo HTML y no quieres que el patrón capture hasta el último `</section>`.

#### Soporte — 

- `dotAll` [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/dotAll)] [[ref](https://caniuse.com/mdn-javascript_builtins_regexp_dotall)]

#### A considerar — 

- Úsalo cuando sabes que tu contenido puede incluir saltos de línea.

- Si no lo necesitas, omítelo para evitar capturas inesperadas.

---

### 02. Named Capturing Groups

Antes de ES2018, las expresiones regulares capturaban grupos mediante índices (`match[1]`, `match[2]`, etc.). Ahora, puedes usar **nombres** para los grupos, accediendo a ellos por clave (`match.groups.nombre`).

#### Principales características — 

- Mejora legibilidad y mantenimiento.

- Compatible con `match`, `exec`, destructuración.

#### Ventajas — 

- Evita depender de índices que pueden cambiar.

- Clarifica el propósito de cada grupo.

#### ¿Dónde se usa? — 

- Cuando las expresiones tienen muchos grupos y los índices son difíciles de seguir.

- En procesamiento de fechas, nombres, estructuras repetitivas o etiquetas.
- Cuando se combinan varias RegExp con lógica condicional.

#### Sintaxis — 

```js
const date = '2025-05-26';
const regex = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;

const { groups } = regex.exec(date);
console.log(groups); // { year: '2025', month: '05', day: '36' }
```

- `(?<nombre>...)` define un grupo con nombre.

- El resultado incluye una propiedad `groups`.

#### ¿Cómo funciona internamente? — 

El motor asocia una propiedad `groups` al resultado del `match`, donde cada key corresponde a su grupo nombrado.

#### Ejemplo 1 — Parseo de logs o registros de servidor

Imagina que tienes líneas de log en el siguiente formato:

```yaml
[2025-05-26 12:34:56] INFO  User: Mauricio Action: Login Status: Success
```

Donde se requieren extraer: fecha, hora, tipo de log (INFO, ERROR, etc), usuario, acción y estado.

```js
const logLine = `[2025-05-26 12:34:56] INFO  User: Mauricio Action: Login Status: Success`;
const logRegex =
  /\[(?<date>\d{4}-\d{2}-\d{2}) (?<time>\d{2}:\d{2}:\d{2})\]\s+(?<level>INFO|ERROR|WARN)\s+User:\s+(?<user>\w+)\s+Action:\s+(?<action>\w+)\s+Status:\s+(?<status>\w+)/;
const result = logRegex.exec(logLine);

if (result?.groups) {
  const { date, time, level, user, action, status } = result.groups;
  console.log(`  
    Date: ${date}  
    Time: ${time}  
    Level: ${level}  
    User: ${user}  
    Action: ${action}  
    Status: ${status}  
  `);
}
```

![](https://cdn-images-1.medium.com/max/1600/1*titzeRPzdyqG5sDpT8ay5w.png)

#### Soporte — 

- Named Capturing Groups [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Regular_expressions/Named_capturing_group)][[ref](https://caniuse.com/mdn-javascript_regular_expressions_named_capturing_group)]

#### A considerar — 

- Usa nombres semánticos (`name`, `year`, `domain`).

- No combines con muchos grupos anónimos en la misma expresión.

---

### 03. Lookbehind Assertions

Los **lookbehind assertions** permiten validar si una posición en la cadena **está precedida o no por un patrón**. Son el “hermano inverso” de los _lookahead_ (`(?=...)` / `(?!...)`).

#### Principales características — 

- Se evalúan antes de una coincidencia.

- No capturan el valor del lookbehind.

#### Ventajas — 

- Más precisión en validaciones de contexto.

- Evita reescrituras complejas o recursivas.

#### Sintaxis — 

- **Positive Lookbehind:** `(?<=...)`

- **Negative Lookbehind:** `(?<!...)`

#### Ejemplo 1 — Extraer el precio sin el símbolo `$`

```js
// Extraer solo el número, pero solo si está precedido por $.
const texto = 'Precio base: $199.99, oferta: €149.99, descuento: $99.50';
const preciosUSD = [...texto.matchAll(/(?<=\$)\d+(\.\d{2})?/g)];

console.log(preciosUSD.map((match) => match[0]));
// ["199.99", "99.50"]
```

#### Ejemplo 2 — Limpiar prefijos de tracking `#`

```js
// Remover el prefijo "TRACK" si va precedido por un #, pero no cuando aparece solo.
const tags = ['#TRACK123', 'ORDER456', '#USER789', 'TRACK012'];
const limpio = tags.map((tag) => tag.replace(/(?<=#)TRACK/, ''));

console.log(limpio);
// ["#123", "ORDER456", "#USER789", "TRACK012"]
```

#### Ejemplo 3 — Filtrar correos que no estén dentro de la lista

```js
// Filtrar de un array solo los correos que NO estén dentro de la lista
const emails = [
  'angelica@example.com',
  'mauricio@example.com',
  'peter@example.com',
  'admin@example.com',
];
const notList = emails.filter((email) =>
  /(?<!mauricio|admin)@example\.com$/.test(email)
);

console.log(notList);
// ['angelica@example.com', 'peter@example.com']
```

#### Ejemplo 4 — Detectar hashtags solo si no están dentro de un enlace

```js
// Capturar los hashtags que no estén dentro de una URL
const txt = 'Sigue #JavaScript en https://example.com/#JS y únete a #NextJS';
const hashtagsOutsideURL = [...txt.matchAll(/(?<!https:\/\/\S*)#\w+/g)];

console.log(hashtagsOutsideURL.map((match) => match[0]));
// ["#JavaScript", "#NextJS"]
```

#### Ejemplo 5 — Lookbehind + lookahead

```js
// Separar comandos si están precedidos por @ (ej. un chat)
const commands = `  
@ban user1  
@kick user2  
normal text  
@mute user3  
`.match(/(?<=@)\w+(?= )/g);

// Extrae solo comandos reales
console.log(commands); // ["ban", "kick", "mute"]
```

**Lookbehind + lookahead** te permite capturar solo la palabra que está después de `@` pero antes de un espacio.

#### Soporte — 

- Lookbehind Assertions [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Regular_expressions/Lookbehind_assertion)] [[ref](https://caniuse.com/js-regexp-lookbehind)]

#### A considerar — 

- Requiere motores modernos (no soportado en algunos entornos antiguos).

- Úsalo solo cuando no puedes resolver con un lookahead o captura previa.
- `lookbehind` está disponible en la mayoría de navegadores modernos (Chrome 64+, Firefox 78+), pero no en versiones antiguas de Safari o Edge Legacy.
- Se debe evitar su abuso en RegExp muy largas o anidadas, ya que puede impactar rendimiento.
- Siempre que puedas, **prefiere la claridad sobre la compactación extrema**.

---

### 04. Unicode Property Escapes

Permite usar `\p{…}` y `\P{…}` para coincidir caracteres según **propiedades Unicode**: como letras, puntuación, emoji, etc (requiere el flag `u`).

#### Principales características — 

- Basado en el estándar Unicode (General_Category, Script, etc).

- Mayor precisión para validaciones internacionales.

#### Ventajas — 

- Puedes validar letras, emojis o scripts específicos sin rangos manuales.

- Funciona con todos los caracteres Unicode, no solo ASCII.
- Elimina necesidad de expresiones como `[a-zA-Z0-9]` que fallan con otros alfabetos.
- Puedes limitar tipos de entrada (ej: evitar símbolos, controlar números ocultos en otros idiomas)
- Regex más autoexplicativas.

#### ¿Dónde se usa? — 

- En formularios con soporte multilingüe (validar si un string contiene solo letras de cierto alfabeto).

- Para detectar o filtrar **emojis** en mensajes o chats.
- Para sanitizar entradas basadas en tipos de caracteres (solo números, solo puntuación, etc.).
- En motores de búsqueda, parsers, procesadores de texto o editores que trabajan con múltiples idiomas.

#### Sintaxis — 

- `\p{PropertyName=PropertyValue}/u`: coincide caracteres que cumplen con una propiedad Unicode.

- `\p{PropertyValue}/u`: una forma abreviada si no se necesita especificar la propiedad.
- `\P{...}/u`: negación, coincide caracteres que **no** cumplen con la propiedad.

Propiedades comunes:

```css
/\p{Letter}/u         // Letras (mayúsculas, minúsculas, acentuadas, etc.)
/\p{Script=Latin}/u   // Letras del alfabeto latino
/\p{Script=Greek}/u   // Letras griegas
/\p{Emoji}/u          // Emojis
/\p{Number}/u         // Dígitos (0-9, números arábigos, etc.)
/\P{Number}/u         // Todo excepto números
```

> Esta sintaxis eleva el poder expresivo de las RegExp al nivel de procesamiento internacional y de texto complejo.

#### Ejemplo 1 — `\p{Emoji}` Emojis Unicode

```js
const regex = /\p{Emoji}/u;

console.log(regex.test('Hola soy un 🤖')); // true
console.log(regex.test('Hola soy un robot')); // false
console.log(regex.test('❤️')); // true
```

#### Ejemplo 2— `\p{Letter}` Letras de cualquier alfabeto

```js
const regex = /\p{Letter}+/gu;

console.log(regex.test('Hola')); // true — letras latinas
console.log(regex.test('über')); // true — letras con acento
console.log(regex.test('λambda')); // true — incluye letra griega
console.log(regex.test('123')); // false — no hay letras
```

#### Ejemplo 3— `\p{Script=Latin}` Solo letras del alfabeto latino

```js
const regex = /\p{Script=Latin}+/gu;

console.log(regex.test('café')); // true
console.log(regex.test('Zürich')); // true — incluye diéresis (aún es Latin)
console.log(regex.test('Москва')); // false — cirílico (ruso)
console.log(regex.test('λambda')); // false — contiene letra griega
```

#### Ejemplo 4— `\p{Number}` Caracteres numéricos (cualquier sistema)

```js
const regex = /\p{Number}/gu;

console.log(regex.test('3')); // true — número arábigo
console.log(regex.test('Ⅻ')); // true — número romano (Unicode)
console.log(regex.test('XII')); // false - texto, no número
console.log(regex.test('٧')); // true — número arábigo oriental
console.log(regex.test('one')); // false — texto, no número
```

#### Ejemplo 5— `\P{Number}` Todo excepto números

```js
const regex = /\P{Number}+/gu;

console.log(regex.test('Hello')); // true — solo letras
console.log(regex.test('!@#')); // true — símbolos, no números
console.log(regex.test('123')); // false — son números
console.log(regex.test('🚀abc')); // true — sin números
```

#### Ejemplo 6 — Reemplazar emojis por texto accesible

```js
// Mapeo básico emoji → texto alternativo
const emojiToAltText = {
  '🚀': '[rocket]',
  '😂': '[laugh]',
  '❤️': '[heart]',
  '👍': '[thumbs up]',
  '🔥': '[fire]',
};

// Regex para detectar todos los emojis en el texto
const emojiRegex = /\p{Emoji_Presentation}/gu;

/**
 * Sustituye los emojis de un mensaje por etiquetas de texto legible.
 */
function replaceEmojisWithAltText(message) {
  return message.replace(
    emojiRegex,
    (emoji) => emojiToAltText[emoji] || '[emoji]'
  );
}

// Ejemplo
const input =
  '¡Hola! 😂 Esto es "Unicode Property Escapes" con JS 🚀. Like it? 👍';
const output = replaceEmojisWithAltText(input);

console.log(output);
// ¡Hola! [laugh] Esto es "Unicode Property Escapes" con JS [rocket]. Like it? [thumbs up]
```

- Usamos `\p{Emoji_Presentation}` para enfocarnos solo en los emojis que tienen presentación visual directa (evitamos símbolos ambiguos).

- Creamos un diccionario para mapear algunos emojis conocidos a texto alternativo.
- Usamos `.replace()` para recorrer cada coincidencia y sustituir.

#### Soporte — 

- `\p{...}, \P{...}` [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Regular_expressions/Unicode_character_class_escape)] [[ref](https://caniuse.com/mdn-javascript_regular_expressions_unicode_character_class_escape)]

#### A considerar — 

- Siempre usa la bandera `u`.

- Útil para inputs multilingües o validaciones de caracteres específicos.

---

## iii. **_Template Literals_**

Desde **ECMAScript 2015 (ES6)**, JavaScript introdujo los **template literals [**[**ref**](https://mauriciogc.medium.com/javascript-es6-parte-i-c7a0dfc1f8a5)**]** como una forma más expresiva de construir cadenas multilínea e interpoladas y los **tagged templates**, que permiten interceptar la interpolación y manipular el contenido antes de construir el string final:

```js
// template literals
const name = 'Mauricio';
const say = `Hola, ${name}!`;
console.log(say); // "Hola Mauricio"

// tagged templates
function label(strings, ...values) {
  return strings[0] + values[0].toUpperCase();
}

label`Hola, ${name}`; // "Hola, MAURICIO"
```

**Antes de ES2018**, los tagged templates **ya funcionaban** en la mayoría de los entornos modernos, pero su comportamiento en **caracteres escapados**, **unicode** y **raw strings** no era **consistente entre navegadores/motores**, y **no estaba totalmente especificado** en la ECMAScript Spec.

#### ¿Qué se soluciona en ES2018? — 

ES2018 **formalizó** y **normalizó** el comportamiento de los tagged templates:

- **Especificación completa y clara**: se definió exactamente cómo se procesan los literales, interpolaciones, escapes y `.raw`.

- **Manejo consistente de escapes**: `strings.raw` **siempre** representa el contenido original, sin procesar.
- **Mayor precisión para librerías como sanitize-html, SQL builders, i18n**: Evita problemas de doble escape y permite validar entrada y salida de templates embebidos.

#### Ventajas —

- Útil para filtrar datos antes de inyectarlos en HTML o queries.

- `.raw` permite obtener texto con escapes intactos.

#### ¿Dónde se usa? — 

- Plantillas HTML o SQL sanitizadas (evitar inyecciones).

- DSLs embebidos dentro de JavaScript.
- Sistemas de traducción (`i18n`) que necesitan interpolar contenido sin perder formato.

#### ¿Cómo funciona internamente? — 

Cuando se usa una tagged template:

```js
tag`Hola, ${name}! tienes ${messages} mensajes.`;
```

El motor invoca:

```js
tag(['Hola, ', '! tienes ', ' mensajes.'], name, messages);
```

- El primer argumento es un **array de strings literales** (`strings`).

- Los siguientes argumentos son los **valores interpolados** (`...values`).
- Además, `strings.raw` te permite acceder a los **escapes literales reales** (útil para evitar interpretación).

#### Ejemplo 1 — Sanitización de HTML (anti-XSS)

```js
function HTMLEscaping(strings, ...values) {
  return strings.reduce((acc, str, i) => {
    const val = String(values[i] ?? '').replace(
      /[&<>"']/g,
      (s) =>
        ({
          '&': '&amp;',
          '<': '&lt;',
          '>': '&gt;',
          '"': '&quot;',
          "'": '&#39;',
        }[s])
    );
    return acc + str + val;
  }, '');
}

const user = '<script>alert("Codigo malicioso")</script>';
const message = HTMLEscaping`Bienvenido, ${user}!`;
console.log(message);
// Bienvenido, &lt;script&gt;alert(&quot;Codigo malicioso&quot;)&lt;/script&gt;!
```

> Este patrón **evita ataques XSS** si interpolan valores en HTML.

#### Ejemplo 2 — Traducción internacionalizada (i18n)

```js
//Simulamos la traducción
function t(strings, ...values) {
  return strings
    .reduce((txt, s, i) => txt + s + (values[i] ?? ''), '')
    .replace('Bienvenido', 'Welcome');
}

const name = 'Mauricio';
t`Bienvenido ${name}`; // "Welcome Mauricio"
```

#### Ejemplo 3 — Acceso a `.raw`

```js
function debugRaw(strings) {
  console.log('Raw:', strings.raw[0]);
  console.log('Processed:', strings[0]);
}

debugRaw`Primera línea\nSegunda línea`;
// Raw: "Primera línea\\nSegunda línea"
// Processed: "Primera línea
// Segunda línea"
```

#### Soporte — 

- Template Literals [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)] [[ref](https://caniuse.com/mdn-javascript_grammar_template_literals_template_literal_revision)]

#### A considerar — 

- Usa tagged templates cuando necesites **control sobre cómo se forma el string**.

- Evita usar `.raw` si no sabes cómo procesarlo ya que puede tener escapes inesperados.
- Combina con otras técnicas (regex, validaciones) para mayor seguridad.
