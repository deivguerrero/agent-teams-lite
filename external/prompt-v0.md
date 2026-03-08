# PROMPT PARA CLAUDE CODE — Crear agente sdd-frontend-v0

## Contexto del proyecto

Estoy construyendo un framework de Spec-Driven Development (SDD) donde los agentes y skills viven como archivos `.md` en carpetas organizadas por responsabilidad. El flujo estándar del framework sigue fases: spec → design → tasks → apply → verify.

Necesito que crees un nuevo agente especializado en generación de componentes frontend usando v0 vía MCP. El agente debe integrarse de forma natural en el flujo SDD existente.

## Tu tarea

1. **Explora la estructura actual del repo** para entender cómo están organizados los agentes/skills existentes. Presta atención a:
   - La convención de nombres de carpetas y archivos
   - El formato interno de los `.md` existentes (cómo están escritos: persona, secciones, tono)
   - Cómo se invocan o referencian entre sí los agentes
   - Si existe un `AGENTS.md` o `README.md` del framework que documente las convenciones

2. **Respeta estrictamente las convenciones que encuentres.** No importes un formato externo; adapta el contenido del nuevo agente al estilo del repo.

3. **Crea el agente `sdd-frontend-v0`** en la ubicación correcta según las convenciones del proyecto. El contenido base del agente está en el archivo adjunto `sdd-frontend-v0.md`. Úsalo como referencia de contenido, pero reformatéalo para que sea consistente con el estilo del repo.

4. **Adapta el stack si es necesario.** El archivo de referencia asume Vue 3 + TypeScript + Tailwind. Si el proyecto tiene un stack frontend diferente definido en algún archivo de configuración o spec, ajusta las referencias de stack en el agente.

5. **Actualiza el índice o registro del framework** si existe alguno (por ejemplo: `AGENTS.md`, `README.md`, `index.md`, o similar) para incluir la entrada de `sdd-frontend-v0` con su propósito y cuándo activarlo.

6. **Verifica la integración** revisando que:
   - El nuevo agente referencia correctamente las fases SDD existentes (sdd-spec, sdd-design, o como se llamen en este repo)
   - El contrato de entrada/salida del agente es compatible con cómo los otros agentes pasan trabajo entre sí
   - No hay conflictos de nombres con agentes existentes

## Prerequisito de entorno que el agente asume

El agente `sdd-frontend-v0` requiere que el MCP de v0 esté registrado en OpenCode. La configuración esperada en `~/.config/opencode/opencode.json` es:

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

Si en el repo existe un archivo de setup o documentación de entorno (`.env.example`, `SETUP.md`, `CONTRIBUTING.md`), agrega ahí la referencia a `V0_API_KEY` como variable requerida para el agente frontend.

## Lo que NO debes hacer

- No modifiques agentes existentes salvo para actualizar el índice
- No cambies el formato de los `.md` existentes
- No crees archivos fuera de la estructura de carpetas del framework
- No asumas el stack frontend; léelo del repo primero

## Entrega esperada

Al terminar, muéstrame:
1. La ruta exacta donde quedó el nuevo agente
2. Los cambios realizados al índice (si aplica)
3. Las adaptaciones que hiciste al contenido de referencia y por qué
4. Si encontraste algo en el repo que requiera atención antes de usar el agente (falta de config de MCP, stack no definido, etc.)
