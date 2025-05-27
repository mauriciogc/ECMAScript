# JavaScript — ES9 (ECMAScript 2018)- Parte I

![](https://cdn-images-1.medium.com/max/1600/1*JJrDfPK0ROz3XAjnBp7ZNQ.png)

----------
#### Temario
-   **_Introducción_**
-   **_Nuevas Características_**  
	- Rest y Spread Properties para Objetos  
	- Asynchronous Iteration (for await…of)  
	- Promise.prototype.finally
----------

## i. Introducción

Vamos a ver la primera parte de **novedades** que nos trae la edición **ES9** (_ES2018_) de _JavaScript_.

> Ten en cuenta que **no todos los navegadores modernos admiten de forma nativa todas las funcionalidades “nuevas” de JavaScript**.

> Antes de utilizar alguna de ellas en entornos productivos, **verifica la compatibilidad en los navegadores objetivo**.

> Si estás utilizando un **transpilador como Babel** o algún sistema de build moderno (por ejemplo, Vite, Webpack con configuración adecuada o Next.js con configuración predeterminada), **no deberías preocuparte**, ya que estas herramientas se encargan de transpilar el código a versiones compatibles con navegadores más antiguos.

> Aunque la mayoría de estas funcionalidades ya cuentan con un **amplio soporte en los navegadores actuales**, siempre es recomendable hacer una validación explícita antes de lanzar a producción.

----------

## ii. Nuevas Características

### 01. Rest [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring#rest_in_object_destructuring)] y Spread [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_object_literals)] Properties para Objetos

Desde ECMAScript 2015 (ES6), la sintaxis `...` ya se utilizaba ampliamente en JavaScript para trabajar con **arreglos**:

-   `...spread`: para copiar o combinar arrays.

-   `...rest`: para capturar elementos restantes al desestructurar.

```js
const arr = [1, 2, 3];  
  
// Spread: copia propiedades  
const newArr = [...arr, 4];   
console.log(newArr); // [1, 2, 3, 4]  
  
// Rest: extrae las propiedades  
const [first, ...rest] = arr;  
console.log(first); // 1  
console.log(rest); // [2, 3]
```

Sin embargo, esta sintaxis **no era compatible con objetos**, lo que forzaba a los desarrolladores a usar métodos como `Object.assign()` para copiar, clonar o fusionar objetos.

ECMAScript 2018 vino a resolver esto al **extender la capacidad** de `...rest` y `...spread` a **objetos**, con una sintaxis similar pero adaptada para trabajar con claves y valores. Esto permite:

-   Extraer propiedades restantes de un objeto (`rest`).

-   Clonar o combinar objetos (`spread`).

#### Principales características —

-   Sintaxis limpia y declarativa para operaciones comunes con objetos.

-   Funciona con propiedades enumerables propias (`own enumerable`).
-   Compatible con desestructuración y funciones.

#### Ventajas —

-   Reemplaza la necesidad de `Object.assign` para copias superficiales.

-   Permite evitar mutaciones accidentales.
-   Mejora la legibilidad y expresividad del código moderno.

#### ¿Dónde se usa? —

-   Filtrar un dato antes de enviar a una API (si no es necesario).

-   Separar datos para audit logs o sistemas de monitoreo.
-   Configuración de aplicaciones (`theme`, `locale`, `features`, etc.).
-   Formularios donde el usuario modifica los valores por defecto.
-   Composición de props en React.

#### Sintaxis —

```js
const obj1 = { a: 1, b: 2 };  
  
// Spread: copia propiedades de obj  
const obj2 = { ...obj1, c: 3 };   
console.log(obj2); // { a: 1, b: 2, c: 3 }  
  
// Rest: extrae las propiedades restantes  
const { a, ...rest } = obj2;  
console.log(a); // 1  
console.log(rest); // { b: 2, c: 3 }
```

#### ¿Cómo funciona internamente? —

Durante la desestructuración:

-   El motor recopila las propiedades no extraídas en un nuevo objeto (`[[Rest]]`).

-   `**Spread**` **es una forma sintáctica de llamar internamente a** `**Object.assign({}, ...)**` **con preservación del orden**.

#### Ejemplos

##### Ejemplo 1 — Desestructuración con `rest`
```js
const obj = { x: 1, y: 2, z: 3 };  
const { x, ...rest } = obj;  
  
console.log(x); // 1  
console.log(rest); // { y: 2, z: 3 }
```
Donde:

-   `const { x, ...rest } = obj;` toma la propiedad `x` y crea una nueva variable llamada `rest` que contiene **todas las propiedades restantes** del objeto original.

-   En este caso, `rest` es un nuevo objeto `{ y: 2, z: 3 }`.

> **Útil cuando**: Deseas acceder a una o dos propiedades específicas y pasar el resto a otro componente, función o API.

##### Ejemplo 2 — Desestructuración desde los `params` de la función
```js
function logUser({ id, ...userInfo }) {  
  console.log('ID:', id); // ID: 42  
  console.log('Datos:', userInfo); // Datos: { name: 'Mauricio', role: 'user' }  
  // ...  
}  
  
logUser({ id: 42, name: 'Mauricio', role: 'user' });
```
Donde: Desestructuramos el objeto para extraer `id` y dejamos el resto (`name`, `role`) dentro de `userInfo`.

> **Útil cuando**: Deseas separar **identificadores o metadatos** de los datos operativos.

##### Ejemplo 3 — Overriding de configuraciones
```js
const defaults = { theme: 'dark', layout: 'grid' };  
const userSettings = { layout: 'list' };  
  
const finalConfig = { ...defaults, ...userSettings };  
console.log(finalConfig); // { theme: 'dark', layout: 'list' }
```
Donde:

-   Se combinan dos objetos (`defaults` y `userSettings`) con `spread`.

-   Las propiedades del segundo (`userSettings`) **sobrescriben** las del primero si coinciden.

##### Ejemplo 4 — React: Composición de props con spread
```js
function Button(props) {  
  const { children, ...rest } = props;  
  return <button {...rest}>{children}</button>;  
}  
  
// Uso:  
<Button className="btn-primary" onClick={handleClick}>  
  Guardar  
</Button>
```
Donde: Separamos `children` para controlarlo, y propagamos el resto (`className`, `onClick`, etc.).

> **Útil ya que:** Es un patrón común en componentes reutilizables para no “re-declarar” cada prop.

##### Ejemplo 5 — Filtrado de campos sensibles
```js
function removeSensitiveFields(user) {  
  const { password, ssn, ...safeData } = user;  
  return safeData;  
}  
  
const sanitized = removeSensitiveFields({  
  name: 'John',  
  email: 'john@example.com',  
  password: '123456',  
  ssn: '999-99-9999',  
});  
  
console.log(sanitized); // { name: 'John', email: 'john@example.com' }
```
Donde: Antes de mostrar o guardar el objeto en logs, se eliminan campos sensibles con `rest`.

##### Ejemplo 6 — Merging de resultados API + locale override
```js
const apiResponse = {  
  title: 'Avengers',  
  description: 'Superhero movie',  
  rating: 8.7,  
};  
  
const localeOverrides = {  
  title: 'Los Vengadores',  
};  
  
const finalData = { ...apiResponse, ...localeOverrides };  
// { title: 'Los Vengadores', description: 'Superhero movie', rating: 8.7 }
```

Donde: Usamos `spread` para adaptar el contenido a distintos idiomas o preferencias sin modificar el resultado original.

#### Riesgos comunes —

##### Ejemplo 1— `Spread` no aplica como copia profunda
```js
const original = {  
  user: {  
    name: 'Mauricio',  
    age: 40,  
  },  
  active: true,  
};  
  
// "Clonamos"  
const copy = { ...original };  
  
// Modificamos una propiedad del objeto anidado  
copy.user.age = 55;  
  
console.log(copy.user.age); // 55  
console.log(original.user.age); // 55 - también cambió el original!!!
```
Aunque `copy` parece una copia nueva de `original`, **la propiedad** `**user**` **sigue siendo la misma referencia** en ambos objetos. Esto ocurre porque `...original` solo hace **shallow copy**, es decir:

-   Copia propiedades **de primer nivel** (clave/valor).

-   Si ese valor es **un objeto**, **la referencia** se copia, no el contenido.

Si necesitas una copia **profunda** (deep copy), debes usar una estrategia adicional como:
```js
const deepCopy = JSON.parse(JSON.stringify(original));  
  
deepCopy.user.age = 55;  
  
console.log(deepCopy.user.age); // 55  
console.log(original.user.age); // 40
```

##### Ejemplo 2— `rest` no aplica como copia profunda
```js
const original = {  
  user: {  
    name: 'Angie',  
    age: 35,  
  },  
  active: true,  
};  
  
// Usamos rest para separar propiedades  
const { active, ...rest } = original;  
  
// Modificamos el objeto anidado en `rest`  
rest.user.age = 21;  
  
console.log(rest.user.age); // 21  
console.log(original.user.age); // 21 - también cambió el original!!!
```

-   El operador `...rest` creó un nuevo objeto con todas las propiedades **excepto** `**active**`.

-   Sin embargo, la propiedad `user` dentro de `rest` sigue siendo la **misma referencia** que en `original`.

Si deseas evitar ese riesgo, deberías hacer una **deep copy manual o asistida**:
```js
const { active, ...rest } = original;  
const safeRest = {  
  ...rest,  
  user: { ...rest.user }, // copia anidada  
};  
  
safeRest.user.age = 21;  
  
console.log(safeRest.user.age); // 21  
console.log(original.user.age); // 35
```
#### Soporte —

-   `rest` [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring#rest_in_object_destructuring)], [[ref](https://caniuse.com/mdn-javascript_operators_destructuring_rest_in_objects)]

-   `spread` [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax#spread_in_object_literals)], [[ref](https://caniuse.com/mdn-javascript_operators_spread_spread_in_object_literals)]

#### A considerar —

-   No hace copia profunda (shallow copy).

-   Solo propiedades propias, no del prototipo.

-   Si hay propiedades repetidas, la última sobrescribe.
-   Usa spread/rest con precaución: no reemplaza lógica de validación profunda.

----------

### 02. Asynchronous Iteration (`for await...of`) [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for-await...of)]

`for await...of` permite iterar asincrónicamente sobre objetos **async iterables** (como streams, APIs paginadas, generadores async, etc.).

#### Principales características —

-   Funciona sobre objetos con `Symbol.asyncIterator`[[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator)].
-   Pausa la ejecución hasta que cada `Promise` se resuelve.

-   Se puede usar dentro de funciones `async`.

#### Ventajas —

-   Facilita el trabajo con flujos de datos asincrónicos.

-   Código más limpio comparado con `.then()` o `forEach + async`.

#### Sintaxis —
```js
async function process() {  
  const asyncIterable = {  
    async *[Symbol.asyncIterator]() {  
      yield "uno";  
      yield "dos";  
      yield "tres";  
    },  
  };  
  
  for await (const val of asyncIterable) {  
    console.log(val);  
  }  
}
```
##### Explicando la sintaxis —

**Paso 1:** Declaración de una **función** **async**
```js
async function process() { ... }
```

> **Nota**: Toda función `async` devuelve automáticamente una **promesa** y nos permite usar `await` dentro de ella.

**Paso 2:** Crear un objeto con un **async iterable**
```js
const asyncIterable = {  
  async *[Symbol.asyncIterator]() {  
    yield "uno";  
    yield "dos";  
    yield "tres";  
  },  
};
```
Donde:

-   Al llamarse `asyncIterable[Symbol.asyncIterator]()`, devuelve un **AsyncGenerator (objeto asincrónicamente iterable)**.
-   Este generador produce (`yield`) valores uno a uno, como una secuencia.

-   Cada `yield` se envuelve automáticamente en una `Promise`.

**Paso 3:** Usar `for await...of`
```js
for await (const val of asyncIterable) {  
  console.log(val);  
}
```
Donde:

-   Itera **de manera asincrónica** sobre un objeto que implemente `Symbol.asyncIterator`.
-   Espera con `await` a que cada `yield` produzca un valor.

-   Asigna ese valor a `val` y ejecuta el cuerpo del bloque `console.log(val)`.

Resumiendo:

-   `**for await**` llama a `**asyncIterator()**` y entra al generador.
-   Primer `**yield**` — `"uno"` se resuelve y se imprime.
-   Segundo `**yield**` — `"dos"` se resuelve y se imprime.
-   Tercer `**yield**` — `"tres"` se resuelve y se imprime.

-   Generador finaliza — `**done: true**` el bucle termina.

#### Ejemplos
#####  Ejemplo 1: Delays simulados
```js
async function* delayedValues() {  
  const delays = [4000, 2000, 3000];  
  for (const ms of delays) {  
    await new Promise(res => setTimeout(res, ms));  
    yield ms;  
  }  
}  
  
async function logDelays() {  
  for await (const ms of delayedValues()) {  
    console.log(`Esperado: ${ms}ms`);  
  }  
}  
  
logDelays();
```
Donde:

`delayedValues` es un **generador asincrónico** (`async function*`) que:

-   Espera 4s, luego devuelve `4000`.

-   Espera 2s, luego devuelve `2000`.
-   Espera 3s, luego devuelve `3000`.

`logDelays` usa `for await...of` para:

-   Iterar sobre cada valor generado por `delayedValues`.

-   Esperar que cada `Promise` se resuelva antes de continuar.
-   Imprimir: `Esperado: 4000ms`, `Esperado: 2000ms`, `Esperado: 3000ms`.

![](https://cdn-images-1.medium.com/max/1600/1*llZNB85nibQz1HhYYn0UsQ.gif)

> **Útil para:** simular tareas asincrónicas que se resuelven en tiempos diferentes (como llamadas a APIs que tardan distinto).

##### Ejemplo 2 — Paginación de películas populares con TMDb + `for await...of`
```js
const API_KEY = 'TU API KEY';   
const BASE_URL = 'https://api.themoviedb.org/3/movie/popular';  
  
async function fetchMoviesPage(page) {  
  const url = `${BASE_URL}?language=en-US&page=${page}`;  
  const res = await fetch(url, {  
    headers: { Authorization: `Bearer ${API_KEY}`}  
  });  
  
  if (!res.ok) throw new Error(`Error HTTP ${res.status}`);  
  return res.json();  
}  
  
// Generador que devuelve todas las películas de TMDb  
async function* fetchAllPopularMovies(maxPages = 3) {  
  let page = 1;  
  while (page <= maxPages) {  
    // Invocamos la función que devuelve las películas de la página actual  
    const data = await fetchMoviesPage(page);  
    // Recorremos los resultados de la página actual y devolvemos la película actual  
    for (const movie of data.results) {  
      yield movie;  
    }  
  
    if (page >= data.total_pages) break;  
    page++;  
  }  
}  
  
// Función principal que muestra las películas de TMDb  
async function main() {  
  console.log('Películas populares de TMDb:');  
  for await (const movie of fetchAllPopularMovies()) {  
    console.log(`= ${movie.title} (${movie.release_date})`);  
  }  
}  
  
// Llamamos a la función principal  
main().catch(console.error);
```
Donde:

-   `fetchAllPopularMovies()` es un **async generator** que va página por página.
-   Usa `yield` para entregar **una película a la vez**.

-   `main()` consume las películas una por una con `for await...of`.

![](https://cdn-images-1.medium.com/max/1600/1*9VIJ8hePvNIeXEke_ahWtA.gif)

> **Útil para:** Procesamiento en batch, consolas, scripts backend, testing, pre-fetch de datos al montar componentes en Next.js.

#### Soporte —

-   `for await…of` [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for-await...of)], [[ref](https://caniuse.com/mdn-javascript_statements_for_await_of)]

#### A considerar —

-   Solo puede **usarse** **dentro de funciones** `**async**`.
-   Ideal para **procesar** **streams**, resultados **paginados**, o **APIs** con espera.
-   **No mezclarlo** con `**.map()**` o `.**forEach()**` si cada paso depende del anterior.

-   Prefiere `for await...of` cuando el orden importa y hay espera real.

----------

### 03.Promise.prototype.finally

`.finally()` es un nuevo método en la cadena de Promesas que se ejecuta **después de que la promesa se resuelve o rechaza**, **sin modificar el valor original**.

#### Principales características —

-   Se ejecuta siempre, sin importar si fue `.then` o `.catch`.
-   No cambia el resultado de la promesa.

-   Ideal para limpiar recursos o resetear estados.

#### Ventajas —

-   Reemplaza patrones repetitivos como duplicar `setLoading(false)` en `.then` y `.catch`.

-   Mejora la claridad y estructura del código asincrónico.

#### Sintaxis —
```js
fetch('/api/data')  
  .then(res => res.json())  
  .catch(err => console.error(err))  
  .finally(() => console.log('Petición finalizada'));
```
-   Se ejecuta **después** de `.then()` o `.catch()`.
-   No recibe parámetros.
-   Retorna una promesa.

-   Si lanzas un error o devuelves una promesa rechazada dentro de `.finally()`, **ese error reemplaza el flujo**

#### Ejemplos
##### Ejemplo 1 — Simulación de espera
```js
function simulateRequest() {  
  return new Promise((resolve, reject) => {  
    const success = Math.random() > 0.5;  
    setTimeout(() => {  
      success ? resolve('Operación exitosa') : reject('Ocurrió un error');  
    }, 1000);  
  });  
}  
  
simulateRequest()  
  .then(console.log)  
  .catch(console.log)  
  .finally(() => {  
    console.log('entra a finally');  
  });
  ```

![](https://cdn-images-1.medium.com/max/1600/1*riWMP6kPloZo2BX0Ev1n1w.gif)

##### Ejemplo 2 — Ocultar un loader en React
```js
'use client';  
  
import { useState, useEffect } from 'react';  
  
interface ProfileProps {  
  name: string;  
  email: string;  
}  
  
function ProfileLoader() {  
  const [loading, setLoading] = useState(false);  
  const [profile, setProfile] = useState<ProfileProps | null>(null);  
  const [error, setError] = useState(null);  
  
  useEffect(() => {  
    setLoading(true);  
    fetch('https://jsonplaceholder.typicode.com/users/1')  
      .then((res) => {  
        if (!res.ok) throw new Error('Error al cargar');  
        return res.json();  
      })  
      .then(setProfile)  
      .catch((err) => setError(err.message))  
      .finally(() => setLoading(false));  
  }, []);  
  
  if (loading) return <p>Cargando...</p>;  
  if (error) return <p>Error: {error}</p>;  
  
  return profile ? (  
    <div>  
      <h1>{profile.name}</h1>  
      <p>{profile.email}</p>  
    </div>  
  ) : null;  
}  
  
export default ProfileLoader;
```

![](https://cdn-images-1.medium.com/max/1600/1*5PZIgWIvD_AP8QvABmzWiw.gif)

#### Soporte —

-   `finally()` [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/finally)] [[ref](https://caniuse.com/promise-finally)]

#### A considerar —

-   No devuelvas nuevos valores dentro de `.finally()` ya que se se ignoran.

-   Usalo para tareas neutrales como limpiar loaders, cerrar conexiones, etc.
-   Evita lógica pesada en `.finally()`.