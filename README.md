# Bases de Datos — Contenidos de la asignatura

Este repositorio contiene **todos los apuntes de la asignatura de Bases de Datos** del Grado en Ingeniería Informática en formato Markdown: temas de teoría, guías de repaso, guiones de laboratorio, enunciados de ejercicios y criterios de evaluación.

No es un repositorio para leer y ya. Está pensado para que **cada estudiante tenga su propia copia (un *fork*)**, la abra en local con un cliente de Markdown (recomendamos **Obsidian**) y la use como su cuaderno de la asignatura: anotando la teoría, escribiendo las soluciones de los laboratorios directamente en los guiones y guardando todo en GitHub.

> **Resumen en una línea:** haz un fork → clónalo → ábrelo como *vault* en Obsidian → anota y resuelve → `commit` + `push` → sincroniza con el repositorio base cuando actualicemos contenidos.

---

## Índice

1. [Qué hay en el repositorio](#1-qué-hay-en-el-repositorio)
2. [Por qué trabajamos así](#2-por-qué-trabajamos-así)
3. [Requisitos previos](#3-requisitos-previos)
4. [Paso 1 — Crear tu fork](#4-paso-1--crear-tu-fork)
5. [Paso 2 — Clonar tu fork en tu ordenador](#5-paso-2--clonar-tu-fork-en-tu-ordenador)
6. [Paso 3 — Abrir el repositorio como vault de Obsidian](#6-paso-3--abrir-el-repositorio-como-vault-de-obsidian)
7. [Alternativa — Usar VS Code](#7-alternativa--usar-vs-code)
8. [Paso 4 — Tomar notas y resolver los laboratorios](#8-paso-4--tomar-notas-y-resolver-los-laboratorios)
9. [Paso 5 — Guardar y subir tus cambios a GitHub](#9-paso-5--guardar-y-subir-tus-cambios-a-github)
10. [Paso 6 — Actualizar tu fork cuando cambiemos los contenidos](#10-paso-6--actualizar-tu-fork-cuando-cambiemos-los-contenidos)
11. [Problemas frecuentes](#11-problemas-frecuentes)
12. [Chuleta de comandos](#12-chuleta-de-comandos)

---

## 1. Qué hay en el repositorio

```
bd-contenidos/
├── Índice de Contenidos.md          ← punto de entrada: enlaces a todos los temas y labs
├── Planificacion Prevista Curso.md  ← calendario orientativo de la asignatura
├── teoria/                          ← temas de teoría (T01 … T07)
│   ├── guias/                       ← guías de repaso por tema (GR01 … GR07)
│   └── anexos/                      ← ejemplos completos y material complementario
├── labs/                            ← guiones de laboratorio (LAB01 … LAB14)
│   ├── guias/                       ← guía de repaso de SQL
│   └── anexos/                      ← instalación y notación de BigER
├── ejercicios/                      ← enunciados de ejercicios (ER, teoría, Sakila)
├── evaluacion/                      ← criterios de evaluación de SQL y de diseño
├── sql/                             ← scripts SQL de apoyo
└── imgs/                            ← imágenes usadas en los apuntes
```

Empieza siempre por `Índice de Contenidos.md`: desde ahí se llega a todo.

---

## 2. Por qué trabajamos así

Explicamos las razones porque entender el *porqué* hace que el *cómo* se recuerde mejor:

- **Markdown en lugar de PDF.** Un PDF se lee; un fichero Markdown se *edita*. Queremos que subrayes, añadas ejemplos propios, apuntes tus dudas y escribas tus soluciones **dentro del propio material**, no en un documento aparte que luego se pierde.
- **Un fork por estudiante.** Un fork es tu copia personal del repositorio en tu cuenta de GitHub. Puedes modificarla libremente sin afectar al material original, y sigue "conectada" al repositorio base para poder traerte nuestras actualizaciones.
- **Git como mochila.** Cada `commit` es una foto de tus apuntes en un momento dado. Si rompes algo, vuelves atrás. Si cambias de ordenador, haces `clone` y lo tienes todo. Además, en la asignatura de Bases de Datos es una buena excusa para practicar Git, que vais a usar el resto de la carrera y de vuestra vida profesional.
- **Obsidian como cliente.** Es gratuito, funciona sobre una carpeta de ficheros Markdown normales (no te "secuestra" los datos), renderiza las imágenes y los enlaces entre notas del repositorio (algunos apuntes usan la sintaxis `[[enlace]]` propia de Obsidian, que GitHub no muestra bien) y tiene un buscador excelente. VS Code vale igualmente; ver la [sección 7](#7-alternativa--usar-vs-code).

---

## 3. Requisitos previos

| Necesitas | Para qué | Dónde |
|---|---|---|
| Cuenta de GitHub | Alojar tu fork | https://github.com/signup (usa tu correo de la UEx si quieres solicitar el [GitHub Student Developer Pack](https://education.github.com/pack)) |
| Git instalado | Clonar, guardar y subir cambios | https://git-scm.com/downloads (en macOS viene con las *Command Line Tools*; en Linux, `sudo apt install git`) |
| Obsidian | Leer y editar los apuntes | https://obsidian.md/download |
| (Opcional) GitHub Desktop | Si prefieres botones a comandos | https://desktop.github.com |

Comprueba que Git funciona abriendo un terminal y escribiendo:

```bash
git --version
```

La primera vez que uses Git en un ordenador, identifícate (estos datos aparecerán en tus commits):

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tu-correo@alumnos.unex.es"
```

---

## 4. Paso 1 — Crear tu fork

1. Entra en GitHub con tu cuenta.
2. Ve a la página del repositorio base: **https://github.com/bd-uex/bd-contenidos**
3. Pulsa el botón **Fork** (arriba a la derecha).
4. En el formulario que aparece, deja el propietario (tu usuario) y el nombre `bd-contenidos`. **Marca** la casilla *"Copy the main branch only"* si aparece desmarcada (así te llevas los contenidos de este curso: rama `main`).
5. Pulsa **Create fork**.

Al terminar estarás en `https://github.com/TU-USUARIO/bd-contenidos`. Fíjate en el texto bajo el título: *"forked from bd-uex/bd-contenidos"*. Ese vínculo es el que usaremos más adelante para sincronizar.

> **¿Fork o clone?** Un *fork* es una copia en GitHub (en tu cuenta). Un *clone* es una copia en tu ordenador. Necesitas las dos: el fork para guardar tu trabajo en la nube, el clone para trabajar en local.

---

## 5. Paso 2 — Clonar tu fork en tu ordenador

Elige una carpeta donde guardar las cosas de la carrera (por ejemplo, `~/uex/gii/` o `Documentos/UEx/GII/`) y, desde un terminal en esa carpeta:

```bash
git clone https://github.com/TU-USUARIO/bd-contenidos.git
cd bd-contenidos
```

> **Importante:** clona **tu fork** (`TU-USUARIO/bd-contenidos`), no el repositorio base (`bd-uex/bd-contenidos`). Si clonas el base, podrás leer pero no subir cambios, porque no tienes permisos de escritura en él.

Si prefieres SSH (evita escribir la contraseña/token cada vez), primero [configura una clave SSH en GitHub](https://docs.github.com/es/authentication/connecting-to-github-with-ssh) y clona con:

```bash
git clone git@github.com:TU-USUARIO/bd-contenidos.git
```

**Con GitHub Desktop:** *File → Clone repository*, elige `TU-USUARIO/bd-contenidos` de la lista y una carpeta de destino. Cuando te pregunte *"How are you planning to use this fork?"*, responde **"For my own purposes"**: así los `push` van a tu fork y no intenta proponer cambios al repositorio base.

Comprueba que todo ha ido bien:

```bash
git remote -v
```

Debe mostrar `origin` apuntando a **tu** usuario. `origin` es el nombre con el que Git se refiere al repositorio remoto del que has clonado, es decir, tu fork.

---

## 6. Paso 3 — Abrir el repositorio como vault de Obsidian

En Obsidian, un *vault* es simplemente una carpeta con ficheros Markdown. No hay que "importar" nada: le dices a Obsidian que la carpeta del repositorio **es** el vault.

1. Abre Obsidian.
2. En la pantalla inicial (o desde el icono del vault, abajo a la izquierda, *"Open another vault"*), pulsa **Open folder as vault**.
3. Selecciona la carpeta `bd-contenidos` que acabas de clonar.
4. Obsidian te preguntará si confías en el autor del vault; acepta.

Verás el árbol de carpetas a la izquierda. Abre `Índice de Contenidos.md` y empieza a navegar.

### Ajustes recomendados (5 minutos que merecen la pena)

Abre *Settings* (icono de engranaje) y revisa:

- **Files and links → Default location for new attachments:** elige *"In the folder specified below"* y escribe `imgs`. Así, si pegas una captura de pantalla en tus notas, irá a la misma carpeta que las imágenes de la asignatura y no se te llenará la raíz de ficheros sueltos.
- **Files and links → Use [[Wikilinks]]:** puedes dejarlo activado (es el estilo que usa parte del material) o desactivarlo si prefieres enlaces Markdown estándar; ambos funcionan.
- **Editor → Show line numbers:** útil cuando trabajes con bloques de código SQL.
- **Appearance:** elige el tema que quieras; es tuyo.

### Sobre la carpeta `.obsidian/`

Al abrir el vault, Obsidian crea una carpeta oculta `.obsidian/` con tu configuración (tema, plugins, disposición de paneles). Esa carpeta **ya está en el `.gitignore`** del repositorio, así que Git la ignora: tus ajustes personales no se suben a GitHub ni entran en conflicto con nadie. Lo mismo pasa con `.trash/` (papelera de Obsidian). Por eso puedes personalizar Obsidian todo lo que quieras sin miedo.

### Plugins útiles (opcionales)

En *Settings → Community plugins → Browse*:

- **Git** (de Vinzent): permite hacer `commit` y `push` desde dentro de Obsidian, e incluso automatizarlos cada X minutos. Muy cómodo una vez que entiendas lo que hace Git por debajo (por eso te recomendamos aprender primero los comandos de la [sección 9](#9-paso-5--guardar-y-subir-tus-cambios-a-github)).
- **Advanced Tables:** permite simplificar la edición de tablas en markdown.

---

## 7. Alternativa — Usar VS Code

Si ya vives en VS Code (por ejemplo, porque lo usas en los laboratorios de BigER), puedes usarlo también para los apuntes:

1. Abre la carpeta: `code bd-contenidos` (o *File → Open Folder*).
2. Abre cualquier `.md` y pulsa `Ctrl+Shift+V` (`Cmd+Shift+V` en macOS) para ver la vista previa, o `Ctrl+K V` para tenerla al lado del editor.
3. Extensiones recomendadas: **Markdown All in One** (atajos, tabla de contenidos, formato de tablas) y **Markdown Preview Enhanced**. Para los diagramas ER, la extensión **bigER** que se explica en `labs/anexos/AL09.1`.

VS Code incluye una vista de *Source Control* (icono de las ramas, a la izquierda) desde la que puedes hacer `commit`, `push` y `pull` con botones. La limitación respecto a Obsidian es que los enlaces tipo `[[nota]]` no se resuelven en la vista previa por defecto (la extensión *Foam* lo soluciona).

---

## 8. Paso 4 — Tomar notas y resolver los laboratorios

### Anotar la teoría

Escribe directamente en los ficheros de `teoria/`. Algunas ideas que funcionan bien:

- Añade tus propias explicaciones justo debajo del párrafo que te ha costado entender, con un formato reconocible, por ejemplo un bloque de cita: `> 📝 Mi nota: ...`
- Apunta las dudas para preguntar en clase con una etiqueta que luego puedas buscar: `#duda`. En Obsidian, al pulsar sobre la etiqueta verás todas tus dudas pendientes de un vistazo.
- Crea tus propias notas de resumen. Te sugerimos la carpeta `mis-notas/` en la raíz (créala tú): todo lo que esté ahí es exclusivamente tuyo y **nunca va a entrar en conflicto** con nuestras actualizaciones, porque nosotros no tocamos esa carpeta.

### Resolver los laboratorios

Los guiones de `labs/` tienen, después de cada enunciado, un bloque preparado para tu solución:

````markdown
### Ejercicio 1 - Repaso SELECT

Escribe una consulta que ...

Solución:
```sql

```

Resultado:
| what_where    | bill_ratio |
| ------------- | ---------- |
| GENTOO biscoe | 3.6        |
````

Escribe tu consulta **dentro del bloque `sql` vacío**. El apartado *Resultado* te muestra lo que debe salir para que puedas comprobarlo tú mismo. Cuando lo tengas, guarda el fichero y haz commit (siguiente paso). Con esto consigues tres cosas: el enunciado y tu solución quedan juntos, tienes un historial de cómo has ido avanzando, y al final del curso tienes un cuaderno completo de SQL para repasar antes del examen.

Para los laboratorios de diseño (LAB09–LAB13, con BigER), guarda tus ficheros `.erd` en la misma carpeta `labs/` o en `mis-notas/`, y enlázalos desde el guión.

### Dos reglas para evitarte problemas

1. **No renombres ni muevas los ficheros originales.** Si renombras `LAB05 - JOIN.md` y nosotros después corregimos una errata en ese fichero, Git no sabrá que son el mismo y tendrás un conflicto innecesario. Edita dentro, añade lo que quieras, pero deja los nombres como están.
2. **Guarda con frecuencia y sube al menos al final de cada sesión.** Un commit por laboratorio o por sesión de estudio es un buen ritmo.

---

## 9. Paso 5 — Guardar y subir tus cambios a GitHub

Git funciona en dos tiempos: primero guardas una "foto" en tu ordenador (`commit`) y después la subes a GitHub (`push`). Desde un terminal, dentro de la carpeta `bd-contenidos`:

```bash
# 1. ¿Qué he cambiado? (siempre es buena idea mirar antes)
git status

# 2. Marcar los ficheros que quieres incluir en la foto
git add .                         # todos los cambios
# o, si prefieres ser selectivo:
git add "labs/LAB02 - select basico.md"

# 3. Hacer la foto con un mensaje que describa el cambio
git commit -m "LAB02: ejercicios 1 a 4 resueltos"

# 4. Subirla a tu fork en GitHub
git push
```

> Sobre los mensajes de commit: escribe qué has hecho, no "cambios" o "asdf". Dentro de tres meses agradecerás poder leer `git log` y entender tu propio historial.

La primera vez que hagas `push` por HTTPS, GitHub te pedirá usuario y contraseña. **La contraseña no vale:** hay que usar un *Personal Access Token*. Créalo en *GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)* con el permiso `repo`, y pégalo cuando te pida la contraseña. Para no tener que hacerlo cada vez, instala [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager) (viene incluido con Git para Windows) o usa SSH.

**Con GitHub Desktop:** escribe el mensaje en la caja de abajo a la izquierda, pulsa *Commit to main* y luego *Push origin*.

**Con el plugin Git de Obsidian:** `Ctrl+P` → *"Git: Commit all changes"* y luego *"Git: Push"*.

Verifica que ha funcionado entrando en `https://github.com/TU-USUARIO/bd-contenidos`: deberías ver tu commit y tus cambios.

---

## 10. Paso 6 — Actualizar tu fork cuando cambiemos los contenidos

Durante el curso corregiremos erratas, añadiremos ejemplos y publicaremos los guiones de los laboratorios más avanzados. Tu fork **no se actualiza solo**: tienes que traerte los cambios explícitamente. Hay dos formas.

### Opción A — Desde la web de GitHub (la más sencilla)

1. Entra en tu fork: `https://github.com/TU-USUARIO/bd-contenidos`.
2. Si hay novedades verás un aviso del tipo *"This branch is N commits behind bd-uex/bd-contenidos:main"*.
3. Pulsa **Sync fork → Update branch**.
4. Ahora tu fork en GitHub está al día, pero tu ordenador aún no. Bájate los cambios:

```bash
git pull
```

Si GitHub te muestra *"This branch has conflicts that must be resolved"*, es porque tú y nosotros hemos tocado las mismas líneas de un mismo fichero. En ese caso usa la Opción B, que te permite resolverlo en local.

### Opción B — Desde el terminal (más control)

La primera vez, dile a Git dónde está el repositorio base. Por convención se le llama `upstream` ("aguas arriba"):

```bash
git remote add upstream https://github.com/bd-uex/bd-contenidos.git
git remote -v      # ahora deberías ver origin (tu fork) y upstream (el base)
```

A partir de ahí, cada vez que quieras actualizarte:

```bash
git add . && git commit -m "Guardo mi trabajo antes de actualizar"   # si tienes cambios sin guardar
git fetch upstream            # descarga las novedades del repositorio base (sin aplicarlas aún)
git merge upstream/main       # las mezcla con tu trabajo
git push                      # sube el resultado a tu fork
```

> **¿Por qué `fetch` + `merge` y no `pull`?** `git pull upstream main` hace las dos cosas a la vez y funciona igual. Las separamos para que veas que son dos pasos distintos: primero *descargar* y luego *integrar*. Cuando entiendas la diferencia, usa `pull` sin problema.

### Si aparece un conflicto

Git te avisará con algo como `CONFLICT (content): Merge conflict in labs/LAB05 - JOIN.md`. No es un error grave: significa que ese fichero tiene cambios tuyos y nuestros en la misma zona y Git no quiere decidir por ti. Abre el fichero (Obsidian o VS Code lo resaltan) y verás bloques así:

```
<<<<<<< HEAD
   (tu versión)
=======
   (nuestra versión)
>>>>>>> upstream/main
```

Edita el fichero dejando el contenido que quieras (normalmente ambas partes: nuestra corrección y tu solución), borra las líneas con `<<<<<<<`, `=======` y `>>>>>>>`, y termina la operación:

```bash
git add "labs/LAB05 - JOIN.md"
git commit -m "Resuelto conflicto al actualizar desde upstream"
git push
```

VS Code muestra botones *"Accept Current / Incoming / Both Changes"* encima de cada conflicto que hacen esto mismo con un clic. Los conflictos son raros si sigues las dos reglas de la [sección 8](#dos-reglas-para-evitarte-problemas), porque casi siempre editaréis zonas distintas del fichero.

---

## 11. Problemas frecuentes

**`git push` me dice "permission denied" o "403".**
Casi seguro has clonado el repositorio base en lugar de tu fork. Comprueba con `git remote -v`; si `origin` apunta a `bd-uex`, cámbialo: `git remote set-url origin https://github.com/TU-USUARIO/bd-contenidos.git`.

**GitHub me pide contraseña y no la acepta.**
Necesitas un *Personal Access Token* o SSH; ver [sección 9](#9-paso-5--guardar-y-subir-tus-cambios-a-github).

**Obsidian no muestra una imagen que en GitHub sí se ve (o al revés).**
Las imágenes están en `imgs/`. Obsidian resuelve `![[imagen.png]]` buscando por nombre en todo el vault; GitHub solo entiende rutas relativas `![](../imgs/imagen.png)`. Si nos encuentras un caso que falla en Obsidian, avísanos (o mejor: ¡abre un *issue* en el repositorio base!).

**He borrado algo sin querer.**
Si aún no has hecho commit: `git checkout -- "ruta/del/fichero.md"` recupera la última versión guardada. Si ya lo has hecho: `git log` para ver el historial y `git checkout <hash-del-commit> -- "ruta/del/fichero.md"` para traerte la versión anterior de ese fichero.

**Quiero trabajar en dos ordenadores.**
Clona tu fork en ambos. Antes de empezar en cualquiera: `git pull`. Al terminar: `commit` + `push`. Si olvidas el `pull` y editas en los dos, tendrás un conflicto pequeño que se resuelve como en la [sección 10](#si-aparece-un-conflicto).

**¿Puedo enviaros correcciones al material?**
Sí, y os lo agradecemos. Si detectas una errata, haz el cambio en tu fork y abre un *Pull Request* hacia `bd-uex/bd-contenidos` desde la pestaña *Contribute* de tu fork. Eso sí: manda solo la corrección, no tus soluciones a los ejercicios.

---

## 12. Chuleta de comandos

```bash
# --- Una sola vez ---
git clone https://github.com/TU-USUARIO/bd-contenidos.git
cd bd-contenidos
git remote add upstream https://github.com/bd-uex/bd-contenidos.git

# --- Cada sesión de trabajo ---
git pull                                  # empezar con lo último de tu fork
#   ... estudiar, anotar, resolver ...
git add .
git commit -m "Descripción de lo que has hecho"
git push

# --- Cuando avisemos de que hay contenidos nuevos ---
git fetch upstream
git merge upstream/main
git push

# --- Para orientarte ---
git status        # qué has cambiado
git log --oneline # historial resumido
git remote -v     # a dónde apuntan origin y upstream
```

---

*Asignatura de Bases de Datos — Grado en Ingeniería Informática, Universidad de Extremadura.*
