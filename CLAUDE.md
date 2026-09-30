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

## 2. DECISIÓN CLAVE — la Resolución nunca se firma digitalmente, pero su escaneo sí es descargable (CM/CA)

Por reglamento de la Universidad del Cauca **no se admiten firmas digitales**:
la Resolución (documento que aprueba o rechaza el trámite) siempre se firma
de forma física, nunca en la aplicación. **Decisión ya validada con el
usuario (2026-09-30):** una vez firmada físicamente, el Funcionario Académico
sube su escaneo (PDF) y el Estudiante puede descargarlo desde la app. Esto
resuelve el punto que antes quedaba pendiente de una próxima reunión.

- Aplica **solo** a Cancelación de Matrícula y Cancelación de Asignatura.
  **Examen Supletorio nunca genera Resolución** y nunca debe mostrar ni pedir
  este documento en ningún diálogo o vista de solo lectura.
- Aplica tanto si el trámite terminó **Aprobada** como **Rechazada** (en
  ambos casos puede o no haber `r.resolucion`; si no hay, se muestra el
  mensaje textual de que aún no está disponible el escaneo). Cuando el
  trámite fue Rechazada, el escaneo de la Resolución se muestra **junto
  con** el motivo del rechazo, no en su lugar (ver `index.html`, función
  `abrirDetallePagina`, bloque "Resultado").
- Lo que sigue sin existir es la firma digital en sí: no se genera la
  Resolución desde la app, no se firma electrónicamente, y no se simula ese
  paso. Solo se digitaliza (escanea) una Resolución que ya fue firmada en
  papel.
- Formatos aceptados para este escaneo: igual que otros anexos (PDF
  principalmente).

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
  cada asignatura — esa resolución se firma físicamente después, y su
  escaneo (si el Funcionario lo sube) se consulta luego en el módulo de
  Respuestas (ver sección 2).

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
  Resolución, que se firma siempre de forma física.
- Este proceso **no genera Resolución** en ningún punto (ver nota en
  `formulario-examen-supletorio.html` ~línea 1272): la decisión se comunica
  directamente al Estudiante. A diferencia de Cancelación de Matrícula y de
  Asignatura, aquí tampoco aplica el escaneo descargable de la Resolución
  (sección 2): Examen Supletorio nunca tiene ese documento.

## 6. Reglas transversales de los formularios

- Formatos aceptados: PDF, JPG y PNG para anexos y comprobantes. La
  Resolución solo se maneja como PDF (escaneo de la firma física) y solo
  aplica a Cancelación de Matrícula y Cancelación de Asignatura (ver
  sección 2).
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

- No simular ni generar una firma digital de la Resolución: la Resolución
  siempre se firma físicamente primero; la app solo permite consultar su
  escaneo posterior (CM/CA, sección 2). No agregues botones de "Generar
  Resolución" ni de firma electrónica.
- No mostrar ni pedir Resolución para Examen Supletorio, en ningún diálogo
  ni vista de solo lectura — ese proceso nunca la genera.
- No tocar archivos de las carpetas de Funcionario o Decano desde aquí.
- No agregar backend real ni reemplazar el bridge de `localStorage` sin que
  se pida explícitamente.