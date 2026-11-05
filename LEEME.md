# Registro Horario — ControlPark

App de control horario propia para Controlpark Eventos S.L.: los empleados fichan desde el móvil y la administración gestiona equipo, centros, turnos, solicitudes, informes y auditoría.

- **Web:** https://abarriosmora04-dev.github.io/Registro-horario/
- **Coste:** 0 € (GitHub Pages + plan gratuito *Spark* de Firebase).
- **Datos:** Firebase (Google Cloud), región Europa.

---

## Puesta en marcha (una sola vez, ~15 minutos)

### 1. Crear el proyecto Firebase
1. Entra en https://console.firebase.google.com con la cuenta de Google de la empresa.
2. **Crear un proyecto** → nombre `controlpark-registro` → desactiva Google Analytics → Crear.

### 2. Activar el inicio de sesión
1. Menú izquierdo → **Compilación → Authentication → Comenzar**.
2. Pestaña **Método de acceso** → **Correo electrónico/contraseña** → Habilitar → Guardar.
3. Pestaña **Configuración → Dominios autorizados** → **Agregar dominio** → `abarriosmora04-dev.github.io`.
4. (Opcional) Pestaña **Plantillas** → cambia el idioma a español para los emails de contraseña.

### 3. Crear la base de datos
1. **Compilación → Firestore Database → Crear base de datos**.
2. Ubicación: **europe-southwest1 (Madrid)**. *No se puede cambiar después.*
3. Elige **modo de producción** → Crear.
4. Pestaña **Reglas** → borra lo que haya → pega el contenido completo de [`firestore.rules`](firestore.rules) → **Publicar**.

### 4. Conectar la web con Firebase
1. Rueda dentada (arriba a la izquierda) → **Configuración del proyecto** → **Tus apps** → icono web `</>`.
2. Apodo: `registro-web` → **Registrar app** (no marques Hosting).
3. Copia el bloque `firebaseConfig` que aparece.
4. En GitHub, abre [`firebase-config.js`](firebase-config.js) → lápiz ✏️ → sustituye el objeto por el que has copiado → **Commit changes**.
5. Espera 1–2 minutos a que GitHub Pages publique.

### 5. Primer acceso
1. Abre la web. Aparecerá **Configuración inicial** con los datos de la empresa ya rellenos.
2. Crea tu cuenta de administrador. Esta pantalla solo funciona la primera vez.
3. En **Admin → Más → Centros** crea los centros (pulsa “Usar mi ubicación actual” estando en el sitio para fijar las coordenadas).
4. En **Admin → Equipo → + Alta** da de alta a cada empleado. La app genera una contraseña temporal para entregársela, o le puedes enviar un email para que elija la suya.

> La `apiKey` de Firebase no es secreta: va en el código de cualquier web que usa Firebase. Lo que protege los datos son las reglas de `firestore.rules`.

---

## Qué hace

**Empleado (móvil)**
- Fichar entrada, pausa, reanudar y salida. La hora la pone el servidor de Google, no el teléfono.
- Ubicación solo en el instante de fichar, con aviso informativo previo (RGPD).
- Ver su turno de hoy y su horario de las próximas 4 semanas.
- Historial mensual con horas trabajadas, pausas y horas extra; descarga en PDF.
- Solicitar correcciones (olvidos de fichaje) y ausencias o vacaciones.
- Revisar y firmar el resumen mensual (conforme / no conforme).
- Instalable como app: menú del navegador → *Añadir a pantalla de inicio*.

**Administración**
- **Panel “Ahora mismo”:** quién está trabajando, en pausa, quién tiene turno y no ha fichado, ausentes e incidencias (olvidos de salida).
- **Equipo:** altas, edición, bajas y reactivaciones. Pensado para fijos discontinuos: cada temporada se reactiva y queda el historial de periodos.
- **Centros de trabajo** con radio de fichaje: marca los fichajes hechos fuera de zona.
- **Turnos:** cuadrante semanal, repetir de lunes a viernes, copiar la semana anterior y turnos nocturnos que pasan de medianoche.
- **Solicitudes:** aprobar o rechazar correcciones y ausencias.
- **Registros:** jornada día a día de cada empleado, corrección directa con motivo y cierre mensual con huella SHA-256.
- **Informes:** resumen de la plantilla, PDF por empleado o de todos y CSV para la Inspección de Trabajo.
- **Auditoría:** todas las acciones (accesos, fichajes, correcciones, altas y bajas, exportaciones).
- **Ajustes:** datos de empresa, modo de geolocalización (desactivada, registrar u obligatoria) y jornada por defecto.

---

## Cumplimiento normativo

| Requisito | Cómo se cumple |
|---|---|
| Registro diario de inicio y fin de jornada (art. 34.9 ET, RDL 8/2019) | Fichajes de entrada y salida por trabajador y día |
| Conservación 4 años | Los registros no se pueden borrar desde la app; los informes indican la fecha límite |
| A disposición de trabajador, representantes e Inspección | El trabajador ve y descarga su historial; la empresa exporta PDF y CSV |
| Horas extra totalizadas mensualmente con copia al trabajador (art. 35.5 ET) | Cierre mensual con horas extra y forma de compensación, firmado por el trabajador |
| Inalterabilidad y trazabilidad (borrador del nuevo Real Decreto) | Hora del servidor; nadie, ni el administrador, puede editar ni borrar un fichaje; cada corrección es un registro nuevo con autor, fecha y motivo; auditoría de solo escritura |
| Pausas, centro de trabajo, identificación del trabajador | Registradas en cada fichaje |
| Sin biometría | Acceso con usuario y contraseña |
| Geolocalización proporcional (RGPD, art. 90 LOPDGDD) | Solo en el momento de fichar, con información previa aceptada y registrada |

**Estado del nuevo Real Decreto de registro digital (noviembre 2026):** aún no publicado en el BOE. Cuando se publique, habrá que revisar si exige un formato concreto de acceso remoto para la Inspección (el borrador hablaba de una API). La exportación CSV cubre hoy los datos que pide.

### Tareas de la empresa (fuera de la app)
- [ ] Informar por escrito a la plantilla (o a su representación legal) del sistema de registro y de la geolocalización.
- [ ] Añadir este tratamiento al Registro de Actividades de Tratamiento (RGPD art. 30).
- [ ] Firmar el contrato de encargado del tratamiento de Google Cloud (en Firebase Console → Configuración del proyecto, apartado de privacidad / *Data Processing Terms*).
- [ ] Pasados 4 años, eliminar registros antiguos desde Firebase Console si se desea.

*Esto es información orientativa, no asesoramiento jurídico.*

---

## Límites del plan gratuito
50.000 lecturas y 20.000 escrituras al día y 1 GB de almacenamiento: holgado para una plantilla de temporada. Si algún día se supera, la app deja de funcionar hasta el día siguiente sin perder datos; se puede activar el plan de pago por uso (céntimos al mes).

## Archivos
- `index.html`: la app completa.
- `firebase-config.js`: conexión con tu proyecto Firebase.
- `firestore.rules`: reglas de seguridad (pegar en Firebase).
- `manifest.webmanifest`, `icon.svg`: instalación como app en el móvil.
