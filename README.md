# OPTI-TIRUX # OPTI TIRUX 1.2 — Windows x64

Aplicación de escritorio Electron para diagnóstico local, control energético prudente y herramientas de análisis de Windows. No es un “FPS booster” mágico: cada acción indica qué cambia y evita desactivar protecciones o servicios esenciales.

## Requisitos

- Windows 10/11 de 64 bits.
- Node.js 22.12 o superior (Node.js 24 LTS recomendado) para construir desde el código.
- Conexión a Internet durante la primera instalación para descargar dependencias de npm y Electron.
- Para capturas ETW DPC/ISR: Windows Performance Recorder disponible y ejecutar OPTI TIRUX como administrador.
- Para abrir y analizar los `.etl`: Windows Performance Analyzer (WPA), incluido en Windows Performance Toolkit/Windows ADK.

## Instalación automática

1. Extrae todo el ZIP en una carpeta local.
2. Haz doble clic en `INSTALAR_OPTI_TIRUX.bat`.
3. El asistente comprueba Windows x64 y Node.js 22.12+, instala Node.js LTS mediante `winget` si falta y está disponible, instala dependencias y ejecuta `npm run dist`.
4. Se abrirá `release/OPTI-TIRUX-Setup-1.2.0.exe` (nombre exacto según versión).
5. Completa el instalador. Se crean accesos directos de Inicio y Escritorio.

El instalador no necesita elevar la CMD para compilar. Si `winget` no está disponible, instala Node.js LTS desde https://nodejs.org/en/download y vuelve a ejecutar el BAT.

## Desarrollo manual

```powershell
npm install
npm start
npm run dist
```

El instalador NSIS se genera dentro de `release/`.

## Funciones conectadas

### Dashboard y Monitor

- Lecturas locales de CPU, carga, memoria, GPU/controladores gráficos cuando el sensor los expone, discos, temperatura cuando está disponible y número de procesos.
- Los valores no disponibles se muestran como N/D. FPS, 1% lows y frametime no se inventan: requieren una captura de PresentMon/CapFrameX aún no integrada.

### Process Manager

- Lista de procesos, PID, uso de CPU/RAM, estado y número de hilos.
- Prioridad de proceso limitada a Below Normal, Normal y Above Normal. No existe Realtime.
- No permite modificar procesos críticos del sistema.
- Guarda la prioridad original, la restaura al salir y vuelve a intentarlo en el siguiente arranque si la app se cerró inesperadamente. Si el PID ya pertenece a otro proceso, omite la restauración.
- No cambia prioridades de hilos individuales ni termina procesos.

### ADM privado

- En el primer uso pide crear una contraseña local de al menos 10 caracteres. Guarda un hash scrypt con salt aleatorio; no guarda la contraseña en texto.
- Inventario de drivers PnP firmados, versiones, fechas, fabricante y dispositivos con estado anómalo.
- Abre Windows Update y su sección de actualizaciones opcionales para revisar drivers. No descarga ni instala controladores automáticamente.
- Inventario de aplicaciones de inicio y acceso a la página nativa de Windows para gestionarlas manualmente.
- Planes de energía reales mediante `powercfg`.
- Crea un clon del plan Equilibrado llamado `OPTI TIRUX Gaming (seguro)`: CPU mínimo 5% y máximo 100% conectado a corriente; mínimo 5% y máximo 85% con batería. No modifica voltajes ni límites térmicos. El plan se puede activar y eliminar desde ADM; no elimina planes originales.
- Guarda el plan de energía previo para poder restaurarlo incluso tras reiniciar la aplicación.
- Timer Consistency: consulta la resolución actual del temporizador con `NtQueryTimerResolution` y mide 500.000 intervalos QPC/Stopwatch en código nativo C#. Informa mediana, P95, P99, máximo y deltas cero. Es una medición de jitter de muestreo, no una medida de DPC ni una predicción de FPS; repite en condiciones comparables.
- DPC/ISR: inicia una traza ETW con el perfil General de WPR y guarda el `.etl` en Documentos. El perfil General incluye las palabras clave de kernel DPC e Interrupt. La captura no identifica automáticamente al driver culpable; se analiza en WPA.
- Exporta un informe técnico JSON local y conserva un historial local de acciones.

### Perfiles

- BAJO: activa el plan Equilibrado de Windows.
- MEDIO: activa Alto rendimiento si está instalado; si no, Equilibrado.
- ALTO: crea/activa el clon seguro Gaming de OPTI TIRUX.
- ADM: abre el laboratorio avanzado protegido.

Los perfiles BAJO/MEDIO/ALTO aplican actualmente el plan de energía correspondiente; no ejecutan una batería de tweaks ocultos. No desactivan Defender, Firewall, VBS, HVCI, servicios críticos, red o audio.

### Cleaner

- Analiza archivos temporales del usuario con más de 7 días.
- Omite enlaces simbólicos, carpetas, archivos recientes e inaccesibles.
- Pide confirmación antes de borrar. La eliminación de temporales no es reversible.

## Ubicaciones

- Informes: `Documentos\OPTI TIRUX\Reports`
- Capturas ETL: `Documentos\OPTI TIRUX\Diagnostics`
- Datos locales de aplicación: `%APPDATA%\opti-tirux`
- La contraseña ADM se guarda en `adm-password.json` como hash con salt. Si se olvida, cierra la aplicación y elimina ese archivo para configurar una nueva contraseña en el siguiente acceso. No compartas la carpeta Diagnostics ni los ETL sin revisarlos: pueden contener nombres de procesos y rutas locales.

## Seguridad y límites conocidos

- Electron usa `contextIsolation`, `sandbox` y `nodeIntegration: false`; el renderer solo recibe métodos explícitos por preload.
- Las acciones de sistema usan comandos permitidos y argumentos validados; no se ejecuta texto arbitrario desde la interfaz.
- No se desactiva Microsoft Defender, Firewall, VBS/HVCI, UAC ni servicios de Windows.
- No se cambian valores de registro, voltajes, límites térmicos, BCD, HPET ni políticas de temporizador.
- DPC/ISR requiere privilegios de administrador. WPR General puede generar trazas grandes; captura solo 60–120 segundos alrededor del problema y detén la grabación al terminar.
- Timer Consistency y DPC/ISR son pruebas distintas. Un resultado de QPC no es una puntuación de latencia del juego.
- No hay todavía captura FPS/frametime/1% low ni análisis automático de `.etl`/driver. Para eso se necesita integrar y validar una versión concreta de PresentMon y WPA.

## Verificación de esta entrega

Se valida la sintaxis JavaScript de Electron/preload/integración y la integridad del ZIP desde el entorno de construcción. No se afirma que la app haya sido ejecutada en un Windows real en este entorno. La prueba final de WPR, `powercfg`, sensores y permisos debe hacerse en Windows 10/11.
