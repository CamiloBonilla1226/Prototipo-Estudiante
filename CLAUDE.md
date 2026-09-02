# CLAUDE.md — Panel del Estudiante (ACBB)

Contexto para Claude Code al trabajar en esta carpeta. Cúbrela junto con
`estudiante.html` y los tres formularios de solicitud:
`formulario-cancelacion-matricula.html`,
`formulario-cancelacion-asignatura.html`,
`formulario-examen-supletorio.html`.

## 1. Qué es este proyecto

Prototipo de aplicación web para la gestión de los procesos académicos de
**Cancelación de Matrícula**, **Cancelación de Asignatura** y **Examen
Supletorio** — FIET, Universidad del Cauca. Trabajo de grado de Andersson
Camilo Bonilla Belalcázar.

Son mockups **HTML de archivo único, estáticos e interactivos**. No hay
backend real. Los datos son de ejemplo (arrays `DATA`/`SOLICITUDES`
hardcodeados). El paso de una solicitud entre `estudiante.html`,
`funcionario.html` y `decano.html` se simula con `localStorage` como puente
de una sola vez.

Actores del sistema: **Estudiante**, **Funcionario Académico**, **Decano**.
Escribe siempre estos nombres con esta capitalización exacta.

Esta carpeta cubre **únicamente el panel del Estudiante y sus tres
formularios de solicitud**. Los paneles de Funcionario y Decano viven en
otras carpetas, cada una con su propio `CLAUDE.md`; no los edites desde aquí.

## 2. DECISIÓN CLAVE — la Resolución es 100% física, no se toca en código

Por reglamento de la Universidad del Cauca **no se admiten firmas digitales**.
Esto ya está reflejado en el código actual y **debe mantenerse así**:

- La Resolución (documento que aprueba o rechaza el trámite) **nunca se
  genera, sube, escanea ni descarga desde la aplicación**. No implementes
  botones de "Generar PDF", "Adjuntar resolución" ni similares.
- Cuando el Estudiante ve que su solicitud fue aprobada, el mensaje que debe
  mostrarse es puramente textual: debe acercarse a la Decanatura de la FIET
  dentro de los cinco (5) días siguientes a firmar la Resolución físicamente.
  No hay ningún archivo adjunto en ese punto (ver `estudiante.html`, función
  de detalle, línea ~944).
- **Pendiente de decisión (próxima reunión):** aún no se sabe si en algún
  momento se subirá un **escaneo** de la Resolución ya firmada físicamente,
  como archivo digital para consulta. Mientras no se confirme, no implementes
  nada relacionado con esto; si el requerimiento lo pide, señálalo como punto
  abierto en vez de inventar una solución.

## 3. Modelo de estados (vocabulario propio del Estudiante)

El panel del Estudiante usa un vocabulario de estados **distinto** al del
Funcionario/Decano, para el mismo ciclo de vida del trámite:

- `En trámite`
- `Pendiente de pago` (solo Examen Supletorio)
- `En verificación de pago` (solo Examen Supletorio)
- `Aprobada`
- `Rechazada`

No hay todavía una tabla de mapeo centralizada entre este vocabulario y el
usado en Funcionario/Decano (`Pendiente`, `En Gestión`, `Pendiente de
Respuesta`, `Pendiente de Verificación`, `Respondida`). Si vas a tocar
estados, ten esto presente y no asumas que son intercambiables.

## 4. Menú del Estudiante

Tres ítems, igual que en los otros paneles:
1. **Mi Usuario**
2. **Solicitudes**
3. **Respuestas**

## 5. Los tres formularios de solicitud

Cada formulario comparte un bloque inicial de **datos del solicitante**, de
solo lectura, precargado de la sesión: Nombre, Programa, Semestre, Correo
institucional, Código, Facultad, Cédula (tipo y número de documento) y Fecha
de la solicitud.

### 5.1. Cancelación de Matrícula (`formulario-cancelacion-matricula.html`)
- Motivo de la cancelación: texto (mín. 50 caracteres, obligatorio) + soporte
  opcional.
- Listado de asignaturas del periodo (precargado, código + nombre).
- Seis documentos de paz y salvo (uno por entidad), cada uno con archivo PDF
  y fecha de expedición, vigencia máxima 5 días calendario:
  1. Paz y salvo — División de Bibliotecas (obligatorio)
  2. Paz y salvo — División de Deportes y Recreación (obligatorio)
  3. Paz y salvo — División de Salud Integral (obligatorio)
  4. Cupón de Confirmación de la Intervención Psicosocial (obligatorio)
  5. Paz y salvo — División Financiera (obligatorio)
  6. Carné estudiantil o constancia de no trámite — DARCA (opcional)
- Declaración de veracidad (checkbox obligatorio).
- **No genera Resolución en este formulario**: el pie del formulario aclara
  que el Decano determinará mediante resolución motivada la situación de
  cada asignatura — esa resolución es posterior y física, no un output del
  formulario.

### 5.2. Cancelación de Asignatura (`formulario-cancelacion-asignatura.html`)
- Motivo de la cancelación: texto (mín. 50 caracteres) + soporte opcional.
- Listado de asignaturas a cancelar (código + nombre), agregado por el
  Estudiante, al menos una obligatoria.
- Declaración de veracidad obligatoria.
- **No exige documentos de paz y salvo ni otros anexos obligatorios** — a
  diferencia de Cancelación de Matrícula. No agregues anexos obligatorios
  aquí por simetría con el otro formulario; es una diferencia real del
  proceso, no un vacío.

### 5.3. Examen Supletorio (`formulario-examen-supletorio.html`)
- Teléfono de contacto (obligatorio).
- Datos del examen no presentado: asignatura (obligatoria), grupo (opcional),
  código (obligatorio).
- Fechas: fecha del examen no presentado (dentro del plazo de 3 días
  hábiles) y fecha propuesta para el supletorio (no anterior a hoy).
- Firma del docente que orienta la asignatura: archivo obligatorio.
- Causa de la inasistencia, el Estudiante elige una de dos:
  - *Distinta a un cruce*: justificación en texto (mín. 50 caracteres) +
    documento opcional.
  - *Cruce con otro examen*: asignatura, grupo (opcional), código
    (obligatorio, distinto al del examen no presentado), fecha (igual a la
    del examen no presentado), hora, y firma del docente del cruce
    (archivo obligatorio).
- Declaración de veracidad obligatoria.
- **Es el único de los tres procesos con flujo de pago**: si el Decano
  aprueba, el Estudiante debe cargar un comprobante de pago (PDF/JPG/PNG,
  obligatorio) antes de que el Funcionario lo verifique y cierre el trámite.
  Este comprobante es un archivo real de la app — no confundir con la
  Resolución, que sigue siendo siempre física.
- Este proceso **no genera Resolución** en ningún punto (ver nota en
  `formulario-examen-supletorio.html` ~línea 1272): la decisión se comunica
  directamente al Estudiante.

## 6. Reglas transversales de los formularios

- Formatos aceptados: PDF, JPG y PNG para anexos y comprobantes (nunca para
  la Resolución, que no es un archivo).
- Los formularios que exigen adjuntos deben bloquear el envío y mostrar
  mensaje de validación si falta el archivo obligatorio.
- **Hay banderas de "modo prueba" activas por defecto** en los tres
  formularios (p. ej. `MODO_PRUEBA_SIN_ARCHIVOS`,
  `MODO_PRUEBA_PAZYSALVO_ADJUNTADOS`, `MODO_PRUEBA_SIN_DATOS`,
  `VALIDACION_ACTIVA = false` en Examen Supletorio) que desactivan
  validaciones para poder recorrer el formulario en demostraciones. No las
  quites por tu cuenta salvo que se te pida explícitamente prepararlo para
  entrega final.

## 7. Estilo visual (coherencia con el sistema JDCE)

| Elemento | Valor |
|---|---|
| Barra lateral | `#1B2660` |
| Barra superior | Blanca |
| Encabezado de tabla | `#2A3A86`, texto blanco |
| Filas | Cebra alternada |
| Acento (subrayado activo) | `#B23B2E` |

Tipografía sans-serif institucional. Sin sombras pronunciadas ni degradados.

## 8. Qué NO hacer aquí

- No generar, adjuntar, descargar ni simular la firma digital de una
  Resolución en ningún punto del panel del Estudiante o de los formularios.
- No inventar la respuesta al punto abierto del escaneo de la Resolución
  firmada — está pendiente de definir en la próxima reunión.
- No tocar archivos de las carpetas de Funcionario o Decano desde aquí.
- No agregar backend real ni reemplazar el bridge de `localStorage` sin que
  se pida explícitamente.