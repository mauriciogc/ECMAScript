# JavaScript — ES8 (ECMA2017)-Parte I

![](https://cdn-images-1.medium.com/max/1600/1*N6qzeZjsjf7QhjRKlTHSqA.png)

---

#### Temario

- **_Introducción_**
- **_Nuevas Características_**
  - Object.values
  - Object.entries
  - Object.getOwnPropertyDescriptors

---

## i. Introducción

Vamos a ver la primera parte de **novedades** que nos trae la edición **ES8** (_ES2017_) de _JavaScript_.

> Ten en cuenta que **no todos los navegadores modernos admiten de forma nativa todas las funcionalidades “nuevas” de JavaScript**.

> Antes de utilizar alguna de ellas en entornos productivos, **verifica la compatibilidad en los navegadores objetivo**.

> Si estás utilizando un **transpilador como Babel** o algún sistema de build moderno (por ejemplo, Vite, Webpack con configuración adecuada o Next.js con configuración predeterminada), **no deberías preocuparte**, ya que estas herramientas se encargan de transpilar el código a versiones compatibles con navegadores más antiguos.

> Aunque la mayoría de estas funcionalidades ya cuentan con un **amplio soporte en los navegadores actuales**, siempre es recomendable hacer una validación explícita antes de lanzar a producción.

---

## ii. Nuevas Características

### 01. Object.values [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values)]

Si una estructura de datos de _JavaScript_ tiene claves y valores, quiere decir que es una entrada de **clave-valor**, por lo tanto podemos decir que es un **arreglo de dos elementos**.

_JavaScript_ hasta este momento solo teníamos la opción de **obtener las claves** de propiedad enumerables del objeto dado, con el método `Object.keys`:

```js
const country = {
  name: 'México',
  capital: 'Mexico City',
  population: '128.9M',
  flag: '🇲🇽',
};

console.log(Object.keys(country));
//output ['name', 'capital', 'population', 'flag']
```

Con `Object.values` nos va retornar un arreglo de todos los **valores de propiedad** propios del objeto enumerables del objeto dado.

#### Sintaxis —

```js
Object.values(obj);
```

Donde:

- `obj`[obligatorio]- Es un objeto con propiedades pares (clave-valor)

#### Ejemplos

##### Ejemplo 1 — Obtener valores de un objeto

```js
const country = {
  name: 'México',
  capital: 'Mexico City',
  population: '128.9M',
  flag: '🇲🇽',
};

console.log(Object.values(country));
//output ['México', 'Mexico City', '128.9M', '🇲🇽']
```

Podemos observar que:

- La _línea 1_ tenemos el objeto `country`
- Con `Object.values` en la línea 8 podemos obtener todos los valores enumerables del objeto `country`

##### Ejemplo 2— Obtener valores (emojis) de un objeto

```js
const flags = ['🇲🇽', '🇨🇷', '🇵🇪', '🇦🇷', '🇬🇹', '🇵🇦'];

console.log(Object.values(flags));
//output ['🇲🇽', '🇨🇷', '🇵🇪', '🇦🇷', '🇬🇹', '🇵🇦']
```

Podemos observar que:

- `Object.values` también funciona con arreglos (_línea 3)_, esto quiere decir que se puede pasar un arreglo como argumento, en lugar de un objeto, y como resultado, obtendremos un nuevo arreglo de valores.

#### Soporte—

- `Object.values` [**[**ref**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values#browser_compatibility)**]

#### A considerar —

- Nos retorna en un arreglo los valores enumerables del objeto dado.

- También funciona con arreglos.

### 02. Object.entries [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)]

`Object.entries(x)` convierte a `x` en un objeto donde va a devolver las entradas de sus propiedades enumerables con clave de cadena.

Este método es bastante útil ya que nos permite iterar sobre las propiedades de un objeto de manera segura.

> **Nota**: Puedes ocupar `for...in` [[ref](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Statements/for...in)] para iterar un objeto, aunque no se recomienda [[ref](https://exploringjs.com/es6/ch_iteration.html#sec_plain-objects-not-iterable)][[ref](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Statements/for...in#iteraci%C3%B3n_en_arreglos_y_for...in)] (**Pista**: `for...in` enumera propiedades en la cadena de prototipo)

#### Sintaxis —

```js
Object.entries(obj);
```

Donde:

- `obj` [obligatorio] — Objeto cuyos pares de propiedades [clave, valor] enumerables deben ser devueltos.

#### Ejemplos

##### Ejemplo 1 —Obtener un arreglo de clave y valor de un objeto

```js
const country = {
  name: 'México',
  capital: 'Mexico City',
  population: '128.9M',
  flag: '🇲🇽',
};

console.log(Object.entries(country));
/*  
output:  
  0: (2) ['name', 'México']  
  1: (2) ['capital', 'Mexico City']  
  2: (2) ['population', '128.9M']  
  3: (2) ['flag', '🇲🇽']  
*/
```

Podemos observar que:

- Que `Object.entries` nos va a retornar un arreglo de arreglos, donde cada sub arreglo tiene pares de propiedades [clave, valor] enumerables.

Finalmente el objeto podemos iterarlo con `for...of`, `forEach` o cualquier iterador de arreglos.

```js
const country = {
  name: 'México',
  capital: 'Mexico City',
  population: '128.9M',
  flag: '🇲🇽',
};

const countryArr = Object.entries(country);

countryArr.forEach(function ([k, v]) {
  console.log(`${k}: ${v}`);
});
/**  
output:  
  name: México  
  capital: Mexico City  
  population: 128.9M  
  flag: 🇲🇽  
**/

for (let [k, v] of countryArr) {
  console.log(`${k}: ${v}`);
}
/**  
output:  
  name: México  
  capital: Mexico City  
  population: 128.9M  
  flag: 🇲🇽  
**/
```

#### Soporte —

- `Object.entries` [[ref](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/entries#browser_compatibility)]

#### A considerar —

- Si vamos a iterar objetos, como buena práctica se sugiere utilizar `Object.entries` .

- NO enumera propiedades de la cadena de prototipo.
- `Object.entries` para obtener **pares** de propiedades [clave, valor].
- `Object.values` para obtener **valores** de propiedad enumerables.
- `Object.keys` para obtener **claves** de propiedad enumerables.

### 03. Object.getOwnPropertyDescriptors [[ref](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors)]

Sabemos que **cualquier objeto** en _JavaScript_ **tiene** un **conjunto** de **propiedades** que a su vez cada un de **estas propiedades tiene un descriptor**.

Un **descriptor** es un **conjunto de atributos de una propiedad**, que esta **compuesto** por lo siguiente:

- `value` — El valor de la propiedad

- `writeable` — `true` si la propiedad puede cambiar
- `get` — Una función getter para la propiedad, y es llamada cuando se lee la propiedad
- `set` — Una función setter para la propiedad, y es llamada cuando a la propiedad se le establece un valor
- `configurable` — `false` si la propiedad no se puede eliminar, ni cambiar ningún atributo, excepto su valor.
- `enumerable` — `true` si la propiedad es enumerable

Entonces ,el **método** `Object.getOwnPropertyDescriptors` **retorna** todos los **descriptores** de **propiedades** **propias** (_no heredadas_) de un **objeto**.

#### Sintaxis —

```js
Object.getOwnPropertyDescriptors(obj);
```

Donde:

- `obj` [obligatorio] — El objeto que se quiere obtener todos los descriptores

#### Ejemplos

##### Ejemplo 1 —

```js
const country = {
  name: 'México',
  capital: 'Mexico City',
  population: '128.9M',
  flag: '🇲🇽',
};

const descriptors = Object.getOwnPropertyDescriptors(country);

console.log(descriptors);
/**  
output:  
  {  
    capital: {value: 'Mexico City', writable: true, enumerable: true, configurable: true},  
    flag: {value: '🇲🇽', writable: true, enumerable: true, configurable: true},  
    name: {value: 'México', writable: true, enumerable: true, configurable: true},  
    population: {value: '128.9M', writable: true, enumerable: true, configurable: true},  
  }  
*/

console.log(descriptors.name);
/**  
output:  
  {value: 'México', writable: true, enumerable: true, configurable: true}  
*/
```

Podemos observar que:

- En la _línea 8_ estamos obteniendo **todos los descriptores** de las **propiedades** de un **objeto**, y los mostramos en consola en la _línea 10_, hasta podemos acceder a cada uno de ellos (_línea 21_)

##### Ejemplo 2 —

```js
const obj = {
  [Symbol('foo')]: 123,
  get bar() {
    return 'abc';
  },
  qux: 'xyz',
  fred: 123,
};

console.log(Object.getOwnPropertyDescriptors(obj));
/**  
output:  
  {  
    bar: {set: undefined, enumerable: true, configurable: true, get: ƒ}  
    fred: {value: 123, writable: true, enumerable: true, configurable: true}  
    qux: {value: 'xyz', writable: true, enumerable: true, configurable: true}  
    Symbol(foo): {value: 123, writable: true, enumerable: true, configurable: true}  
  }  
*/
```

El método `getOwnPropertyDescriptors` es muy útil al momento de crear copias de un objeto, quizás, me dirás :

“_Oye Mau, pero ya pero ya existe_ `object.assign`_[_[_ref_](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)_] para copiar objetos…_”

Es bastante correcta tu observación, pero recuerda que el método `assign` **copia todas las propiedades enumerables propias** de uno o más objetos y **retorna** un **nuevo** **objeto**, es decir **NO copia correctamente las propiedades** con **atributos** **NO** **predeterminados.**

Veamos el siguiente ejemplo:

```js
const person1 = {
  get name() {
    return 'abc';
  },
};

const person2 = {};

//"Clonamos" person1 a person2
Object.assign(person2, person1); //ouput {name: 'abc'}

//Revisamos los descriptores
console.log(Object.getOwnPropertyDescriptors(person1).name);
// output { set: undefined, enumerable: true, configurable: true, get: ƒ }

console.log(Object.getOwnPropertyDescriptors(person2).name);
// output { value: 'abc', writable: true, enumerable: true, configurable: true }

//Revisamos los objeto
console.log(person1); //output { name: "abc", get name: ƒ name() }
console.log(person2); //output { name: "abc" }
```

Podemos observar que:

- Los descriptores de `person1`y `person2` son totalmente diferentes (_línea 10 y 13_)

- El objeto `person2` pierde el método descriptor `get` ya que `assign` copia todas las propiedades enumerables propias

Para evitar esto, usamos `Object.getOwnPropertyDescriptors` (continuando con el ejemplo de arriba)

```js
const person3 = {};

Object.defineProperties(person3, Object.getOwnPropertyDescriptors(person1));

//Revisamos los descriptores
console.log(Object.getOwnPropertyDescriptors(person1));
// output { name: {set: undefined, enumerable: true, configurable: true, get: ƒ}  }

console.log(Object.getOwnPropertyDescriptors(person3));
// output { name: {set: undefined, enumerable: true, configurable: true, get: ƒ}  }

//Revisamos los objetos
console.log(person1); //output { name: "abc", get name: ƒ name() }
console.log(person3); //output { name: "abc", get name: ƒ name() }
```

Podemos observar que:

- Los descriptores de `person1`y `person3` son exactamente los mismos (_línea 8 y 11_)

#### Soporte —

- `Object.getOwnPropertyDescriptors`[**[**ref**](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptors#browser_compatibility)**]

#### A considerar —

- `getOwnPropertyDescriptors` **retorna** todos los **descriptores** de **propiedades** **propias** (_no heredadas_) de un **objeto**

- Es bastante útil para copiar los descriptores de otro objeto.

- No funciona copiar o mover a aquellos objetos que tengan el método `super` a otro objeto, por lo que aún tiene sus límites.
