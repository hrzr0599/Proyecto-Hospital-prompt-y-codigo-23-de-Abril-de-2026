Aquí tienes la guía completa y detallada:

---

# 🛠️ Guía Completa: Node.js, npm, Firebase CLI y Flutter en Windows

---

## 📌 PARTE 1 — ¿Qué software necesitas?

Antes de instalar Firebase CLI para Flutter, necesitas tener instalado el siguiente software en orden:

```
1. Node.js  (incluye npm automáticamente)
2. Firebase CLI     (se instala con npm)
3. FlutterFire CLI  (se instala con dart pub global)
```

> 💡 npm es requerido para instalar y ejecutar Firebase CLI y FlutterFire CLI.

---

## 📌 PARTE 2 — Verificar si ya tienes Node.js y npm instalados

### Paso 1 — Abrir CMD o PowerShell
```
Presiona: Windows + R
Escribe:  cmd
Presiona: Enter
```

### Paso 2 — Verificar Node.js
```bash
node -v
```
**Resultado esperado si está instalado:**
```
v22.14.0   ← (la versión puede variar)
```

### Paso 3 — Verificar npm
```bash
npm -v
```
**Resultado esperado:**
```
10.9.2   ← (la versión puede variar)
```

> ✅ Si ambos comandos muestran versiones, **ya tienes npm instalado** y puedes saltar a la Parte 4.
> ❌ Si aparece `"no se reconoce como comando"`, necesitas instalar Node.js (sigue la Parte 3).

---

## 📌 PARTE 3 — Instalar Node.js y npm en Windows (paso a paso)

Node.js te ofrece dos versiones para descargar: la versión **LTS** (Long Term Support) y la versión **Current**. Si eres un usuario habitual que prefiere estabilidad, opta por la versión LTS.

### 🔽 PASO 1 — Descargar Node.js

```
Sitio oficial: https://nodejs.org/en/download/
```

Al visitar el sitio, la página detecta automáticamente que estás usando Windows y presenta las opciones de descarga. Haz clic en el botón correspondiente a la versión **LTS**, lo cual descargará un archivo `.msi` — por ejemplo: `node-v22.14.0-x64.msi`.

---

### ⚙️ PASO 2 — Ejecutar el instalador

Una vez descargado, ejecuta el instalador como **administrador** (haz clic derecho → "Ejecutar como administrador"). Este paso es crucial: Windows a veces bloquea o restringe ciertos archivos si no ejecutas el instalador con los permisos necesarios.

Sigue este flujo en el asistente:

```
┌─────────────────────────────────────────────┐
│  Welcome to Node.js Setup Wizard            │
│  → Click "Next"                             │
├─────────────────────────────────────────────┤
│  License Agreement                          │
│  → Marca "I accept" → Click "Next"          │
├─────────────────────────────────────────────┤
│  Destination Folder                         │
│  → Dejar ruta por defecto → Click "Next"    │
├─────────────────────────────────────────────┤
│  Custom Setup                               │
│  → ✅ Node.js runtime                       │
│  → ✅ npm package manager   ← IMPORTANTE    │
│  → ✅ Add to PATH            ← IMPORTANTE   │
│  → Click "Next"                             │
├─────────────────────────────────────────────┤
│  Tools for Native Modules                   │
│  → ✅ Marcar el checkbox (instala Chocolatey)│
│  → Click "Next" → Click "Install"           │
└─────────────────────────────────────────────┘
```

> Es importante garantizar que al menos las opciones **Node.js runtime**, **npm package manager** y **Add to PATH** estén seleccionadas.

---

### ✅ PASO 3 — Verificar la instalación

Para verificar si la instalación se realizó correctamente, abre el Símbolo del sistema y ejecuta `node --version` para verificar la versión de Node y `npm --version` para verificar la versión del gestor de paquetes.

```bash
node --version
# Resultado: v22.14.0

npm --version
# Resultado: 10.9.2
```

> 🔁 Si los comandos no son reconocidos, **reinicia tu PC** y vuelve a intentarlo (Windows necesita recargar las variables de entorno PATH).

---

## 📌 PARTE 4 — Instalar Firebase CLI de forma global con npm

En Windows, se recomienda ampliamente instalar Firebase CLI a través de npm con el comando `npm install -g firebase-tools`. La versión binaria standalone puede causar problemas.

### PASO 1 — Instalar firebase-tools globalmente

Abre **CMD como administrador** y ejecuta:

```bash
npm install -g firebase-tools
```

```
Resultado esperado:
added 629 packages in 45s
```

### PASO 2 — Verificar la instalación de Firebase CLI

```bash
firebase --version
```

```
Resultado esperado:
13.x.x
```

---

## 📌 PARTE 5 — Instalar FlutterFire CLI

Una vez instalado Firebase CLI, instala el FlutterFire CLI ejecutando el siguiente comando. Una vez instalado, el comando `flutterfire` estará disponible de forma global.

```bash
dart pub global activate flutterfire_cli
```

### Verificar FlutterFire CLI

```bash
flutterfire --version
```

> ⚠️ Si aparece un warning como: *"Warning: Pub installs executables into C:\Users\TU_USUARIO\AppData\Local\Pub\Cache\bin, which is not on your path"*, necesitas agregar esa ruta al PATH de Windows:

```
1. Busca "Variables de entorno" en el menú inicio
2. Click en "Variables de entorno"
3. En "Variables del sistema" → busca "Path" → Click "Editar"
4. Click "Nuevo" y agrega:
   C:\Users\TU_USUARIO\AppData\Local\Pub\Cache\bin
5. Click "Aceptar" en todo
6. Reinicia CMD
```

---

## 📌 PARTE 6 — Acceder a Firebase con tu Cuenta de Google

### PASO 1 — Comando de login

```bash
firebase login
```

### PASO 2 — Proceso de autenticación

```
┌──────────────────────────────────────────────────────┐
│ Terminal pregunta:                                    │
│ "Allow Firebase to collect CLI usage data?" → Y/N    │
│ → Escribe: Y  (recomendado)                          │
│                                                      │
│ Se abre automáticamente tu navegador:                │
│ → Selecciona tu cuenta de Google                     │
│ → Click "Permitir / Allow"                           │
│ → Cierra la ventana del navegador                    │
│                                                      │
│ En terminal aparece:                                 │
│ ✓  Success! Logged in as tu@correo.com               │
└──────────────────────────────────────────────────────┘
```

> Si iniciaste sesión con la cuenta incorrecta, ejecuta `firebase logout` y luego `firebase login` nuevamente. Asegúrate de usar la cuenta de Google vinculada a los proyectos de Firebase con los que deseas trabajar.

### PASO 3 — Verificar proyectos disponibles

```bash
firebase projects:list
```

Este comando listará los mismos proyectos que tienes disponibles en la consola de Firebase.

```
Resultado esperado:
┌──────────────────┬──────────────────┬──────────────┐
│ Project Display  │ Project ID       │ Platform     │
├──────────────────┼──────────────────┼──────────────┤
│ crudhospital     │ crudhospital-xxx │ [ANDROID]    │
└──────────────────┴──────────────────┴──────────────┘
```

---

## 📌 PARTE 7 — Configurar Firebase en tu proyecto Flutter

Una vez dentro de la carpeta de tu proyecto Flutter:

```bash
cd xflutterhernandezr0599/crudhospital
```

### Ejecutar FlutterFire configure

```bash
flutterfire configure
```

El comando `flutterfire configure` realiza lo siguiente: selecciona un proyecto Firebase basado en el archivo `.firebaserc` o desde la consola, solicita las plataformas (Android, iOS, macOS, Web) que deseas configurar, y genera un archivo `firebase_options.dart` en tu proyecto.

```
Flujo interactivo:
┌────────────────────────────────────────────────────┐
│ ? Select a Firebase project:                       │
│   > crudhospital (crudhospital-xxx)                │
│                                                    │
│ ? Which platforms should your configuration        │
│   support?                                         │
│   ✅ android                                       │
│   ☐  ios                                           │
│   ☐  web                                           │
│                                                    │
│ ✔ Firebase configuration file generated:          │
│   lib/firebase_options.dart                        │
└────────────────────────────────────────────────────┘
```

---

## 📊 Resumen de Comandos Clave

| Acción | Comando |
|---|---|
| Verificar Node.js | `node -v` |
| Verificar npm | `npm -v` |
| Instalar Firebase CLI | `npm install -g firebase-tools` |
| Verificar Firebase CLI | `firebase --version` |
| Instalar FlutterFire CLI | `dart pub global activate flutterfire_cli` |
| Login Firebase | `firebase login` |
| Logout Firebase | `firebase logout` |
| Ver proyectos | `firebase projects:list` |
| Configurar en Flutter | `flutterfire configure` |
| Instalar dependencias Flutter | `flutter pub get` |

---

> 🎯 **Orden correcto de instalación:**
> `Node.js (LTS)` → `npm install -g firebase-tools` → `dart pub global activate flutterfire_cli` → `firebase login` → `flutterfire configure` → `flutter pub get` → `flutter run`
