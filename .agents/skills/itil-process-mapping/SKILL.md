---
name: itil-process-mapping
description: Mapeo completo de los procesos ITIL aplicados a este sistema (gestión de incidencias, solicitudes de servicio y conocimiento), adaptado del estudio de caso SUNARP a una mesa de ayuda interna con traspaso de turno. Úsalo al planificar cualquier tarea de modelado de datos o de flujo de trabajo.
---

# Mapeo de procesos ITIL para este proyecto

## 1. Gestión de Incidencias (Incident Management)

Ciclo de vida obligatorio para cada ticket:

registro -> categorización -> priorización -> diagnóstico inicial
-> (resuelto en el equipo actual) -> cierre
-> (no resuelto) -> derivación a otro equipo -> ... -> cierre


Reglas:
- El registro ocurre automáticamente en el primer mensaje del chatbox,
  sin que el agente tenga que abrir un formulario aparte.
- La categorización usa las 8 categorías base (ver sección 4).
- Cada paso de diagnóstico o intento de solución se agrega a la
  `bitacora` del ticket como una entrada nueva — nunca se edita ni se
  borra una entrada anterior.
- El cierre solo puede ocurrir cuando hay al menos una entrada de
  bitácora que describe la solución aplicada.

## 2. Gestión de Solicitudes de Servicio (Service Request Management)

Para consultas básicas que no son una falla (p. ej. "cómo se configura
X", "cuál es el procedimiento para Y"): se resuelven vía la Gestión de
Conocimiento (sección 3) sin necesidad de abrir un ticket completo, a
menos que la respuesta no exista, en cuyo caso se convierte
automáticamente en un ticket de incidencia categoría "otro" o
"requisitos".

## 3. Gestión del Conocimiento (Knowledge Management)

Dos capas, nunca una sola:

- **Capa estática (intents):** una pregunta, una respuesta, definida en
  `config/knowledge-base.json`. Sirve para información que no cambia
  según el caso (horarios, procedimientos generales).
- **Capa dinámica (flows / búsqueda de similares):** para preguntas que
  dependen del caso concreto. Incluye:
  - Flows guiados con preguntas de seguimiento y cálculo (igual que el
    ejemplo de costo de compraventa ya construido).
  - Búsqueda de incidencias similares ya cerradas dentro del mismo
    equipo/cola, mostrando qué se hizo y cuánto tardó, para que el
    agente decida si aplica al caso actual.

La base de conocimiento se recarga sin reiniciar el sistema
(`/api/kb/reload` o equivalente) para que el ciclo de mejora continua
sea inmediato.

## 4. Categorías base (no renombrar, son las del instrumento validado)

`tramites`, `estado`, `requisitos`, `costos`, `incidencia_tecnica`,
`reclamo`, `informacion_general`, `otro`.

## 5. Equipos/colas de soporte (destinos de derivación)

`mesa_de_ayuda`, `area_tecnica`, `supervisor`, `otro`. Cada ticket
pertenece a un equipo/cola en todo momento; al derivar, cambia de
equipo pero conserva toda su bitácora anterior — el equipo receptor
debe poder leerla íntegra.

## 6. Traspaso de turno

Un turno es un atributo del agente que atiende (mañana, tarde, noche,
o el esquema que defina la institución), no del ticket en sí. El
ticket puede pasar por varios turnos sin cerrarse. Al iniciar sesión,
el agente entrante debe poder filtrar "tickets abiertos de mi
equipo" y ver, para cada uno, la bitácora completa ordenada
cronológicamente con autor, acción y minutos invertidos.

## 7. Métricas obligatorias (se calculan, nunca se registran a mano)

- **TFR** = hora de primera respuesta − hora de inicio del ticket.
- **TTR** = hora de cierre − hora de inicio del ticket.
- **FCR** = se resolvió sin pasar por más de un equipo/cola.
- **Satisfacción** (opcional, si se mantiene la encuesta): promedio de
  los 6 ítems Likert del instrumento validado.

## 8. Portabilidad (regla de diseño, no de contenido)

Ningún proceso de esta lista debe codificarse pensando en una
institución concreta. Las categorías, los flows y las respuestas son
datos de configuración. El código de `motor-itil` solo conoce
estructura (categoría, equipo, bitácora, métricas), nunca contenido
institucional específico.
