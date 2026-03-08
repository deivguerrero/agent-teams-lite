# Extensión sdd-frontend-v0 — Guía de Configuración

> Guía completa para configurar el MCP de v0 en cada AI coding assistant soportado.

---

## ¿Qué es sdd-frontend-v0?

`sdd-frontend-v0` es una extensión opcional del framework Agent Teams Lite que permite generar componentes frontend de producción usando [v0 by Vercel](https://v0.dev) vía MCP dentro del flujo SDD.

```
sdd-spec → sdd-design → sdd-frontend-v0 → sdd-apply → sdd-verify
                              ↑
              (se activa cuando el spec tiene sección ## UI o ## Frontend)
```

**El skill está instalado automáticamente** junto con los demás skills (`~/{tool}/skills/sdd-frontend-v0/SKILL.md`), pero requiere configuración manual del MCP de v0 para funcionar.

---

## Prerequisitos

### 1. Cuenta en v0

Necesitás una cuenta activa en [v0.dev](https://v0.dev). La extensión usa el MCP oficial de v0.

### 2. V0 API Key

Obtené tu API key desde la configuración de tu cuenta en v0.dev.

Guardala como variable de entorno en tu shell (`~/.zshrc`, `~/.bashrc`, o `~/.profile`):

```bash
export V0_API_KEY=v0_xxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

**Nunca la hardcodees en archivos de configuración ni la expongas en logs.**

Recargá tu shell:

```bash
source ~/.zshrc   # o ~/.bashrc
```

Verificá que está disponible:

```bash
echo $V0_API_KEY   # debe mostrar tu key
```

---

## Configuración por AI Assistant

### Claude Code

En Claude Code, los MCPs se configuran en `~/.claude/claude_desktop_config.json`.

**Opción A — Via CLI (recomendado):**

```bash
claude mcp add v0 \
  --transport sse \
  https://mcp.v0.dev \
  --header "Authorization: Bearer $V0_API_KEY"
```

**Opción B — Editar el archivo manualmente:**

Abrí `~/.claude/claude_desktop_config.json` (créalo si no existe) y agregá:

```json
{
  "mcpServers": {
    "v0": {
      "type": "sse",
      "url": "https://mcp.v0.dev",
      "headers": {
        "Authorization": "Bearer ${V0_API_KEY}"
      }
    }
  }
}
```

**Verificar:**

Abrí Claude Code y escribí:

```
¿Cuáles MCPs tenés disponibles?
```

Deberías ver `v0` en la lista.

---

### OpenCode

Editá `~/.config/opencode/opencode.json`. Si no existe, créalo:

```json
{
  "mcpServers": {
    "v0": {
      "type": "sse",
      "url": "https://mcp.v0.dev",
      "headers": {
        "Authorization": "Bearer ${V0_API_KEY}"
      }
    }
  }
}
```

Si ya tenés otros MCPs configurados, agregá solo el bloque `"v0"` dentro de `"mcpServers"`:

```json
{
  "mcpServers": {
    "engram": { ... },
    "v0": {
      "type": "sse",
      "url": "https://mcp.v0.dev",
      "headers": {
        "Authorization": "Bearer ${V0_API_KEY}"
      }
    }
  }
}
```

**Verificar:**

Reiniciá OpenCode y ejecutá `/sdd-v0`. Debería mostrar el prereq check:

```
✓ v0 MCP disponible
```

---

### Cursor

En Cursor, los MCPs se configuran en `.cursor/mcp.json` a nivel proyecto o en la configuración global.

**Configuración global** (`~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "v0": {
      "type": "sse",
      "url": "https://mcp.v0.dev",
      "headers": {
        "Authorization": "Bearer ${V0_API_KEY}"
      }
    }
  }
}
```

**Configuración por proyecto** (`.cursor/mcp.json` en el root del proyecto):

```json
{
  "mcpServers": {
    "v0": {
      "type": "sse",
      "url": "https://mcp.v0.dev",
      "headers": {
        "Authorization": "Bearer ${V0_API_KEY}"
      }
    }
  }
}
```

Reiniciá Cursor para que detecte el nuevo MCP.

---

### Codex (OpenAI)

Codex soporta MCPs a través de la configuración de agentes. Editá `~/.codex/config.json`:

```json
{
  "mcpServers": {
    "v0": {
      "type": "sse",
      "url": "https://mcp.v0.dev",
      "headers": {
        "Authorization": "Bearer ${V0_API_KEY}"
      }
    }
  }
}
```

---

### Gemini CLI

Gemini CLI no soporta MCPs de terceros en SSE de forma nativa en su versión actual. El skill `sdd-frontend-v0` puede estar instalado, pero no podrá invocar el MCP de v0 automáticamente.

**Workaround disponible:** Podés usar el skill manualmente — el agente te indicará qué prompt enviar a v0.dev en el browser, y vos pegás el código resultante. El skill ajusta su comportamiento cuando detecta que el MCP no está disponible:

```
[sdd-frontend-v0] AVISO: v0 MCP no disponible en este entorno.
Modo manual activado — te guío para usar v0.dev directamente.
```

---

### Antigravity

Antigravity ejecuta todo inline (sin sub-agentes reales), lo que limita el acceso a MCPs externos. El comportamiento es similar a Gemini CLI — el skill estará instalado pero operará en modo manual si el MCP no está disponible.

---

### VS Code (GitHub Copilot)

VS Code con GitHub Copilot soporta MCPs desde las versiones recientes. Editá `settings.json` o `.vscode/settings.json`:

```json
{
  "github.copilot.mcp.servers": {
    "v0": {
      "type": "sse",
      "url": "https://mcp.v0.dev",
      "headers": {
        "Authorization": "Bearer ${V0_API_KEY}"
      }
    }
  }
}
```

---

## Verificación Rápida

Para confirmar que todo está configurado correctamente en tu assistant:

1. Abrí tu AI assistant en un proyecto con frontend
2. Escribí: `sdd v0 prueba` (o `/sdd-v0 prueba` en OpenCode)
3. El skill debería responder:
   ```
   ✓ v0 MCP disponible
   ✓ V0_API_KEY presente
   ✓ Stack detectado: {framework detectado}
   Listo para generar componentes. Proporcioná un spec o descripción.
   ```

Si el MCP no está disponible, verás el error de configuración con la instrucción correspondiente.

---

## Uso dentro del flujo SDD

### Activación automática (recomendado)

El orquestador sugiere `sdd-frontend-v0` automáticamente cuando detecta que el spec activo tiene una sección `## UI` o `## Frontend`:

```
[Orquestador detecta sección ## UI en el spec]
→ ¿Querés generar el componente con sdd-frontend-v0 antes de sdd-apply?
```

### Activación manual

```
# Claude Code / texto natural
sdd v0 LoginForm

# OpenCode / slash command
/sdd-v0 LoginForm
```

### Con wireframe

```
# Con imagen de referencia
sdd v0 DashboardLayout --wireframe ./designs/dashboard-v2.png
```

---

## Tabla Resumen de Soporte

| AI Assistant | Soporte MCP v0 | Modo |
|---|---|---|
| Claude Code | ✅ Nativo | Automático |
| OpenCode | ✅ Nativo | Automático |
| Cursor | ✅ Nativo | Automático |
| Codex | ✅ Nativo | Automático |
| VS Code Copilot | ✅ Nativo (versiones recientes) | Automático |
| Gemini CLI | ⚠️ No nativo | Manual (guiado) |
| Antigravity | ⚠️ No nativo | Manual (guiado) |

---

## Troubleshooting

### "v0 MCP not available"
- Verificá que `V0_API_KEY` está exportada en tu shell (`echo $V0_API_KEY`)
- Verificá que el archivo de configuración tiene la sintaxis correcta (JSON válido)
- Reiniciá el assistant después de editar la configuración

### "v0 credits exhausted"
- El skill reporta inmediatamente sin retry
- Verificá tu plan en v0.dev y recargá créditos si es necesario

### v0 genera el framework incorrecto
- El skill envía automáticamente una corrección al chat de v0
- Si persiste después de 2 correcciones, el skill reporta al orquestador con el código actual

### Stack no detectado
- El skill pregunta antes de continuar: `"¿Qué stack usás? (next/react/vue/nuxt/svelte/angular)"`
- Podés evitar esto asegurándote de tener `package.json` con las dependencias del framework

---

## Variables de Entorno Requeridas

| Variable | Requerida para | Descripción |
|---|---|---|
| `V0_API_KEY` | sdd-frontend-v0 | API key de tu cuenta v0.dev |

Estas variables deben estar disponibles en el entorno donde corre tu AI assistant.
