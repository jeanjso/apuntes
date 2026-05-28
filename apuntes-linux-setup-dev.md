# Apuntes — Linux + Setup de desarrollo

> Notas personales de Jean. Compiladas durante el setup inicial de CachyOS para programar full-stack JS.
> Última actualización: mayo 2026.

---

## Índice

1. [Conceptos base de Linux](#1-conceptos-base-de-linux)
2. [La terminal y el shell](#2-la-terminal-y-el-shell)
3. [Comandos esenciales de Unix](#3-comandos-esenciales-de-unix)
4. [El sistema de archivos](#4-el-sistema-de-archivos)
5. [Gestión de paquetes en Arch / CachyOS](#5-gestión-de-paquetes-en-arch--cachyos)
6. [Git y GitHub](#6-git-y-github)
7. [SSH y autenticación](#7-ssh-y-autenticación)
8. [Node.js, npm y fnm](#8-nodejs-npm-y-fnm)
9. [VS Code](#9-vs-code)
10. [Servidores de desarrollo](#10-servidores-de-desarrollo)
11. [Multi-monitor y KDE Plasma](#11-multi-monitor-y-kde-plasma)
12. [Patrones mentales y filosofía de senior](#12-patrones-mentales-y-filosofía-de-senior)
13. [Troubleshooting: cómo pensar cuando algo falla](#13-troubleshooting-cómo-pensar-cuando-algo-falla)
14. [Glosario rápido](#14-glosario-rápido)

---

## 1. Conceptos base de Linux

### ¿Qué es Linux?

Linux es un **kernel** — el corazón del sistema operativo, el programa que habla con tu hardware (CPU, memoria, disco, periféricos). Cuando decimos "Linux" en lenguaje coloquial, en realidad nos referimos a una **distribución de Linux**: el kernel + un conjunto de programas, herramientas y configuraciones.

### Distribuciones (distros)

Una distro es una "receta" preempaquetada de Linux. Algunas familias:

- **Debian / Ubuntu / Mint** → estables, conservadoras, ideal para servidores y principiantes.
- **Fedora / RHEL** → corporativo, lo que usa Red Hat (gran empresa).
- **Arch / Manjaro / CachyOS / EndeavourOS** → rolling release (actualizaciones continuas, sin versiones grandes). Te dan la última de todo, pero con más responsabilidad.

**CachyOS** es una distro basada en Arch optimizada para rendimiento (kernel tuneado, repositorios con paquetes pre-compilados para CPUs modernas). Tiene KDE Plasma como entorno de escritorio por defecto.

### Entornos de escritorio (DE)

El DE es la "cara" gráfica del sistema. Los más comunes:

- **KDE Plasma** (lo que tienes) → completo, configurable, parecido a Windows en estilo.
- **GNOME** → minimalista, parecido a macOS, más opinado.
- **XFCE / Cinnamon / MATE** → más ligeros, para PCs antiguas o quien quiere algo sencillo.

Un sistema Linux puede correr varios DE simultáneamente, eliges en login.

### Sistemas de display: X11 vs Wayland

Cuando ves la interfaz gráfica, hay un sistema que dibuja ventanas:

- **X11** (Xorg) → viejo (30+ años), muy compatible, pero limitado para hardware moderno (multi-monitor con escalas distintas, HiDPI, etc.).
- **Wayland** → reemplazo moderno, lo que usa CachyOS por defecto. Mejor seguridad, mejor multi-monitor, pero algunas apps viejas aún tienen bugs ahí.

**Tip:** cuando una app gráfica te dé problemas raros, una de las primeras cosas a investigar es si es problema de Wayland. Algunas apps (como Discord viejo, algunas screenshot tools) funcionaban mejor en X11. Esto va a ir desapareciendo con el tiempo.

---

## 2. La terminal y el shell

### Terminal vs shell — la confusión típica

Mucha gente los usa como sinónimos, pero son cosas distintas:

- **Terminal (emulador de terminal):** la APP gráfica que abres (Konsole, GNOME Terminal, Alacritty, Kitty, etc.). Es la ventana donde escribes.
- **Shell:** el PROGRAMA que interpreta lo que escribes (bash, zsh, fish, etc.). Es el "lenguaje" que entiende la terminal.

Analogía: la terminal es la ventana del navegador (Chrome, Firefox); el shell es el motor de JavaScript (V8, SpiderMonkey). Distintos roles, ambos necesarios.

### Shells comunes

| Shell | Características | Cuándo usar |
|---|---|---|
| **bash** | Estándar universal, en todo servidor Linux. 30+ años. | Scripts portables, conocer obligatoriamente. |
| **zsh** | Bash + autocompletado inteligente + plugins. Default en macOS. | Tu shell de trabajo diario (lo que usas ahora). |
| **fish** | Muy amigable, sintaxis distinta a bash. | Si te gusta y no vas a copiar scripts de internet. |
| **sh** | El shell POSIX puro. Mínimo. | Scripts ultra-portables. |

### Oh My Zsh

Es un **framework** para zsh: un conjunto de configuraciones, plugins y temas que hacen zsh mucho más útil "out of the box". No es zsh en sí, es una capa encima.

Al instalarlo:

- Crea la carpeta `~/.oh-my-zsh/` con todos sus scripts.
- Sobrescribe tu `~/.zshrc` con una plantilla.
- Te activa plugins y temas con solo nombrarlos.

### Plugins esenciales que usamos

- **git** (viene incluido) → alias de Git (ej: `gst`, `gco`, `gp`) y autocompletado de ramas.
- **node, npm** (vienen incluidos) → autocompletado de comandos de Node y npm.
- **zsh-autosuggestions** → te sugiere comandos en gris claro basado en tu historial. Aceptás con flecha derecha.
- **zsh-syntax-highlighting** → colorea comandos en tiempo real (verde si válido, rojo si inválido). Te ahorra typos.

Los plugins se activan en `~/.zshrc` en la línea:

```bash
plugins=(git node npm zsh-autosuggestions zsh-syntax-highlighting)
```

### El archivo `.zshrc`

Es el archivo de configuración de zsh que se ejecuta cada vez que abrís una terminal. Vive en tu HOME (`~/.zshrc`). Ahí defines:

- Tema del prompt.
- Plugins activos.
- Variables de entorno (PATH, etc.).
- Alias.
- Funciones personalizadas.

**Recargar después de editar:**
```zsh
source ~/.zshrc
```

### Alias

Atajos para comandos largos. Se definen en `.zshrc`:

```bash
alias gs="git status"
alias ll="ls -lah"
alias ..="cd .."
```

Mis alias actuales:

```bash
# Git
alias gs="git status"
alias ga="git add"
alias gc="git commit -m"
alias gp="git push"
alias gl="git log --oneline --graph --decorate -20"
alias gd="git diff"
alias gco="git checkout"
alias gcb="git checkout -b"

# Navegación
alias ..="cd .."
alias ...="cd ../.."
alias ll="ls -lah"

# Utilidad
alias c="clear"
```

---

## 3. Comandos esenciales de Unix

### Navegación

| Comando | Qué hace | Ejemplo |
|---|---|---|
| `pwd` | Print Working Directory: te dice dónde estás | `pwd` → `/home/jean` |
| `ls` | List: lista archivos del directorio actual | `ls`, `ls -la` (incluye ocultos y detalles) |
| `cd` | Change Directory | `cd ~/dev`, `cd ..` (subir un nivel), `cd -` (al directorio anterior) |
| `~` | Atajo para tu HOME | `cd ~` = `cd /home/jean` |

### Archivos y directorios

| Comando | Qué hace |
|---|---|
| `mkdir nombre` | Crear directorio |
| `mkdir -p a/b/c` | Crear directorios anidados (la `-p` evita errores si existen) |
| `touch archivo.txt` | Crear archivo vacío (o actualizar timestamp) |
| `cp origen destino` | Copiar |
| `cp -r carpeta/ destino/` | Copiar recursivamente (carpetas) |
| `mv origen destino` | Mover o renombrar |
| `rm archivo` | Borrar archivo |
| `rm -r carpeta` | Borrar carpeta recursivamente |
| `rm -rf carpeta` | ⚠️ Borrar forzando, sin preguntar (peligroso) |

### Lectura de archivos

| Comando | Qué hace |
|---|---|
| `cat archivo` | Imprime todo el contenido |
| `less archivo` | Ver con scroll (q para salir) |
| `head -20 archivo` | Primeras 20 líneas |
| `tail -20 archivo` | Últimas 20 líneas |
| `tail -f archivo` | "Follow": útil para logs en tiempo real |
| `grep "texto" archivo` | Busca líneas que contengan "texto" |

### Búsqueda

| Comando | Qué hace |
|---|---|
| `find /ruta -name "*.js"` | Busca archivos por nombre |
| `grep -r "texto" /ruta` | Busca texto dentro de archivos, recursivo |
| `which comando` | Te dice dónde está instalado un programa |

### Permisos y propietarios

| Comando | Qué hace |
|---|---|
| `sudo comando` | Ejecutar como root (administrador) |
| `chmod +x archivo` | Hacer ejecutable |
| `chown usuario:grupo archivo` | Cambiar propietario |

**Nota:** `sudo` no se debe usar para todo. Si estás haciendo `sudo` mucho, probablemente estás en el directorio equivocado o instalando algo en el lugar equivocado (ej: paquetes npm globales — por eso usamos fnm en home).

### Operadores útiles

| Operador | Qué hace |
|---|---|
| `comando1 && comando2` | Ejecuta el segundo SOLO si el primero tuvo éxito |
| `comando1 \|\| comando2` | Ejecuta el segundo SOLO si el primero falló |
| `comando1 ; comando2` | Ejecuta ambos en orden, sin importar resultado |
| `comando \| otro` | Pipe: la salida del primero es entrada del segundo |
| `comando > archivo` | Redirige salida a archivo (sobrescribe) |
| `comando >> archivo` | Redirige y añade al final |
| `> archivo` | Vacía un archivo (sin comando) |

**Ejemplos prácticos:**

```bash
# Listar archivos y filtrar por nombre
ls -la | grep "config"

# Buscar palabra "fnm" en .zshrc
grep "fnm" ~/.zshrc

# Vaciar un archivo sin borrarlo
> ~/.zshrc
```

### Atajos de teclado en terminal

| Atajo | Qué hace |
|---|---|
| `Ctrl + C` | Mata el proceso actual (cancelar comando) |
| `Ctrl + D` | EOF / cerrar shell (logout) |
| `Ctrl + L` | Limpiar pantalla (igual que `clear`) |
| `Ctrl + R` | Buscar en historial (escribe para filtrar) |
| `Ctrl + A` | Ir al inicio de la línea |
| `Ctrl + E` | Ir al final de la línea |
| `Ctrl + U` | Borrar toda la línea desde el cursor hacia atrás |
| `Ctrl + K` | Borrar desde el cursor hasta el final |
| `Ctrl + W` | Borrar la palabra anterior |
| `Flecha arriba/abajo` | Navegar historial |
| `Tab` | Autocompletar (presiona dos veces para ver opciones) |

---

## 4. El sistema de archivos

### Estructura básica de Linux (FHS — Filesystem Hierarchy Standard)

Todo en Linux cuelga de `/` (raíz). No hay "C:" ni "D:" como en Windows.

```
/                   ← raíz
├── bin/            ← binarios esenciales (ls, cp, etc.) — suele ser symlink a /usr/bin/
├── boot/           ← kernel y bootloader (GRUB)
├── dev/            ← dispositivos (teclado, disco, etc.) representados como archivos
├── etc/            ← configuración del sistema (TODO archivo de config global aquí)
├── home/           ← carpetas personales de usuarios
│   └── jean/       ← TU HOME (también accesible como ~)
├── lib/            ← librerías compartidas
├── media/, mnt/    ← puntos de montaje para USBs, particiones extra
├── opt/            ← software opcional/de terceros
├── proc/           ← info del kernel y procesos en memoria (no es disco real)
├── root/           ← HOME del usuario root
├── sbin/           ← binarios del sistema (solo root)
├── tmp/            ← archivos temporales (se borra al reiniciar)
├── usr/            ← programas de usuario + librerías
│   ├── bin/        ← binarios instalados (donde están casi todos tus comandos)
│   ├── lib/        ← librerías
│   └── share/      ← archivos compartidos (docs, iconos, etc.)
└── var/            ← datos variables (logs, caches, mail)
```

**Lo que importa para ti como dev:**

- `/home/jean/` (alias `~`) → todo tu trabajo personal. Aquí van proyectos, configs (`.zshrc`, `.gitconfig`), todo.
- `/etc/` → configs globales del sistema (raramente las tocas).
- `/usr/bin/` → donde están los programas instalados (no edites nada acá).

### Archivos ocultos

En Linux, **cualquier archivo o carpeta cuyo nombre empiece con un punto `.` es oculto**. No se muestran en `ls` normal.

Ejemplos:
- `.zshrc` → config de zsh
- `.gitconfig` → config global de git
- `.ssh/` → carpeta con tus llaves SSH
- `.config/` → configs de muchas apps (KDE, VS Code en algunos casos)
- `.git/` → metadata de un repo Git

Para verlos:

```zsh
ls -la
```

### Rutas absolutas vs relativas

- **Absoluta:** empieza con `/`. Funciona desde cualquier lugar.
  - Ejemplo: `/home/jean/dev/music-finder/index.html`
- **Relativa:** desde donde estás parado.
  - Ejemplo: `./index.html` (en el directorio actual), `../config.json` (un nivel arriba).

**Tip:** `.` es el directorio actual, `..` el de arriba. `~` es tu HOME.

### Permisos

Cada archivo tiene 3 niveles de permisos (lectura/escritura/ejecución) para 3 tipos de usuarios (dueño/grupo/otros).

Cuando haces `ls -la` ves cosas como:

```
-rwxr-xr-x  1 jean jean  7374576  Mar  5 21:51 fnm
```

- `-rwxr-xr-x` → permisos
  - `-` tipo de archivo (- = regular, d = directorio)
  - `rwx` permisos del dueño (read, write, execute)
  - `r-x` permisos del grupo
  - `r-x` permisos de otros
- `jean jean` → dueño y grupo
- `7374576` → tamaño en bytes
- `Mar 5 21:51` → fecha
- `fnm` → nombre

---

## 5. Gestión de paquetes en Arch / CachyOS

### Pacman: el gestor de paquetes oficial

Pacman es el equivalente a `apt` (Debian/Ubuntu) o `dnf` (Fedora) en Arch.

| Comando | Qué hace |
|---|---|
| `sudo pacman -Syu` | Actualizar TODO el sistema (sync + upgrade) |
| `sudo pacman -S paquete` | Instalar paquete |
| `sudo pacman -S --needed paquete` | Instalar solo si no existe ya |
| `sudo pacman -R paquete` | Desinstalar (mantiene dependencias) |
| `sudo pacman -Rns paquete` | Desinstalar paquete + sus dependencias + configs |
| `pacman -Ss palabra` | Buscar paquetes |
| `pacman -Q` | Listar paquetes instalados |
| `pacman -Qi paquete` | Info detallada de un paquete instalado |

**Reglas de oro en Arch:**

1. **Siempre actualiza ANTES de instalar.** `sudo pacman -Syu`. Arch es "rolling release" y mezclar repos viejos con paquetes nuevos te puede romper el sistema.
2. **Nunca canceles un `pacman -Syu` a la mitad.** Puede dejar tu sistema inconsistente.
3. **`-Syyu` es agresivo** (fuerza re-descargar la lista de repos). Solo úsalo si sospechas mirrors corruptos.

### AUR (Arch User Repository)

Repositorio comunitario con paquetes que no están en repos oficiales. Lo manejas con un AUR helper:

- **`paru`** (lo más moderno, escrito en Rust)
- **`yay`** (clásico, escrito en Go)

CachyOS suele venir con uno de los dos preinstalado.

```bash
paru -S paquete-aur
yay -S paquete-aur
```

**Cuidado con AUR:**

- Es código que cualquiera puede subir. Lee el `PKGBUILD` antes de instalar.
- Compila desde fuente, puede tardar.
- Algunos paquetes están abandonados.
- Solo usa AUR cuando algo NO esté en repos oficiales.

### Filosofía: qué instalar de dónde

| Tipo de paquete | Dónde instalar |
|---|---|
| Programas grandes y conocidos (Firefox, VS Code, Node) | Pacman (repos oficiales) si están |
| Software propietario (Chrome, Spotify, Discord) | AUR usualmente |
| Herramientas de desarrollo (Node versions, Python versions) | Gestores especializados (fnm, pyenv) |
| Paquetes de un lenguaje específico (librerías npm, pip, etc.) | El gestor del lenguaje, no pacman |

**Antipatrón a evitar:** instalar Node con `sudo pacman -S nodejs`. Eso te ata a una sola versión y te obliga a usar `sudo` para `npm install -g`. Mucho mejor: fnm en tu home.

---

## 6. Git y GitHub

### Índice de la sección

- [Qué es Git](#qué-es-git)
- [Configuración inicial](#configuración-inicial)
- [Conceptos clave](#conceptos-clave)
- [Flujo básico](#flujo-básico)
- [Commits convencionales (Conventional Commits)](#commits-convencionales-conventional-commits)
- [Branches (ramas)](#branches-ramas)
- [Pull Requests (PRs)](#pull-requests-prs)
- [Merge vs Rebase: dos formas de integrar cambios](#merge-vs-rebase-dos-formas-de-integrar-cambios)
- [Rebase interactivo: limpiar historia antes de pushear](#rebase-interactivo-limpiar-historia-antes-de-pushear)
- [Reflog: la red de seguridad de Git](#reflog-la-red-de-seguridad-de-git)
- [Comandos útiles](#comandos-útiles)
- [.gitignore](#gitignore)

---

### Qué es Git

Git es un **sistema de control de versiones distribuido**. Te permite:

- Guardar el historial completo de tu código.
- Volver a versiones anteriores.
- Trabajar en ramas paralelas (branches) sin romper lo principal.
- Colaborar con otros sin pisar el trabajo de nadie.

**Git ≠ GitHub.** Git es la herramienta (corre en tu compu); GitHub es un servicio web que aloja repos remotos. Otras alternativas: GitLab, Bitbucket, Codeberg.

### Configuración inicial

Solo se hace una vez por máquina:

```bash
git config --global user.name "Jean"
git config --global user.email "tu-email@ejemplo.com"
git config --global init.defaultBranch main
git config --global pull.rebase false
git config --global core.editor "code --wait"
```

Verificar con: `git config --global --list`

> ⚠️ Si Git no encuentra el editor configurado, cae al default `vi`. Si `vi` no está instalado (como en CachyOS por default), te tira `error: unable to start editor 'vi'` cuando intentes hacer `commit` sin `-m`, `rebase -i`, o cualquier operación que requiera abrir un editor. Tener `core.editor` configurado desde el día 1 evita el problema.

### Conceptos clave

- **Repository (repo):** un proyecto con historial de Git. Es una carpeta con una subcarpeta oculta `.git/` adentro.
- **Working directory:** los archivos tal cual están en tu disco.
- **Staging area (index):** zona intermedia donde "preparas" los cambios que vas a commitear.
- **Commit:** una "foto" del estado del proyecto en un momento. Cada commit tiene un mensaje y un hash único.
- **Branch (rama):** una línea paralela de desarrollo. `main` es la principal por convención.
- **Remote:** una copia del repo en otro lugar (GitHub, etc.). El remote por defecto se llama `origin`.
- **HEAD:** puntero que indica dónde estoy parado en la historia (típicamente el último commit de la rama actual).

### Flujo básico

```bash
# 1. Crear un repo nuevo
mkdir mi-proyecto && cd mi-proyecto
git init

# 2. Modificar/crear archivos
echo "# Mi proyecto" > README.md

# 3. Ver qué cambió
git status

# 4. Agregar al staging
git add README.md
# o agregar TODO lo modificado:
git add .

# 5. Commitear
git commit -m "feat: agregar README inicial"

# 6. Conectar con un remote (la primera vez)
git remote add origin git@github.com:jeanjso/mi-proyecto.git

# 7. Subir al remote
git push -u origin main
# en pushes siguientes solo:
git push
```

### Commits convencionales (Conventional Commits)

Estándar de la industria para escribir mensajes de commit consistentes:

```
tipo: descripción corta

[cuerpo opcional con más detalle]
```

Tipos comunes:

- `feat:` → nueva funcionalidad
- `fix:` → corrección de bug
- `refactor:` → cambio de código que no añade feature ni arregla bug
- `docs:` → cambios en documentación
- `style:` → formato (sin cambiar lógica)
- `test:` → agregar/modificar tests
- `chore:` → tareas de mantenimiento (configs, deps, etc.)

Ejemplos:

```
feat: integración con iTunes Search API
fix: corregir error de CORS en búsqueda
docs: actualizar README con instrucciones de setup
refactor: extraer lógica de fetch a módulo separado
```

**Regla mental fix vs feat:**
- `fix:` → estoy arreglando algo que ya estaba en `main` y funcionaba mal.
- `feat:` → estoy construyendo algo nuevo (incluso si durante el desarrollo "corrijo" mi propio código antes de mergear).

### Branches (ramas)

**Nunca trabajes directo en `main`.** Cada feature/cambio va en su propia rama.

```bash
# Crear una rama nueva y cambiarte a ella
git checkout -b feat/buscador

# Ver en qué rama estás
git branch                    # lista con asterisco en la actual
git branch --show-current     # solo el nombre, más rápido

# Cambiarte a otra rama existente
git checkout main

# Mergear una rama en la actual
git merge feat/buscador

# Borrar una rama ya mergeada
git branch -d feat/buscador
# Forzar borrado (peligroso, ignora si está mergeada o no)
git branch -D feat/buscador
```

**Regla de oro al crear una rama nueva:** siempre desde un `main` actualizado.

```bash
git checkout main
git pull origin main          # ← traer últimos cambios remotos
git checkout -b feat/nueva-cosa
```

Si te saltás el `pull`, tu rama nace "atrás" de main y vas a tener que sincronizar después (con conflictos potenciales). Es la causa #1 de divergencias raras en juniors.

### Pull Requests (PRs)

En GitHub, los PRs son "propuestas de cambios": tu rama vs main. Permiten:

- Revisión de código antes de mergear.
- Discusión inline.
- Integración con CI/CD (corre tests automáticos).

Flujo típico:

1. Crear rama localmente.
2. Hacer commits.
3. `git push origin nombre-rama`.
4. En GitHub: "Compare & pull request".
5. Llenar descripción.
6. Pedir review (o aprobar tú mismo si trabajas solo).
7. Merge.
8. Borrar la rama.

**Cerrar issues desde el PR:** en la descripción del PR escribí `Closes #N` (o `Fixes #N`, `Resolves #N`). Cuando se mergea, GitHub cierra el issue automáticamente. Conecta planificación con código.

### Merge vs Rebase: dos formas de integrar cambios

Cuando dos ramas divergieron y querés "juntarlas", hay dos estrategias. Entender la diferencia es clave para no romper el repo.

#### Merge (lo conservador)

```bash
git checkout main
git merge feat/buscador
```

Toma el último commit de `feat/buscador` y lo combina con `main`, creando un **merge commit** nuevo si hubo divergencia.

**Ventaja:** preserva la historia tal cual ocurrió. Si tu rama vivió 3 días en paralelo, queda registrado.
**Desventaja:** la historia se vuelve "ramificada" y a veces difícil de leer con muchos PRs.

#### Rebase (el quirúrgico)

```bash
git checkout feat/buscador
git rebase main
```

**Reescribe** los commits de `feat/buscador` para que aparezcan como si hubieran salido de `main` actualizado. Resultado: historia lineal y limpia, sin merge commits.

**Ventaja:** historia clara, fácil de leer, ideal para PRs con pocos commits.
**Desventaja:** reescribe historia → los hashes de commits cambian. Si esos commits ya estaban pusheados a una rama compartida, **rompés el repo de los demás**.

#### ⚠️ REGLA DE ORO DEL REBASE

**Nunca hagas rebase de commits que ya estén pusheados a una rama compartida.** Los hashes cambian, y cualquiera que tuviera esos commits queda con una "rama paralela" rota. Solo se rebasea historia **local** (commits que solo viven en tu compu).

#### Cuándo usar cada uno (opinión de senior)

| Situación | Estrategia |
|---|---|
| Mergear PR a `main` en GitHub | **Merge** (GitHub crea el merge commit) |
| Actualizar tu rama de feature con cambios de `main` antes del PR | Cualquiera; merge es más seguro al empezar |
| Tu rama tiene 5 commits "WIP" feos y querés limpiarlos antes del PR | **Rebase interactivo** (ver sección siguiente) |
| Conflictos complicados con muchos archivos | **Merge** (resolves una vez, no por commit) |

### Rebase interactivo: limpiar historia antes de pushear

El rebase interactivo (`-i`) te deja **editar la lista de commits** antes de aplicarlos: fusionar, reordenar, renombrar mensajes, o descartar commits.

#### Caso de uso típico

Hiciste 3 commits durante el desarrollo: `feat: empezar buscador`, `fix: typo`, `wip: terminar buscador`. Antes de abrir el PR, querés que aparezca **un solo commit limpio**: `feat: buscador con preview de audio`.

#### Comando

```bash
git rebase -i HEAD~3
```

`HEAD~3` = "los 3 últimos commits". Git abre un editor con la lista:

```
pick a1b2c3d feat: empezar buscador
pick e4f5g6h fix: typo
pick i7j8k9l wip: terminar buscador
```

Los commits aparecen en **orden cronológico** (el más viejo arriba), al revés que en `git log`.

#### Operaciones disponibles

| Comando | Corto | Qué hace |
|---|---|---|
| `pick` | `p` | Mantener el commit como está |
| `reword` | `r` | Mantener el commit pero cambiar el mensaje |
| `edit` | `e` | Pausar acá para modificar el commit (avanzado) |
| `squash` | `s` | Fusionar con el commit anterior (combina mensajes) |
| `fixup` | `f` | Fusionar con el anterior pero **descarta** el mensaje del commit fusionado |
| `drop` | `d` | Eliminar el commit por completo |

Para el caso del ejemplo, editás la lista a:

```
pick a1b2c3d feat: empezar buscador
squash e4f5g6h fix: typo
squash i7j8k9l wip: terminar buscador
```

Guardás, cerrás el editor. Git abre **otro editor** con los mensajes combinados de los tres commits para que escribas el mensaje final. Editás, guardás, cerrás. Listo: ahora tenés un solo commit.

#### Comandos de emergencia

Si algo sale mal durante un rebase (conflicto, lo iniciaste por error, etc.):

```bash
git rebase --abort        # ⭐ cancela todo y vuelve al estado anterior
git rebase --continue     # después de resolver conflictos, sigue
git rebase --skip         # saltarse el commit actual (avanzado)
```

`--abort` es tu botón de pánico: no es destructivo, te devuelve a como estabas antes del `rebase -i`. Usalo sin culpa.

#### Tip: GitLens Interactive Rebase

Si tenés VS Code con la extensión GitLens, al lanzar `git rebase -i HEAD~N` se abre una **UI gráfica** con dropdowns para cada commit. Más a prueba de errores que editar el archivo de texto. Ojo: GitLens muestra los commits en orden **inverso** al editor de texto (más nuevo arriba), pero la lógica es la misma.

#### Ejemplo real (Issue #3 de music-finder)

Terminé con dos commits duplicados accidentalmente, ambos con el mismo mensaje:

```
0f5751e feat: integración con iTunes Search API
b0e138f feat: integración con iTunes Search API
```

Como **no los había pusheado todavía** (vivían solo en local), pude hacer `git rebase -i HEAD~2` y squashearlos en uno solo. El commit final quedó con un hash nuevo (`b1394c3`) porque al reescribir historia, Git genera un commit nuevo. Aprendizaje: si hubiera pusheado antes, ya no podría haber hecho esa limpieza sin romper el repo remoto.

### Reflog: la red de seguridad de Git

El **reflog** es el log de TODOS los movimientos de `HEAD` en tu repo local. Cada vez que hacés un commit, cambiás de rama, hacés reset, rebase, merge, lo que sea — queda registrado en el reflog.

**Esto significa una cosa enorme:** Git **nunca borra de verdad nada** que vos hayas commiteado, al menos durante un tiempo. Aunque borres una rama, hagas `reset --hard`, o pierdas commits en un rebase, las "huellas" siguen ahí.

#### Comando básico

```bash
git reflog
```

Te muestra los últimos movimientos en orden cronológico **inverso** (el más reciente arriba):

```
0d1cc2e HEAD@{0}: pull origin main: Fast-forward
6afd9d5 HEAD@{1}: checkout: moving from feat/itunes-api to main
b1394c3 HEAD@{2}: rebase -i (finish): returning to refs/heads/feat/itunes-api
b1394c3 HEAD@{3}: rebase -i (squash): feat: integración con iTunes Search API
0f5751e HEAD@{4}: commit: feat: integración con iTunes Search API
b0e138f HEAD@{5}: commit: feat: integración con iTunes Search API
```

Cada línea tiene:
- **Hash** del commit donde estaba `HEAD` en ese momento.
- **`HEAD@{N}`**: el "N" es la posición en el reflog (0 = más reciente).
- **Descripción** de la operación (commit, checkout, rebase, reset, etc.).

#### Para qué sirve realmente

| Situación | Cómo recuperar con reflog |
|---|---|
| Borraste una rama por error | Encontrás su último commit en reflog, `git branch nombre-rama <hash>` |
| Hiciste `git reset --hard` y perdiste commits | `git reset --hard <hash>` al estado anterior |
| Un rebase te dejó la rama vacía o rota | `git reset --hard <hash>` antes del rebase |
| "Desaparecieron" archivos al cambiar de rama (en realidad están en otro commit) | `git checkout <rama-correcta>` |

#### Cuánto dura el reflog

Por default:
- **30 días** para commits "no alcanzables" (no apuntados por ninguna rama o tag).
- **90 días** para commits alcanzables.

Configurable con `gc.reflogExpire`. En la práctica, si te diste cuenta del problema en el mismo día o semana, **el reflog te salva**.

#### Limitaciones importantes

- **El reflog es LOCAL**. No se sincroniza con remotos. Si clonás un repo en otra máquina, no tenés el reflog de la original.
- **No incluye archivos sin commitear.** Si hiciste cambios en disco sin agregar a stage ni commitear y los descartaste con `git restore .`, el reflog NO los recupera. Para eso existe `git stash`.

#### Flujo de pánico (cuando creés que perdiste trabajo)

1. **No corras nada destructivo todavía.** No `reset`, no `checkout` forzado.
2. `git status` para ver el estado actual.
3. `git reflog | head -20` para ver tu historia reciente.
4. Identificá el hash donde estaba tu trabajo (busca el último `commit:` del trabajo que querés).
5. Decidí cómo volver:
   - Si querés mover tu rama actual a ese punto: `git reset --hard <hash>`.
   - Si querés ir a ese commit "de visita": `git checkout <hash>`.
   - Si querés crear una rama nueva desde ese commit: `git branch rescate <hash>`.

Ver el incidente concreto en **Situaciones → 14.1 (Recuperar trabajo "perdido" después de cambiar de rama)**.

### Comandos útiles

```bash
# Ver historial bonito
git log --oneline --graph --decorate -20
git log --oneline --graph --all -20      # incluye TODAS las ramas

# Ver qué cambió desde el último commit
git diff

# Ver cambios ya en staging
git diff --staged

# Descartar cambios no commiteados (versión moderna)
git restore archivo.txt
git restore .                             # todos los archivos

# Descartar cambios no commiteados (versión vieja, también funciona)
git checkout -- archivo.txt

# Deshacer el último commit (manteniendo cambios)
git reset --soft HEAD~1

# Deshacer el último commit (descartando cambios) ⚠️ peligroso
git reset --hard HEAD~1

# Guardar cambios temporalmente sin commitear
git stash
git stash push -m "WIP: descripción del por qué"
# y recuperarlos:
git stash pop
git stash list                            # ver pila de stashes

# Modificar el último commit (mensaje o agregar archivos)
git commit --amend                        # abre editor para cambiar mensaje
git commit --amend --no-edit              # mantiene el mensaje, agrega cambios staged

# Ver qué archivos modificó un commit
git show --stat <hash>

# Probar conexión SSH a GitHub
ssh -T git@github.com
```

### .gitignore

Archivo que le dice a Git qué ignorar. Ejemplos típicos para JS:

```
node_modules/
.env
.env.local
dist/
build/
*.log
.DS_Store
```

**Regla de oro:** nunca subas `node_modules/` (gigante e innecesario), ni archivos con secretos (`.env`).

> Si ya commiteaste algo que debería estar en `.gitignore`, agregarlo al archivo NO lo saca del repo. Hay que ejecutar `git rm --cached <archivo>` y commitear ese borrado. Por eso conviene tener `.gitignore` desde el primer commit.

---

## 7. SSH y autenticación

### Qué es SSH

Secure Shell — protocolo para conexiones seguras encriptadas. Lo usamos para:

1. Conectarse a servidores remotos por línea de comandos.
2. **Autenticarse en GitHub** sin tener que escribir password cada push.

### Cómo funciona la autenticación con llaves

Generas un par de llaves:

- **Llave privada** (`~/.ssh/id_ed25519`) → se queda en tu máquina, NUNCA se comparte.
- **Llave pública** (`~/.ssh/id_ed25519.pub`) → se sube a GitHub.

Cuando te conectas:

1. GitHub te manda un "desafío" cifrado con tu llave pública.
2. Tu máquina lo descifra con la llave privada.
3. GitHub verifica que lo descifraste bien → te autentica.

Es como un candado (pública) y llave (privada). Cualquiera puede tener el candado, pero solo tú abres con la llave.

### Generar y configurar (resumen del proceso)

```bash
# Generar llave
ssh-keygen -t ed25519 -C "tu-email@ejemplo.com"
# Enter para todos los prompts (sin passphrase para uso casual)

# Ver la llave pública
cat ~/.ssh/id_ed25519.pub
# Copiar TODO el output

# En GitHub: Settings → SSH and GPG keys → New SSH key → pegar

# Probar
ssh -T git@github.com
# Debe responder: "Hi <usuario>! You've successfully authenticated..."
```

### HTTPS vs SSH para Git

GitHub permite dos formas de clonar:

- **HTTPS:** `https://github.com/user/repo.git`
  - Pide usuario y password cada push (o token de acceso personal).
  - Pasa por puerto 443 (típicamente abierto en redes corporativas).
- **SSH:** `git@github.com:user/repo.git`
  - Usa tus llaves SSH, sin escribir password.
  - Puerto 22 (a veces bloqueado en redes corporativas restrictivas).

**Recomendación general:** SSH para tu compu personal, HTTPS si estás en una red restrictiva.

Cambiar un repo de HTTPS a SSH:

```bash
git remote set-url origin git@github.com:user/repo.git
```

### ed25519 vs RSA

Cuando generas llaves, hay distintos algoritmos:

- **ed25519** (lo que usamos) → moderno, rápido, seguro, llaves cortas. Recomendado.
- **RSA 4096** → clásico, seguro pero más lento y llaves más largas.
- **DSA, ECDSA** → no usar.

---

## 8. Node.js, npm y fnm

### Node.js

Runtime de JavaScript que corre fuera del navegador. Permite usar JS para:

- Backend (servidores).
- Herramientas de línea de comandos.
- Procesos de build (bundlers como Vite, webpack).
- Scripts de automatización.

### npm (Node Package Manager)

Viene incluido con Node. Es:

1. Un **CLI** para instalar paquetes (`npm install`).
2. Un **registro** de paquetes públicos (npmjs.com).
3. Un sistema de **scripts** en `package.json`.

Comandos esenciales:

```bash
# Iniciar un proyecto
npm init           # interactivo
npm init -y        # default automático

# Instalar dependencia
npm install lodash     # local al proyecto (en node_modules/)
npm install -g serve   # global (en sistema)

# Instalar como devDependency (solo desarrollo)
npm install --save-dev eslint
# o corto:
npm i -D eslint

# Quitar
npm uninstall lodash

# Correr scripts definidos en package.json
npm run dev
npm test
```

### package.json y package-lock.json

- **`package.json`** → manifiesto del proyecto. Define metadata, dependencias, scripts. Lo editas tú o lo manipula npm.
- **`package-lock.json`** → snapshot exacto del árbol de dependencias. Lo genera npm automáticamente. **SÍ se sube a Git** (garantiza que todos instalen lo mismo).
- **`node_modules/`** → carpeta gigante con las dependencias instaladas. **NO se sube a Git** (va en .gitignore).

### Versiones de Node y por qué fnm

Distintos proyectos pueden necesitar distintas versiones de Node. Instalar Node "nativo" con `sudo pacman -S nodejs` te ata a una sola. Solución: **gestores de versiones**.

Opciones:

- **fnm** (lo que usamos) → Fast Node Manager, escrito en Rust. Rápido.
- **nvm** → el clásico, escrito en bash. Lento al iniciar shell.
- **volta** → alternativa moderna.

### Comandos de fnm

```bash
# Instalar una versión específica
fnm install 22         # Node 22 LTS

# Instalar la última LTS
fnm install --lts

# Listar instaladas
fnm list

# Usar una versión en la sesión actual
fnm use 22

# Hacerla default (cada shell nueva la usa)
fnm default 22

# Desinstalar
fnm uninstall 24

# Ver dónde está
fnm current
```

### Versiones LTS vs Current

Node libera versiones cada 6 meses:

- **LTS (Long Term Support)** → versiones **pares** (18, 20, 22). Soporte por 3 años. Lo que usan empresas en producción.
- **Current** → versiones **impares** (19, 21, 23, 25). Experimental, dura 6 meses.

**Regla:** para aprender/trabajar profesionalmente, siempre LTS. Para experimentar features nuevos del lenguaje, current.

### Globales: cuándo sí y cuándo no

`npm install -g paquete` lo instala como CLI accesible desde cualquier lugar.

**Sí instala global:**

- CLIs que usas desde varios proyectos: `serve`, `http-server`, `nodemon`, `vite` (a veces), `typescript`.

**NO instala global:**

- Librerías que importás en código (react, axios, express). Esas van en `package.json` del proyecto.

**Antipatrón:** instalar React global. React NO es una CLI; tu proyecto debe declararlo en su `package.json` con la versión que necesita.

---

## 9. VS Code

### Por qué VS Code

- Gratis, open source (en su parte fundamental).
- Multiplataforma (Linux, Mac, Windows).
- Ecosistema gigante de extensiones.
- Buen rendimiento.
- Git integrado.
- Terminal integrada.
- Soporte excelente para JS, TS, Python, y prácticamente cualquier lenguaje.

### Estructura básica

- **Activity Bar** (izquierda, vertical): archivos, búsqueda, git, debug, extensiones.
- **Side Bar:** contenido del item seleccionado en Activity Bar.
- **Editor:** donde escribes código (puede dividirse en columnas).
- **Panel** (abajo): terminal, output, problems, debug console.
- **Status Bar** (abajo del todo): rama de Git, errores, encoding, etc.

### Atajos esenciales (memorízalos)

| Atajo | Qué hace |
|---|---|
| `Ctrl + P` | Abrir archivo por nombre (RÁPIDO) |
| `Ctrl + Shift + P` | Paleta de comandos (haz CUALQUIER cosa) |
| `Ctrl + B` | Toggle sidebar |
| `Ctrl + ñ` o `` Ctrl + ` `` | Toggle terminal |
| `Ctrl + Shift + N` | Nueva ventana |
| `Ctrl + Shift + T` | Reabrir última pestaña cerrada |
| `Ctrl + D` | Seleccionar siguiente coincidencia (multi-cursor) |
| `Alt + click` | Agregar cursor en posición |
| `Ctrl + Alt + flecha arriba/abajo` | Agregar cursor arriba/abajo |
| `Alt + flecha arriba/abajo` | Mover línea |
| `Shift + Alt + flecha arriba/abajo` | Duplicar línea |
| `Ctrl + /` | Comentar/descomentar |
| `Ctrl + Shift + K` | Borrar línea |
| `F2` | Renombrar variable/función (refactor) |
| `F12` | Ir a definición |
| `Alt + F12` | Ver definición inline (peek) |
| `Ctrl + Shift + F` | Buscar en todo el proyecto |
| `Ctrl + Tab` | Cambiar entre archivos abiertos |

### Settings esenciales

Abrí `Ctrl + ,` (Settings) y configura:

- **Format On Save** → ON
- **Default Formatter** → Prettier (después de instalarlo)
- **Tab Size** → 2
- **Files: Auto Save** → `onFocusChange`
- **Editor: Word Wrap** → on
- **Editor: Bracket Pair Colorization** → ON

### Extensiones mínimas (no llenes de basura)

Para Fase 1 (vanilla JS):

- **Live Server** (Ritwick Dey) — servidor con reload automático.
- **Prettier - Code formatter** — formateo automático.
- **ESLint** — linter para JS.
- **GitLens** — supercarga el panel de Git.
- **Error Lens** — muestra errores inline en la línea.

Para más adelante:

- React: ya viene soporte de JSX/TSX nativo.
- Tailwind CSS IntelliSense (cuando uses Tailwind).
- Path Intellisense, Auto Rename Tag (HTML), etc.

**No instales packs gigantes ("Full Stack Pack").** Cada extensión añade lentitud al startup.

### Terminal integrada

`` Ctrl + ` `` abre/cierra una terminal dentro de VS Code. Útil porque:

- Está en el mismo directorio que tu proyecto.
- No tienes que cambiar de ventana.
- Puedes tener varias divididas.

---

## 10. Servidores de desarrollo

### Por qué necesitas un servidor local

HTML/CSS/JS abiertos como archivo (`file://`) tienen restricciones:

- No funciona `fetch()` a otros archivos locales (CORS).
- No funcionan ciertas APIs del navegador.
- No simula bien cómo va a comportarse en producción.

Un servidor local sirve tus archivos por `http://localhost:puerto`, eliminando esas restricciones.

### Opciones

| Herramienta | Tipo | Uso |
|---|---|---|
| **Live Server (VS Code)** | Extensión | Click derecho → "Open with Live Server". Reload automático al guardar. Ideal para Fase 1. |
| **`serve`** | CLI (npm) | `serve .` en cualquier carpeta. Levanta servidor estático. |
| **`http-server`** | CLI (npm) | Similar a `serve`. Alternativa equivalente. |
| **Vite, webpack-dev-server** | Bundlers | Para proyectos React/SPA modernos. Hot Module Replacement. |
| **Node con Express** | Backend | Cuando tengas un backend propio. |

### Patrón mental: el servidor "secuestra" la terminal

Cuando levantas un servidor en una terminal:

1. La terminal queda ocupada — no te devuelve el prompt.
2. El servidor está vivo MIENTRAS la terminal esté abierta y el proceso corriendo.
3. **`Ctrl + C` mata el servidor** y te devuelve el prompt.

Por eso los devs trabajan con **varias terminales/pestañas a la vez:**

- Terminal 1: servidor corriendo.
- Terminal 2: comandos de Git, instalaciones, etc.
- Terminal 3: tests, etc.

En Konsole: `Ctrl + Shift + T` para nueva pestaña. En VS Code: botón `+` en el panel de terminal.

### localhost y puertos

- `localhost` = tu propia máquina (alias de `127.0.0.1`).
- Un **puerto** es como un canal de comunicación. Los servidores escuchan en uno específico.
- Puertos comunes en desarrollo:
  - `3000` → React, Next.js, serve, http-server
  - `4173` → Vite preview
  - `5173` → Vite dev
  - `8080` → http-server, varios
  - `8000` → Python's SimpleHTTPServer

Si un puerto está ocupado, el servidor te dice "Port 3000 in use" y suele intentar el siguiente.

---

## 11. Multi-monitor y KDE Plasma

### Atajo rápido

`Super + P` (Super = tecla Windows) → menú flotante para:

- Solo PC.
- Duplicar.
- Extender. ← lo que quieres para programar.
- Solo externa.

### Configuración detallada

**Configuración del sistema → Pantalla y monitor:**

- Arrastrar rectángulos para definir posición física de los monitores.
- Resolución, escala, refresh rate por monitor.
- Definir monitor primario.

### Escala por monitor (HiDPI mixto)

Si tu portátil tiene pantalla densa (1920x1200 en 14") y tu monitor externo es 1080p en 24"+, usa escalas distintas:

- Portátil: 125% o 150%.
- Monitor externo: 100%.

Wayland (lo que usa CachyOS por default) maneja esto bien por monitor.

### Atajos útiles de KDE

| Atajo | Qué hace |
|---|---|
| `Super + P` | Selector de modo multi-monitor |
| `Super + Flecha izq/der` | Snap a mitad izquierda/derecha |
| `Super + Flecha arriba` | Maximizar |
| `Super + Flecha abajo` | Minimizar/restaurar |
| `Super + Shift + Flecha izq/der` | Mover ventana al otro monitor |
| `Super + Tab` | Cambiar entre ventanas |
| `Ctrl + Alt + T` | Abrir terminal (a veces, depende de config) |

### Setup productivo recomendado

- **Monitor externo (grande, primario):** VS Code en pantalla completa o split con terminal.
- **Portátil (secundario):** navegador con la app + DevTools, documentación, Slack/Discord.

---

## 12. Patrones mentales y filosofía de senior

### Convenciones invisibles que valen oro

1. **Todo en inglés en código y commits.** Variables, funciones, mensajes de commit, comentarios. Es el estándar de la industria y permite colaborar globalmente. Errores en Google también en inglés (resultados 10x mejores).

2. **Indentación consistente.** Define tu estilo (2 espacios para JS, 4 para Python) y respétalo. Prettier lo automatiza.

3. **Commits frecuentes y pequeños.** Cada commit = una unidad lógica de cambio. NO `commit -m "varios fixes"`. Mejor 5 commits pequeños que 1 gigante.

4. **README serio en cada repo.** Qué hace, cómo correrlo, screenshot. La presentación de tu trabajo a un reclutador empieza ahí.

5. **`.gitignore` desde el primer commit.** Si después agregas algo grande a .gitignore que ya estaba commiteado, queda en historial para siempre.

### Cómo aprende un senior vs un junior

**Junior:**
- Busca tutoriales antes de leer docs.
- Copia código sin entender.
- Pregunta sin haber intentado.
- Quiere aprenderlo "todo".

**Senior:**
- Va a la documentación oficial primero.
- Copia código y lo entiende línea por línea antes de usarlo.
- Investiga 30 min antes de preguntar; cuando pregunta, lo hace con contexto.
- Domina pocas herramientas profundamente, luego suma.

### La regla de las 3 capas (debugging)

Cuando algo no funciona, los problemas suelen estar en una de estas capas, en este orden:

1. **Tu código** (lo que escribiste hace 5 min). ← 70% de los casos.
2. **Tu config** (settings, env vars, plugins). ← 25%.
3. **El sistema/herramienta** (bug real). ← 5%.

Los juniors asumen lo último y se quejan; los seniors empiezan por lo primero.

### Cuándo pedir ayuda

Regla de 45 minutos:

- Si llevas 45 min trabado en un problema, **escribe la pregunta bien y pide ayuda.**
- Una pregunta bien escrita incluye: qué intentaste, qué esperabas, qué pasa en realidad, mensajes de error exactos, qué entorno usas.
- A veces escribir la pregunta te hace darte cuenta de la respuesta — esto se llama "Rubber Duck Debugging".

### Antipatrones a evitar

- **Tutorial hell** → ver cursos sin programar.
- **Stack soup** → querer aprender 10 tecnologías a la vez.
- **Premature optimization** → optimizar antes de tener algo funcionando.
- **Configuración eterna** → pasar días afinando el shell/editor en vez de programar.
- **Magia copy-paste** → meter código sin entenderlo.

### Filosofía Unix

Cada herramienta hace **una cosa, y la hace bien**. Las conectas con pipes (`|`) para componer.

```bash
# Encontrar todos los .js modificados en la última semana,
# contar líneas, ordenar de mayor a menor
find . -name "*.js" -mtime -7 | xargs wc -l | sort -nr | head
```

Esta mentalidad de componer herramientas pequeñas es clave en backend, scripting y devops.

---

## 13. Troubleshooting: cómo pensar cuando algo falla

### Pasos sistemáticos

1. **Lee el mensaje de error completo.** Literal, palabra por palabra. Los errores de Linux suelen ser brutalmente honestos.
2. **Reproduce el error.** ¿Siempre falla? ¿Aleatorio? ¿Solo en cierta carpeta?
3. **Aísla.** ¿En qué momento empezó? ¿Qué cambió antes?
4. **Verifica lo obvio.**
   - ¿Está conectado el cable?
   - ¿Está prendido?
   - ¿Existe el archivo?
   - ¿Tengo permisos?
   - ¿Hay internet?
5. **Busca el error EXACTO en Google.** Copia el mensaje literal, sin tus nombres de archivo.
6. **Stack Overflow primero, IA después.** En problemas raros, la IA inventa soluciones; SO tiene respuestas verificadas por humanos.

### Errores comunes y cómo leerlos

| Error | Significado | Solución típica |
|---|---|---|
| `command not found` | Programa no instalado o no está en PATH | Instálalo o agrega su ruta al PATH |
| `permission denied` | No tienes permisos | Verifica con `ls -la`, usa `sudo` con cuidado |
| `no such file or directory` | El archivo/ruta no existe LITERAL | Verifica el path con `ls`, typos comunes |
| `connection refused` | Intentaste conectar a algo que no está escuchando | Verifica que el servicio esté corriendo |
| `port already in use` | Otro proceso usa ese puerto | Mátalo o usa otro puerto |
| `disk full` | Disco lleno | `df -h` para ver, limpia |

### Diagnóstico de shell (cuando los comandos no funcionan)

```bash
# ¿Qué shell estoy corriendo?
echo $0           # ej: zsh
echo $SHELL       # variable seteada al login (puede estar desactualizada)

# ¿Dónde está mi PATH?
echo $PATH

# ¿Dónde está un comando?
which mi-comando
type mi-comando

# Recargar config
source ~/.zshrc
```

### Diagnóstico de Git

```bash
# Estado completo del repo
git status

# Historial visual
git log --oneline --graph --all -20

# Configuración actual
git config --list

# Verificar remotes
git remote -v

# Probar conexión SSH a GitHub
ssh -T git@github.com
```

### Diagnóstico de hardware (multi-monitor, etc.)

```bash
# Monitores conectados detectados por el kernel
ls /sys/class/drm/
for p in /sys/class/drm/*/status; do echo "$p: $(cat $p)"; done

# Output detallado (KDE)
kscreen-doctor --outputs
```

---

## 14. Glosario rápido

| Término | Definición |
|---|---|
| **CLI** | Command Line Interface — programa que se usa por terminal. |
| **GUI** | Graphical User Interface — programa con ventanas e iconos. |
| **TUI** | Text User Interface — interfaz "gráfica" pero en texto (ej: htop, vim). |
| **IDE** | Integrated Development Environment — editor potente (VS Code, IntelliJ). |
| **kernel** | Núcleo del sistema operativo. Habla con el hardware. |
| **distro** | Distribución de Linux: kernel + programas empaquetados. |
| **shell** | Programa que interpreta tus comandos (bash, zsh, fish). |
| **terminal** | App gráfica donde corre el shell (Konsole, Alacritty). |
| **PATH** | Variable con lista de directorios donde el shell busca comandos. |
| **alias** | Atajo para un comando largo. |
| **package manager** | Programa que instala/desinstala software (pacman, apt, npm). |
| **repo** | Repositorio: carpeta con historial de Git. |
| **commit** | Snapshot de cambios en Git. |
| **branch** | Rama de desarrollo paralela. |
| **remote** | Repo alojado en otro lugar (GitHub, etc.). |
| **SSH** | Protocolo de conexión segura encriptada. |
| **HTTP/HTTPS** | Protocolo de web (S = encriptado). |
| **localhost** | Tu propia máquina (127.0.0.1). |
| **puerto** | Canal de comunicación de red (3000, 8080, etc.). |
| **API** | Application Programming Interface — interfaz para que programas hablen entre sí. |
| **REST** | Estilo de diseño de APIs HTTP (GET, POST, PUT, DELETE). |
| **JSON** | Formato de datos texto que JS entiende nativamente. |
| **DOM** | Document Object Model — representación en memoria del HTML. |
| **fetch** | API nativa del navegador para hacer requests HTTP. |
| **async/await** | Sintaxis de JS para código asíncrono. |
| **promise** | Objeto de JS que representa un valor que llegará en el futuro. |
| **callback** | Función que pasas como argumento para que otra la ejecute. |
| **LTS** | Long Term Support — versión con soporte extendido. |
| **CI/CD** | Continuous Integration / Continuous Deployment — automatizar tests y deploys. |
| **deploy** | Subir tu app a un servidor accesible al público. |
| **rolling release** | Modelo de distro con actualizaciones continuas (Arch). |
| **dotfiles** | Archivos de configuración ocultos (.zshrc, .gitconfig). |
| **environment variable** | Variable que existe en tu sesión de shell (ej: $PATH, $HOME). |
| **stdout / stderr** | Salidas estándar (`>`) y de errores (`2>`). |
| **process** | Programa corriendo en memoria. |
| **daemon** | Proceso que corre en segundo plano (servicios). |

---

## Mi setup actual (snapshot mayo 2026)

- **OS:** CachyOS x86_64
- **DE:** KDE Plasma 6.6.5
- **Kernel:** Linux 7.0.9-1-cachyos
- **Shell:** zsh + Oh My Zsh (tema: robbyrussell)
- **Plugins zsh:** git, node, npm, zsh-autosuggestions, zsh-syntax-highlighting
- **Editor:** VS Code
- **Terminal:** Konsole (perfil personalizado apuntando a `/usr/bin/zsh`)
- **Node:** v22 LTS gestionado por fnm
- **npm globals:** serve, http-server
- **Git:** configurado con SSH a GitHub (usuario: jeanjso)

---

## Próximos pasos / pendientes

- [ ] Configurar extensiones de VS Code (Live Server, Prettier, ESLint, GitLens, Error Lens).
- [ ] Resolver detección del segundo monitor.
- [ ] Empezar Issue #1 de music-finder: setup HTML semántico.
- [ ] Conventional Commits desde el primer commit del proyecto.

---

*Estos apuntes son míos y se actualizan a medida que aprendo. Cuando algo no me cuadre, vuelvo aquí y lo reviso o lo amplío.*
