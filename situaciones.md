## 14. Situaciones

> Bitácora de incidentes reales que viví durante el aprendizaje. Cada situación documenta: qué pasó, por qué pasó, cómo lo resolví, y la lección a no olvidar. Cuando algo parecido vuelva a pasar, esta es la primera referencia que consulto.

### Índice

- [14.1 — Recuperar trabajo "perdido" después de cambiar de rama](#141--recuperar-trabajo-perdido-después-de-cambiar-de-rama)

---

### 14.1 — Recuperar trabajo "perdido" después de cambiar de rama

**Fecha:** mayo 2026
**Contexto:** Issue #3 de `music-finder` — integración con iTunes Search API.

#### Qué vi

Abrí VS Code y los archivos `js/api.js` y `js/main.js` aparecían vacíos (0 bytes), como si nunca hubiera empezado el Issue #3. En GitHub no aparecía ninguna rama relacionada con iTunes API. Pánico inicial: pensé que había perdido el trabajo.

#### Qué había pasado en realidad

No perdí nada. Lo que pasó fue:

1. Había commiteado el trabajo del Issue #3 en la rama `feat/itunes-api-integration` (commit `0f5751e`).
2. Por error, me cambié a otra rama (`feat/nueva-cosa`) que no contenía esos archivos.
3. **Al cambiar de rama, Git actualiza el working directory para reflejar la rama actual.** Como `feat/nueva-cosa` no tenía `api.js` ni `main.js` con contenido, esos archivos aparecieron vacíos en disco.
4. Los archivos del Issue #3 NO se borraron — siguen guardados dentro del commit `0f5751e` de la rama original. Solo no eran visibles en el working directory mientras estaba parado en otra rama.

#### Cómo lo diagnostiqué

Usé estos comandos en orden:

```bash
git branch --show-current      # me dijo que estaba en main, no en la rama del issue
git status                     # working directory limpio respecto a main
git log --oneline -5           # no veía los commits del issue desde main
git reflog | head -20          # acá apareció la verdad
```

El `git reflog` mostró TODA mi actividad reciente, incluido:

```
0f5751e (feat/itunes-api-integration) HEAD@{3}: commit: feat: integración con iTunes Search API
```

Eso confirmó que el commit existía y la rama existía. Solo no estaba parado en ella.

#### Cómo lo resolví

Un solo comando:

```bash
git checkout feat/itunes-api-integration
```

Con eso, Git actualizó el working directory al estado de esa rama, los archivos volvieron a tener contenido, y VS Code mostró el código del Issue #3 al refrescar.

#### Lecciones a no olvidar

1. **Git nunca borra de verdad nada que hayas commiteado.** El `reflog` guarda todo durante 30-90 días, incluso ramas borradas y commits "perdidos". Cuando entre en pánico, el primer comando siempre es `git reflog`.

2. **Working directory ≠ commits.** Los archivos en disco son una "vista" de la rama actual. Cambiar de rama cambia esa vista. Lo permanente es el commit, no el archivo en disco.

3. **Antes de cambiar de rama, siempre `git status`.** Si hay cambios sin commitear, pueden perderse o generar conflictos. Si todo está commiteado, el cambio es seguro.

4. **Comportamiento normal, no bug.** Que un archivo "desaparezca" al cambiar de rama no es un error de Git — es exactamente lo que tiene que pasar. Cada rama es una versión distinta del proyecto.

#### Comandos clave que aprendí

| Comando | Para qué |
|---|---|
| `git reflog` | Ver TODA la actividad reciente, incluso commits y ramas "borrados". |
| `git reflog \| head -20` | Lo mismo pero limitado a los 20 movimientos más recientes. |
| `git branch --show-current` | Confirmar en qué rama estoy (más rápido que `git branch`). |
| `git checkout <rama>` | Saltar a una rama, actualizando el working directory. |

---
