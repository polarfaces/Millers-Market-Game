# Millers Market

Juego de simulación ambientado en una **oficina de mercado financiero**, desarrollado en Unity 6. El jugador explora un entorno de trading con pantallas, periódicos, teléfonos y objetos interactivos que cuentan la historia del mercado.

> **Estado actual:** pre-alpha — entorno 3D funcional, lógica de juego en desarrollo.

---

## Requisitos

| Herramienta | Versión |
|---|---|
| Unity Editor | **6000.3.6f1** (Unity 6) |
| Render Pipeline | URP 17.3 |
| Input | Nuevo Input System 1.18 |

Abre el proyecto con **Unity Hub** seleccionando la carpeta raíz del repositorio.

---

## Inicio rápido

1. Clona el repositorio:
   ```bash
   git clone https://github.com/polarfaces/Millers-Market-Game.git
   ```
2. Abre el proyecto en Unity **6000.3.6f1**.
3. Carga la escena principal:
   ```
   Assets/MillersMarket/Scenes/Office_Main.unity
   ```
4. Pulsa **Play** para recorrer la oficina en primera persona (cámara hija del objeto `Player`).

---

## Estructura de carpetas

Todo el contenido del juego vive bajo `Assets/MillersMarket/`:

```
Assets/MillersMarket/
│
├── Art/                              # Contenido visual
│   └── Environment/
│       └── Office/
│           ├── Models/               # Mallas FBX (escritorio, TV, cámara, etc.)
│           └── Surfaces/             # Materiales + texturas PBR por categoría
│               ├── Alarm/
│               ├── Cabinet/
│               ├── Camera/
│               ├── Coffee/
│               ├── Desk/
│               ├── Door/
│               ├── Floor/
│               ├── Keyboard/
│               ├── Lighting/
│               ├── Newspaper/
│               ├── Phone/
│               ├── Poster/
│               ├── Screen/
│               ├── TV/
│               ├── Vent/
│               └── Wall/
│
├── Audio/
│   ├── Ambience/                     # Sonido ambiental de oficina
│   ├── Music/                        # Banda sonora
│   └── SFX/                          # Efectos puntuales
│
├── Prefabs/
│   ├── Environment/                  # Prefabs de arquitectura (paredes, suelo)
│   ├── Player/                       # Prefab del jugador
│   └── Props/                        # Prefabs de objetos interactuables
│
├── Scenes/
│   └── Office_Main.unity             # Escena principal (incluida en build)
│
├── Scripts/                          # Código C# modular por ensamblado
│   ├── Core/                         # MillersMarket.Core
│   ├── Player/                       # MillersMarket.Player
│   ├── Interaction/                  # MillersMarket.Interaction
│   ├── UI/                           # MillersMarket.UI
│   └── Editor/                       # MillersMarket.Editor (solo Editor)
│
└── Settings/
    ├── Input/
    │   └── PlayerInput.inputactions  # Mapa de acciones (Move, Look, Interact…)
    └── Rendering/                    # Perfiles URP (PC + Mobile)
```

### Reglas de organización

| Tipo de asset | Ubicación | Convención de nombre |
|---|---|---|
| Modelos 3D | `Art/.../Models/` | `snake_case` descriptivo (`security_camera_01_4k.fbx`) |
| Texturas | `Art/.../Surfaces/{Categoría}/` | Sufijo PBR: `_diff`, `_nor_gl`, `_rough`, `_metal` |
| Materiales | Junto a sus texturas | `PascalCase` o nombre del prop (`door 1.mat`) |
| Scripts | `Scripts/{Módulo}/` | Un tipo público por archivo, `PascalCase` |
| Prefabs | `Prefabs/{Tipo}/` | `PascalCase` (`PlayerController.prefab`) |
| Escenas | `Scenes/` | `PascalCase` descriptivo (`Office_Main.unity`) |
| Audio | `Audio/{Categoría}/` | `snake_case` con prefijo de categoría |

**No crear carpetas sueltas en `Assets/`** — todo nuevo contenido va dentro de `MillersMarket/`.

---

## Arquitectura de código

El proyecto usa **Assembly Definitions** para compilar solo lo necesario en cada cambio:

```
MillersMarket.Core          ← constantes, servicios, utilidades base
    ↑
    ├── MillersMarket.Player        ← movimiento, cámara, input
    ├── MillersMarket.UI            ← HUD, menús
    └── MillersMarket.Interaction   ← objetos interactuables (depende de Player)
            ↑
MillersMarket.Editor        ← herramientas de editor (solo plataforma Editor)
```

### Namespaces

Cada carpeta de scripts mapea 1:1 a su namespace:

| Carpeta | Namespace |
|---|---|
| `Scripts/Core/` | `MillersMarket.Core` |
| `Scripts/Player/` | `MillersMarket.Player` |
| `Scripts/Interaction/` | `MillersMarket.Interaction` |
| `Scripts/UI/` | `MillersMarket.UI` |
| `Scripts/Editor/` | `MillersMarket.Editor` |

### Convenciones C#

- Namespaces con **file-scoped** (`namespace MillersMarket.Core;`)
- Tipos y métodos públicos en `PascalCase`
- Campos privados en `_camelCase`
- Constantes en `PascalCase` dentro de clases estáticas (`GameConstants`)
- Un script = una responsabilidad = un archivo
- Sin lógica de juego en scripts de Editor

El archivo `.editorconfig` en la raíz refuerza estas reglas para el IDE.

---

## Escena principal: Office_Main

La oficina incluye props colocados como instancias de modelos 3D:

- Escritorios, teclado, teléfono, TVs con texturas de pantalla
- Reloj despertador, cámara de seguridad animada
- Periódicos, carteles, puerta industrial, carrito de café
- Arquitectura básica: suelo + paredes con colliders

**Pendiente de implementar:** movimiento del jugador, interacciones, UI y audio.

---

## Input

Mapa de acciones en `Settings/Input/PlayerInput.inputactions`:

| Acción | Tipo | Uso previsto |
|---|---|---|
| Move | Vector2 | Desplazamiento WASD / stick |
| Look | Vector2 | Rotación de cámara |
| Interact | Button (Hold) | Usar objetos de la oficina |
| Jump | Button | Salto |
| Crouch | Button | Agacharse |

El Input System está activo a nivel de proyecto. Los scripts de `MillersMarket.Player` deben consumir estas acciones.

---

## Renderizado

- **URP** con perfiles separados para PC y Mobile en `Settings/Rendering/`
- Post-procesado activo en la cámara principal
- Texturas PBR en 4K — optimizar antes de build de producción (compresión ASTC/BCn, mipmaps)

---

## Control de versiones

```bash
# Remoto
https://github.com/polarfaces/Millers-Market-Game.git

# Rama principal
main
```

Archivos ignorados según `.gitignore` estándar de Unity (`Library/`, `Temp/`, `Logs/`, etc.).

---

## Roadmap

- [ ] `PlayerController` con Input System
- [ ] Extraer props a Prefabs reutilizables
- [ ] Sistema de interacción (pantallas, teléfono, periódico)
- [ ] UI básica (HUD + menú pausa)
- [ ] Audio ambiental
- [ ] Optimización de texturas para build
- [ ] Mecánicas de simulación de mercado

---

## Licencias de assets

Los modelos 3D provienen de fuentes externas (Sketchfab y similares). Revisar y documentar la atribución de cada asset antes de distribución comercial.

---

## Contacto

Repositorio: [polarfaces/Millers-Market-Game](https://github.com/polarfaces/Millers-Market-Game)
