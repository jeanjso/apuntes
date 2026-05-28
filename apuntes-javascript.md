# Apuntes — JavaScript

> Notas personales de Jean. El lenguaje en sí (sintaxis y conceptos), separado del entorno/herramientas (eso vive en `apuntes-linux-setup-dev.md`).
> Vengo de Java/POO, así que comparo con Java donde ayuda.
> Última actualización: mayo 2026.

---

## Índice

1. [Funciones — fundamentos](#1-funciones--fundamentos)
2. [Funciones de orden superior y callbacks](#2-funciones-de-orden-superior-y-callbacks)
3. Métodos de array (map, filter, reduce) — *pendiente*
4. `this` y contexto — *pendiente*
5. Asincronía (promises, async/await, try/catch) — *pendiente*
6. [ES Modules (import / export)](#6-es-modules-import--export)

> Las secciones 3, 4 y 5 se documentan en próximas sesiones. El índice ya las reserva para mantener el orden conceptual.

---

## 1. Funciones — fundamentos

### Qué es una función

Un bloque de código reutilizable que recibe entradas (parámetros), hace algo, y opcionalmente devuelve un valor con `return`.

**Diferencia mental clave viniendo de Java:** en Java los métodos viven *dentro* de clases. En JavaScript, las funciones son **ciudadanos de primera clase** (*first-class citizens*): existen por sí solas, se pueden guardar en variables, pasar como argumento a otra función, y devolver desde otra función. Esto desbloquea patrones que en Java pre-8 no existían (las lambdas llegaron en Java 8 para acercarse a esto).

### Las tres formas de crear una función

| Forma | Sintaxis | Hoisting | Cuándo usar |
|---|---|---|---|
| **Declaración** | `function nombre() {}` | Sí (subible) | Funciones nombradas, utilidades top-level |
| **Expresión** | `const nombre = function () {}` | No | Cuando querés tratar la función como un valor |
| **Arrow** | `const nombre = () => {}` | No | Callbacks cortos, funciones inline |

#### Declaración de función (function declaration)

```js
function mapTrack(raw) {
  return {
    id: raw.trackId,
    title: raw.trackName,
  };
}
```

Esto es exactamente lo que usé en `api.js`. Es la forma más clásica.

**Hoisting:** las declaraciones se "suben" (hoist) al inicio de su scope. Podés llamarlas *antes* de escribirlas en el código:

```js
saludar(); // Funciona, aunque se declare después
function saludar() {
  console.log("Hola");
}
```

#### Expresión de función (function expression)

La función se asigna a una variable. Es un *valor* que vive en esa variable.

```js
const mapTrack = function (raw) {
  return { id: raw.trackId };
};
```

**No tiene hoisting de la función**, solo de la variable. Si la llamás antes de la línea de asignación, error (`Cannot access 'mapTrack' before initialization` con `const`).

#### Arrow functions (funciones flecha)

Sintaxis más corta, introducida en ES6. Es lo que vas a ver por todos lados en código moderno.

```js
// Forma completa
const sumar = (a, b) => {
  return a + b;
};

// Return implícito: si el cuerpo es una sola expresión, sin llaves y sin return
const sumar = (a, b) => a + b;

// Un solo parámetro: paréntesis opcionales
const doble = x => x * 2;

// Sin parámetros: paréntesis obligatorios y vacíos
const saludar = () => console.log("Hola");

// Devolver un objeto literal: hay que envolverlo en paréntesis
// (sino JS confunde las llaves con un bloque de código)
const crear = id => ({ id });
```

**La trampa más común con arrow:** devolver un objeto sin paréntesis.

```js
const crear = id => { id: id };   // ❌ devuelve undefined (las {} son un bloque)
const crear = id => ({ id: id }); // ✅ devuelve { id: id }
```

**Diferencia importante (se profundiza en la sección 4):** las arrow functions **no tienen su propio `this`**. Heredan el `this` del scope donde se definieron (*lexical this*). Por eso son ideales para callbacks dentro de métodos, pero NO sirven como métodos de objeto si necesitás `this`.

### Parámetros

#### Parámetros por defecto (default params)

```js
function buscar(query, limit = 20) {
  // si no pasás limit, vale 20
}
buscar("coldplay");      // limit = 20
buscar("coldplay", 50);  // limit = 50
```

Esto reemplaza el viejo truco `limit = limit || 20`, que tenía bugs (si pasabas `0`, lo trataba como falsy y usaba el default).

#### Rest params (agrupar argumentos sobrantes)

```js
function sumarTodos(...numeros) {
  return numeros.reduce((acc, n) => acc + n, 0);
}
sumarTodos(1, 2, 3, 4); // 10
```

`...numeros` recoge todos los argumentos en un array. Es el equivalente al varargs de Java (`int... numeros`).

### return

- Una función sin `return` devuelve `undefined`.
- `return` corta la ejecución de la función inmediatamente.
- Solo se puede devolver UN valor; para varios, devolvés un objeto o array.

```js
// Patrón Result que usé en api.js: devuelvo un objeto con varios campos
return { ok: true, data: tracks };
```

### Notas técnicas

- Nombrá las funciones con **verbos**: `searchMusic`, `mapTrack`, `renderResults`. Una función *hace* algo.
- Una función debería hacer **una sola cosa** (Single Responsibility, igual que en Java). Si tu función "busca Y renderiza Y maneja errores", separala.
- Funciones puras (mismo input → mismo output, sin efectos secundarios) son más fáciles de testear. `mapTrack` es pura: recibe un objeto, devuelve otro, no toca nada externo.

---

## 2. Funciones de orden superior y callbacks

### El concepto

Como las funciones son valores, podés:
1. Pasarlas como argumento a otra función → esa función pasada es un **callback**.
2. Devolverlas desde otra función.

Una función que **recibe o devuelve** otras funciones se llama **función de orden superior** (*higher-order function*, HOF).

### Callback

Una función que pasás como argumento para que **otra la ejecute** en algún momento.

```js
// setTimeout recibe una función (callback) y la ejecuta después de 1000ms
setTimeout(() => {
  console.log("Pasó 1 segundo");
}, 1000);
```

### Ejemplo de mi propio código

En `api.js` escribí:

```js
const tracks = data.results.map(mapTrack);
```

Acá:
- `.map()` es una **función de orden superior** (recibe una función).
- `mapTrack` es el **callback** (la función que `.map` va a ejecutar por cada elemento).

`.map` recorre `data.results` y por cada track crudo llama a `mapTrack(track)`, juntando los resultados en un array nuevo.

### La trampa #1 de callbacks: pasar la referencia, NO invocarla

```js
data.results.map(mapTrack);    // ✅ paso la REFERENCIA, map la llama por mí
data.results.map(mapTrack());  // ❌ INVOCO mapTrack ya mismo (sin argumento) y paso su resultado
```

Diferencia crítica:
- `mapTrack` (sin paréntesis) = "acá tenés la función, llamala vos cuando quieras".
- `mapTrack()` (con paréntesis) = "ejecutá la función AHORA y usá lo que devuelva".

Confundir esto es uno de los errores más comunes al empezar.

### Las HOF más usadas (detalle en la sección 3)

Estas tres son higher-order functions que recorren arrays. Las menciono acá porque son el caso típico de HOF; el detalle de cada una va en la sección 3.

```js
const numeros = [1, 2, 3, 4];

numeros.map(n => n * 2);          // [2, 4, 6, 8]   — transforma cada elemento
numeros.filter(n => n % 2 === 0); // [2, 4]         — filtra por condición
numeros.reduce((acc, n) => acc + n, 0); // 10        — acumula a un solo valor
```

### Por qué importa (contexto real)

En Java venías de hacer `for (int i = 0; i < lista.size(); i++)`. En JS moderno casi nunca escribís loops manuales: usás `map`/`filter`/`reduce`. Es más declarativo (*qué* querés, no *cómo* iterar) y menos propenso a errores de índice. React, por ejemplo, se basa muchísimo en `.map()` para renderizar listas.

---

## 6. ES Modules (import / export)

### El problema que resuelven

Antes de los módulos, todo el JavaScript de una página compartía **un solo scope global**. Si cargabas tres archivos `<script>`, todas sus variables y funciones convivían en el mismo espacio. Problemas:

- **Colisiones de nombres:** si dos archivos definían `searchMusic`, uno pisaba al otro.
- **Orden manual frágil:** tenías que cargar los scripts en el orden correcto a mano (primero el que define, después el que usa).
- **Dependencias implícitas:** no quedaba claro qué archivo necesitaba qué.

Así cargaba yo los scripts en el Issue #1 (modo viejo):

```html
<script src="js/api.js" defer></script>
<script src="js/ui.js" defer></script>
<script src="js/main.js" defer></script>
```

Los ES Modules (ESM) resuelven esto: cada archivo es un **módulo con su propio scope**, y declarás explícitamente qué exporta y qué importa.

### export — exponer cosas de un módulo

Dos tipos:

#### Export nombrado (named export)

Exportás cosas con su nombre. Puede haber varios por archivo.

```js
// api.js
export function searchMusic(query) { ... }
export const BASE_URL = "https://itunes.apple.com/search";
```

O agrupados al final:

```js
function searchMusic(query) { ... }
const BASE_URL = "...";
export { searchMusic, BASE_URL };
```

#### Export default

Un solo "valor principal" por archivo. Sin nombre obligatorio al importar.

```js
// api.js
export default function searchMusic(query) { ... }
```

**Regla:** named exports para varias cosas; default para "lo principal" de un módulo. En mi proyecto usé **named export** porque `api.js` podría exportar varias funciones (`searchMusic`, y después `mapTrack` si la necesito afuera).

### import — traer cosas de otro módulo

```js
// Importar named exports: van entre llaves, con su nombre exacto
import { searchMusic } from "./api.js";
import { searchMusic, BASE_URL } from "./api.js";

// Renombrar al importar
import { searchMusic as buscar } from "./api.js";

// Importar un default: sin llaves, nombre libre
import buscar from "./api.js";

// Importar TODO como un objeto (namespace)
import * as api from "./api.js";
api.searchMusic("coldplay");
```

Esto es lo que escribí en `main.js`:

```js
import { searchMusic } from "./api.js";
```

**Comparación con Java:** es parecido a los `import` de paquetes Java, pero más explícito sobre *qué* traés. En vez de `import java.util.*`, en JS importás exactamente `{ searchMusic }`.

### ⚠️ El error que cometí: orden de las palabras clave

```js
async export function searchMusic(query) { ... }  // ❌ SyntaxError
export async function searchMusic(query) { ... }  // ✅ correcto
```

El orden es **fijo**: `export` → `async` → `function`. No es opinión, es regla del lenguaje. Si lo invertís, el módulo entero no carga.

### Cómo se activan en el navegador

Para usar `import`/`export`, el `<script>` debe declararse como módulo:

```html
<script type="module" src="js/main.js"></script>
```

Detalles importantes:

1. **Con `type="module"`, solo cargás el punto de entrada** (`main.js`). El resto se carga solo, en cadena, por los `import`. Por eso pasé de 3 `<script>` a 1.
2. **Los módulos son `defer` por default.** No hace falta poner `defer` (esperan al DOM automáticamente).
3. **Strict mode automático.** Los módulos corren siempre en modo estricto (`"use strict"` implícito): menos comportamientos raros, más errores tempranos.
4. **Scope propio.** Las variables de un módulo NO contaminan el scope global. `window.searchMusic` no existe a menos que lo asignes explícitamente (por eso lo usaba como truco de debug temporal).

### ⚠️ Los módulos NO funcionan con `file://`

Si abrís el HTML con doble clic (protocolo `file://`), los módulos **fallan**. Necesitás un servidor local (`serve .`, Live Server, etc.).

**Por qué (razón de seguridad):** permitir que una página lea archivos arbitrarios del disco vía `import` sería un agujero enorme (una página maliciosa podría leer `~/.ssh/id_ed25519`). El navegador lo impide exigiendo HTTP/HTTPS, donde el "root" está bien definido. Esto es parte de la **Same-Origin Policy**.

> Ver `apuntes-linux-setup-dev.md` → sección 10 (Servidores de desarrollo) para cómo levantar un servidor local.

### Notas técnicas

- Los `import` son **estáticos**: van al tope del archivo y se resuelven antes de ejecutar nada. No podés hacer `import` condicional con `if` (para eso existe el `import()` dinámico, que devuelve una promesa — tema avanzado).
- La extensión `.js` en el path **importa en el navegador** (`from "./api.js"`, no `from "./api"`). Los bundlers como Vite son más permisivos, pero el navegador nativo es estricto.
- `./` significa "mismo directorio", `../` un nivel arriba. Igual que en la terminal.

---

*Estos apuntes son míos y crecen a medida que aprendo. Próximas secciones: array methods, `this`, y asincronía (promises/async-await), que ya toqué en el Issue #3 pero quiero documentar bien.*
