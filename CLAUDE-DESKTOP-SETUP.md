# 🔗 Conectar NotebookLM con Claude Desktop en Mac

Guía completa para conectar Google NotebookLM con Claude Desktop usando el protocolo MCP (Model Context Protocol). Una vez configurado, podrás consultarle a Claude directamente sobre tus notebooks de NotebookLM sin cambiar de pestaña.

---

## ✅ Requisitos previos

- Mac con macOS 11 (Big Sur) o superior
- Cuenta de Google con acceso a [NotebookLM](https://notebooklm.google.com)
- [Claude Desktop](https://claude.ai/download) instalado
- Conexión a internet

---

## 📦 Paso 1 — Instalar Node.js

Abre la terminal (`Cmd + Espacio` → escribe "Terminal") y verifica si ya tienes Node.js:

```bash
node --version
```

**Si aparece un número de versión** (ej: `v24.15.0`), ya está instalado. Pasa al Paso 2.

**Si aparece `command not found`**, instala Node.js con una de estas opciones:

### Opción A — Con Homebrew (recomendado)

Verifica si tienes Homebrew:
```bash
brew --version
```

Si lo tienes:
```bash
brew install node
```

Si **no** tienes Homebrew, instálalo primero:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
Luego instala Node:
```bash
brew install node
```

### Opción B — Instalador directo (más fácil)

1. Ve a [nodejs.org](https://nodejs.org)
2. Descarga el botón verde **"LTS"**
3. Abre el `.pkg` y sigue el instalador
4. Cierra y vuelve a abrir la terminal
5. Verifica con `node --version`

---

## 🔐 Paso 2 — Autenticarse con Google

### 2.1 — Arreglar permisos de npm (si es necesario)

Si al ejecutar `npx` ves un error `EACCES`, ejecuta:

```bash
sudo chown -R 501:20 "/Users/$(whoami)/.npm"
```

Ingresa tu contraseña de Mac cuando te la pida (es normal que no se vean los caracteres).

### 2.2 — Autenticación con NotebookLM

Ejecuta en la terminal:

```bash
npx notebooklm-mcp-server auth
```

> Si te pregunta `Ok to proceed? (y)` escribe `y` y presiona Enter.

Se abrirá el navegador Chromium automáticamente. Inicia sesión con tu cuenta de Google que tiene acceso a NotebookLM.

Cuando veas en la terminal:
```
Authentication successful!
Your session is now active.
```
¡La autenticación fue exitosa! Esta sesión persiste — solo necesitas hacerlo una vez.

---

## ⚙️ Paso 3 — Configurar Claude Desktop

### 3.1 — Abrir el archivo de configuración

Ejecuta en la terminal:

```bash
open -e ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

Si el archivo no existe aún, créalo con:

```bash
mkdir -p ~/Library/Application\ Support/Claude && touch ~/Library/Application\ Support/Claude/claude_desktop_config.json && open -e ~/Library/Application\ Support/Claude/claude_desktop_config.json
```

### 3.2 — Pegar la configuración

Se abrirá el editor de texto. Pega el siguiente contenido:

```json
{
  "mcpServers": {
    "notebooklm": {
      "command": "npx",
      "args": ["-y", "notebooklm-mcp-server", "start"]
    }
  }
}
```

Guarda con `Cmd + S` y cierra el editor.

---

## 🔄 Paso 4 — Reiniciar Claude Desktop

1. En el Dock, haz **clic derecho** sobre el ícono de Claude
2. Selecciona **"Quit"** (Salir)
3. Vuelve a abrir Claude Desktop

---

## 🎉 ¡Listo! Prueba la conexión

En el chat de Claude deberías ver un ícono de **martillo 🔨** en la barra de entrada — esto indica que los conectores MCP están activos.

Prueba escribiendo:

```
Lista mis notebooks de NotebookLM
```

Claude consultará directamente tu cuenta y mostrará tus notebooks.

---

## 💡 Ejemplos de uso

Una vez conectado, puedes pedirle a Claude cosas como:

- *"Lista mis notebooks de NotebookLM"*
- *"Pregúntale a mi notebook de [tema] cómo funciona X"*
- *"Resume el contenido de mi notebook sobre marketing"*
- *"Genera un resumen en audio de mi notebook sobre finanzas"*
- *"Agrega esta URL como fuente a mi notebook de investigación: https://..."*
- *"Crea un nuevo notebook llamado 'Proyecto 2025'"*

---

## 🛠️ Solución de problemas

### ❌ `zsh: command not found: node`
Node.js no está instalado. Sigue el Paso 1.

### ❌ Error `EACCES` al ejecutar npx
Problema de permisos. Ejecuta:
```bash
sudo chown -R 501:20 "/Users/$(whoami)/.npm"
```

### ❌ El ícono de martillo no aparece en Claude Desktop
- Verifica que el JSON en `claude_desktop_config.json` sea válido (sin comas extra ni llaves faltantes)
- Asegúrate de haber cerrado y vuelto a abrir Claude Desktop completamente

### ❌ Sesión expirada
Si Claude ya no puede conectarse a NotebookLM, renueva la autenticación:
```bash
npx notebooklm-mcp-server auth
```

### ❌ Claude Desktop no encuentra `npx`
Agrega la ruta completa de npx. Primero encuéntrala con:
```bash
which npx
```
Luego reemplaza `"command": "npx"` en el JSON por la ruta completa, por ejemplo:
```json
"command": "/usr/local/bin/npx"
```

---

## 📁 ¿Dónde están los archivos importantes?

| Archivo | Ubicación |
|---|---|
| Configuración de Claude Desktop | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Cookies de autenticación de NotebookLM | `~/.notebooklm-mcp/auth.json` |

---

## 🔗 Referencias

- [notebooklm-mcp-server en npm](https://www.npmjs.com/package/notebooklm-mcp-server)
- [Claude Desktop](https://claude.ai/download)
- [NotebookLM](https://notebooklm.google.com)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io)

---

## 📝 Notas

- Esta integración usa un servidor MCP no oficial que automatiza el navegador para acceder a NotebookLM
- Se recomienda usar una cuenta de Google dedicada (no tu cuenta principal) para mayor seguridad
- Probado en Mac con macOS 12, 13, 14 y 15
