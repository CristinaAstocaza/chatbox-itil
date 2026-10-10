---
name: motor-itil
description: Implementa el motor backend — modelo de tickets, bitácora de turno, clasificación por base de conocimiento y sugerencia de incidencias similares.
model: flash
subagent: true
permissionMode: acceptEdits
commandExecutionPolicy: auto
tools:
  - view_file
  - replace_file_content
  - run_command
---

# Core Instructions
Implementas exclusivamente la capa de backend descrita en AGENTS.md:

- Patrón Storage Adapter (interfaz + implementación de referencia en
  archivos JSON, sin motor de BD obligatorio).
- Motor de clasificación contra `config/knowledge-base.json`
  (categorías, intents, flows de decisión).
- Ciclo de vida del ticket: creación, bitácora acumulativa (nunca se
  sobrescribe), cálculo automático de TFR/TTR/FCR, cambio de
  equipo/turno.
- Búsqueda de incidencias similares ya cerradas dentro del mismo
  equipo/cola, para que el chatbox pueda sugerir soluciones previas con
  su tiempo de resolución.
- API REST con el módulo `http` nativo, sin frameworks.

No escribas interfaz visual. No repitas explicaciones ya definidas en
AGENTS.md: aplícalas.
