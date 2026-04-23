# 🎬 Video Wallpaper en macOS con Swift

Guía completa para crear un fondo de pantalla animado con videos en macOS usando Swift nativo. No requiere Xcode completo, solo las Command Line Tools.

---

## ✅ Requisitos

- macOS Monterey o superior
- Swift instalado (viene con las Command Line Tools de Apple)
- Un archivo de video (mp4, mov, etc.)

Verifica que tienes Swift instalado:
```bash
swiftc --version
```

---

## 📄 Código completo (wallpaper.swift)

```swift
import Cocoa
import AVFoundation

class WallpaperWindow: NSWindow {
    init() {
        let screen = NSScreen.main!.frame
        super.init(
            contentRect: screen,
            styleMask: .borderless,
            backing: .buffered,
            defer: false
        )
        self.level = NSWindow.Level(rawValue: Int(CGWindowLevelForKey(.desktopWindow)))
        self.collectionBehavior = [.canJoinAllSpaces, .stationary, .ignoresCycle]
        self.isOpaque = true
        self.backgroundColor = .black

        let player = AVPlayer(url: URL(fileURLWithPath: "/Users/TU_USUARIO/Downloads/tu_video.mp4"))

        // Silencia el audio del video
        player.isMuted = true

        let playerLayer = AVPlayerLayer(player: player)
        playerLayer.frame = screen
        playerLayer.videoGravity = .resizeAspectFill

        // Loop infinito: cuando termina el video vuelve al inicio
        NotificationCenter.default.addObserver(
            forName: .AVPlayerItemDidPlayToEndTime,
            object: player.currentItem,
            queue: .main
        ) { _ in
            player.seek(to: .zero)
            player.play()
        }

        let view = NSView(frame: screen)
        view.wantsLayer = true
        view.layer?.addSublayer(playerLayer)
        self.contentView = view

        player.play()
    }
}

let app = NSApplication.shared
app.setActivationPolicy(.accessory)
let window = WallpaperWindow()
window.makeKeyAndOrderFront(nil)
app.run()
```

---

## 🚀 Pasos para usarlo

### 1. Crear el archivo Swift

Abre la terminal y pega esto (cambia la ruta del video por la tuya):

```bash
cat > ~/Desktop/wallpaper.swift << 'EOF'
import Cocoa
import AVFoundation

class WallpaperWindow: NSWindow {
    init() {
        let screen = NSScreen.main!.frame
        super.init(
            contentRect: screen,
            styleMask: .borderless,
            backing: .buffered,
            defer: false
        )
        self.level = NSWindow.Level(rawValue: Int(CGWindowLevelForKey(.desktopWindow)))
        self.collectionBehavior = [.canJoinAllSpaces, .stationary, .ignoresCycle]
        self.isOpaque = true
        self.backgroundColor = .black

        let player = AVPlayer(url: URL(fileURLWithPath: "/Users/TU_USUARIO/Downloads/tu_video.mp4"))
        player.isMuted = true

        let playerLayer = AVPlayerLayer(player: player)
        playerLayer.frame = screen
        playerLayer.videoGravity = .resizeAspectFill

        NotificationCenter.default.addObserver(
            forName: .AVPlayerItemDidPlayToEndTime,
            object: player.currentItem,
            queue: .main
        ) { _ in
            player.seek(to: .zero)
            player.play()
        }

        let view = NSView(frame: screen)
        view.wantsLayer = true
        view.layer?.addSublayer(playerLayer)
        self.contentView = view

        player.play()
    }
}

let app = NSApplication.shared
app.setActivationPolicy(.accessory)
let window = WallpaperWindow()
window.makeKeyAndOrderFront(nil)
app.run()
EOF
```

### 2. Compilar el archivo

```bash
swiftc ~/Desktop/wallpaper.swift -o ~/Desktop/wallpaper -framework Cocoa -framework AVFoundation
```

> ⚠️ La compilación puede tardar 2-3 minutos la primera vez. Es normal.

### 3. Ejecutar el wallpaper

```bash
~/Desktop/wallpaper
```

### 4. Detener el wallpaper

Presiona `Ctrl + C` en la terminal.

---

## 🔧 Personalización

### Cambiar el video
Edita esta línea en el código y cambia la ruta por la de tu video:
```swift
let player = AVPlayer(url: URL(fileURLWithPath: "/Users/TU_USUARIO/Downloads/tu_video.mp4"))
```

### Activar el audio del video
Cambia esta línea:
```swift
player.isMuted = true
```
Por:
```swift
player.isMuted = false
```

### Cambiar cómo se ajusta el video a la pantalla
Modifica `videoGravity`:
```swift
playerLayer.videoGravity = .resizeAspectFill  // Llena toda la pantalla (puede recortar)
playerLayer.videoGravity = .resizeAspect      // Muestra el video completo (puede dejar barras negras)
playerLayer.videoGravity = .resize            // Estira el video para llenar la pantalla
```

---

## 📌 Notas importantes

- El video queda **detrás de los íconos del escritorio**, igual que un wallpaper normal.
- La app **no aparece en el Dock** ni en el Alt+Tab.
- Funciona con cualquier formato de video que soporte macOS: mp4, mov, m4v, etc.
- Cada vez que cambies el código debes volver a compilar con `swiftc`.
- Probado en **macOS Monterey 12.7.4** con **Swift 5.7.2**.

---

## 💻 Versiones probadas

| macOS | Swift | Estado |
|-------|-------|--------|
| Monterey 12.7.4 | 5.7.2 | ✅ Funciona |

---

## 📁 Estructura del proyecto

```
wallpaper.swift   → Código fuente en Swift
wallpaper         → Ejecutable compilado (se genera al compilar)
```

---

## 🤝 Contribuciones

Si lo pruebas en otra versión de macOS o Swift, abre un Issue o Pull Request con los resultados.
