# 🖥️ Desktop Commander MCP — Guía Completa

> Versión: `v0.2.40` · Paquete: `@wonderwhy-er/desktop-commander` · macOS Monterey

Desktop Commander es un servidor MCP (Model Context Protocol) gratuito y de código abierto que le da a Claude acceso directo al sistema de archivos, terminal y herramientas de desarrollo de tu Mac.

---

## 📖 Índice

1. [Requisitos](#requisitos)
2. [Instalación](#instalación)
3. [Configuración en Claude Desktop](#configuración-en-claude-desktop)
4. [Permisos de acceso (macOS)](#permisos-de-acceso-macos)
5. [Herramientas disponibles](#herramientas-disponibles)
6. [Configuración avanzada](#configuración-avanzada)
7. [Ejemplos prácticos](#ejemplos-prácticos)
8. [Comandos bloqueados](#comandos-bloqueados)
9. [Preguntas frecuentes](#preguntas-frecuentes)

---

## ✅ Requisitos

| Requisito | Versión mínima |
|---|---|
| Node.js | `>= v18.0.0` |
| Claude Desktop | Cualquiera |
| macOS | Monterey o superior |

---

## ⚡ Instalación

### Opción 1 — Script automático (recomendado macOS)

```bash
curl -fsSL https://raw.githubusercontent.com/wonderwhy-er/DesktopCommanderMCP/refs/heads/main/install.sh | bash
```

### Opción 2 — NPX (multiplataforma)

```bash
npx @wonderwhy-er/desktop-commander@latest setup
```

---

## ☁️ Configuración en Claude Desktop

El archivo de configuración se ubica en:

```
[macOS]   ~/Library/Application Support/Claude/claude_desktop_config.json
[Windows] %APPDATA%\Claude\claude_desktop_config.json
```

Agrega esta entrada dentro de `mcpServers`:

```json
{
  "mcpServers": {
    "desktop-commander": {
      "command": "npx",
      "args": [
        "-y",
        "@wonderwhy-er/desktop-commander@latest"
      ]
    }
  }
}
```

Reinicia Claude Desktop después de guardar el archivo.

---

## 🔒 Permisos de acceso (macOS)

Por defecto, el MCP no puede acceder a Desktop, Documents ni Downloads por las restricciones de macOS. Para habilitarlo:

1. Ve a **Preferencias del Sistema → Seguridad y Privacidad → Privacidad**
2. Selecciona **Acceso total al disco** en la lista lateral
3. Haz clic en el 🔒 candado e ingresa tu contraseña
4. Haz clic en `+` y agrega **Claude.app** (`/Applications/Claude.app`)
5. **Reinicia Claude Desktop** completamente (Cmd+Q)

> **Nota:** El proceso Node.js del MCP es lanzado por `Claude.app/Contents/Helpers/disclaimer`, por lo que es Claude Desktop quien necesita el permiso, no Terminal.

---

## 🔧 Herramientas disponibles

El MCP expone **21 herramientas** organizadas en 5 categorías:

### 📁 FileSystem

| Herramienta | Descripción | Parámetros clave |
|---|---|---|
| `read_file` | Lee texto, PDF, DOCX, Excel, imágenes. Soporta lectura parcial | `path*`, `offset`, `length`, `sheet`, `range` |
| `write_file` | Escribe o hace append en archivos. Soporta modo `rewrite` y `append` | `path*`, `content*`, `mode` |
| `view` | Lista directorios hasta N niveles de profundidad | `path*`, `depth` |
| `list_directory` | Lista archivos y carpetas con prefijos `[FILE]` y `[DIR]` | `path*`, `depth` |
| `create_directory` | Crea un directorio (y subdirectorios) | `path*` |
| `move_file` | Mueve o renombra archivos y carpetas | `source*`, `destination*` |
| `get_file_info` | Metadatos: tamaño, fechas, permisos, tipo MIME | `path*` |
| `start_search` | Búsqueda en background por nombre o contenido | `pattern*`, `path`, `type` |
| `get_more_search_results` | Obtiene más resultados de una búsqueda activa | `sessionId*` |
| `stop_search` | Detiene una búsqueda activa | `sessionId*` |
| `list_searches` | Lista todas las búsquedas en curso | — |

### 💻 Terminal

| Herramienta | Descripción | Parámetros clave |
|---|---|---|
| `start_process` | Inicia procesos: shells, REPLs (`python3 -i`, `node -i`), scripts | `command*`, `timeout_ms*`, `shell` |
| `interact_with_process` | Envía input a un proceso activo y recibe la respuesta | `pid*`, `input*`, `timeout_ms` |
| `read_process_output` | Lee la salida acumulada sin enviar input | `pid*`, `timeout_ms` |

### ✂️ Edición

| Herramienta | Descripción | Parámetros clave |
|---|---|---|
| `str_replace` | Reemplaza un fragmento único en un archivo | `path*`, `old_str*`, `new_str` |
| `edit_block` | Ediciones quirúrgicas incluyendo DOCX (XML) y Excel (rangos) | `file_path*`, `old_string`, `new_string`, `range` |

### ⚙️ Proceso

| Herramienta | Descripción | Parámetros clave |
|---|---|---|
| `kill_process` | Termina un proceso por PID | `pid*` |
| `force_terminate` | Fuerza la terminación cuando `kill_process` no es suficiente | `pid*` |
| `list_sessions` | Lista todas las sesiones de terminal activas | — |
| `list_processes` | Lista todos los procesos del sistema | — |

### 🔧 Configuración

| Herramienta | Descripción | Parámetros clave |
|---|---|---|
| `get_config` | Devuelve la configuración completa del servidor | — |
| `set_config_value` | Actualiza un valor de configuración en tiempo de ejecución | `key*`, `value*` |

---

## ⚙️ Configuración avanzada

Puedes personalizar el comportamiento del MCP editando su configuración. Usa `get_config` desde Claude para ver los valores actuales y `set_config_value` para cambiarlos.

| Parámetro | Valor por defecto | Descripción |
|---|---|---|
| `defaultShell` | `/bin/zsh` (macOS) | Shell usado para ejecutar comandos |
| `allowedDirectories` | `[]` (sin restricciones) | Lista de carpetas permitidas |
| `fileReadLineLimit` | `1000` | Máximo de líneas por lectura |
| `fileWriteLineLimit` | `50` | Máximo de líneas por escritura (recomendado: ≤ 30) |
| `telemetryEnabled` | `true` | Telemetría anónima de uso |

### Restringir acceso a carpetas específicas

```json
{
  "mcpServers": {
    "desktop-commander": {
      "command": "npx",
      "args": ["-y", "@wonderwhy-er/desktop-commander@latest"],
      "env": {
        "ALLOWED_DIRECTORIES": "/Users/tu-usuario/proyectos,/Users/tu-usuario/Documents"
      }
    }
  }
}
```

---

## 💡 Ejemplos prácticos

### Leer un archivo parcialmente (sin cargar todo al contexto)

```
Lee las líneas 50 a 100 del archivo /Users/new/proyecto/index.js
```
> Usa `read_file` con `offset: 50` y `length: 50` — ahorra tokens al no cargar el archivo completo.

### Editar sin regenerar el archivo completo

```
En /Users/new/jobs/index.html, reemplaza el texto "Hola Mundo" por "Bienvenido"
```
> Usa `str_replace` — solo envía el fragmento que cambia, no las 500 líneas del archivo.

### Ejecutar un script Python y ver el resultado

```
Ejecuta el script /Users/new/jobs/analisis.py y muéstrame la salida
```
> Usa `start_process` + `read_process_output`.

### Buscar todos los archivos .js que contengan "useState"

```
Busca todos los archivos .js en /Users/new/proyectos que contengan "useState"
```
> Usa `start_search` con `pattern: "useState"` y `type: "content"`.

### Crear un directorio y escribir un archivo

```
Crea la carpeta /Users/new/nuevo-proyecto y dentro un archivo README.md
```
> Usa `create_directory` + `write_file`.

---

## 🚫 Comandos bloqueados

Por seguridad, los siguientes comandos están **bloqueados por defecto** y no pueden ejecutarse:

```
mkfs, format, mount, umount, fdisk, dd, parted, diskpart
sudo, su, passwd, adduser, useradd, usermod, groupadd, chsh, visudo
shutdown, reboot, halt, poweroff, init
iptables, firewall, netsh, sfc, bcdedit, reg, net, sc, runas
cipher, takeown
```

Puedes modificar esta lista con `set_config_value` → `blockedCommands`.

---

## ❓ Preguntas frecuentes

**¿Por qué no puede acceder a Desktop o Documents?**
macOS requiere permiso explícito de "Acceso Total al Disco". Ve a la sección [Permisos](#permisos-de-acceso-macos).

**¿El MCP tiene acceso a internet?**
No directamente. Solo puede ejecutar comandos del sistema. Si necesitas acceso a internet, usa herramientas como `curl` o `wget` desde un proceso.

**¿Reduce el consumo de tokens?**
Sí, especialmente en proyectos con múltiples ediciones. `str_replace` y `edit_block` envían solo el fragmento que cambia. `read_file` con `offset/length` carga solo la sección necesaria.

**¿Es compatible con otros editores?**
Sí. Funciona con Claude Desktop, Cursor, Windsurf y cualquier cliente MCP-compatible.

**¿Cómo desinstalo el MCP?**
```bash
npx @wonderwhy-er/desktop-commander@latest uninstall
```

---

## 🔗 Recursos

- [Repositorio oficial](https://github.com/wonderwhy-er/DesktopCommanderMCP)
- [Sitio web](https://desktopcommander.app/mcp/)
- [NPM Package](https://www.npmjs.com/package/@wonderwhy-er/desktop-commander)
- [FAQ completo en GitHub](https://github.com/wonderwhy-er/DesktopCommanderMCP/blob/main/FAQ.md)
- [Discord](https://discord.gg/pyXshw54)
