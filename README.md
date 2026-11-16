# OPTI TIRUX

Optimizador de Windows para escritorio (Electron). Ajustes reversibles de sistema orientados a rendimiento en juegos, con perfiles, análisis de hardware y herramientas de diagnóstico.

**Plataforma:** Windows 10 / 11 (x64)  
**Versión:** 1.8.4

No modifica Windows Defender, Firewall, VBS ni HVCI.

---

## Requisitos

- Windows 10 u 11 de 64 bits
- Permisos de administrador para aplicar cambios de sistema
- Node.js 22.12+ únicamente si construyes desde el código fuente
- Conexión a Internet en la primera instalación de dependencias

---

## Instalación

### Uso recomendado

1. Extrae o clona el proyecto.
2. Ejecuta `INSTALAR_RAPIDO.bat` (primera vez).
3. En siguientes arranques usa `INICIAR.bat`.

| Archivo | Función |
|---------|---------|
| `INSTALAR_RAPIDO.bat` | Instala dependencias de ejecución y abre la aplicación |
| `INICIAR.bat` | Inicia sin reinstalar |
| `COMPILAR_SETUP.bat` | Genera el instalador NSIS (`.exe`) |

### Desde terminal

```bash
npm install --omit=dev
npm start
```

### Compilar instalador

```bash
npm install
npm run dist
```

Salida: `release/OPTI-TIRUX-Setup-<version>.exe`

---

## Funcionalidad

- **Optimizador:** tweaks de registro, servicios y energía con copia de seguridad y reversión
- **Perfiles:** BAJO, MEDIO, ALTO, FORTNITE, MAX, OWNER, PING
- **Analizar PC:** lectura de hardware y recomendación de perfil
- **Modo Gaming:** preparación de sesión y cierre opcional de aplicaciones en segundo plano
- **Ping:** parámetros de red orientados a latencia
- **Restore Center:** deshacer cambios aplicados por la aplicación
- **ADM:** panel avanzado local (contraseña almacenada con scrypt)
- **Comunidad:** intercambio local de perfiles de configuración

Los niveles premium se desbloquean con claves privadas definidas en el código de la aplicación. No se documentan en este repositorio.

---

## Estructura

```
electron/          Proceso principal, IPC, motor de tweaks y análisis
src/               Interfaz (HTML/JS/CSS) y recursos
INSTALAR_RAPIDO.bat
INICIAR.bat
COMPILAR_SETUP.bat
package.json
```

---

## Scripts npm

| Comando | Descripción |
|---------|-------------|
| `npm start` | Ejecutar en desarrollo |
| `npm run dist` | Build instalador NSIS x64 |
| `npm run dist:all` | NSIS + portable |
| `npm run pack` | Empaquetado sin instalador |

---

## Repositorio

```bash
git clone https://github.com/<usuario>/OPTI-TIRUX.git
cd OPTI-TIRUX
npm install --omit=dev
npm start
```

Ajusta en `package.json` el bloque `build.publish` si el remoto no coincide con el predeterminado.

---

## Notas

- Los resultados en FPS dependen del hardware, drivers y del juego.
- Algunos cambios requieren cerrar sesión o reiniciar.
- En portátiles, los perfiles agresivos aumentan consumo y temperatura.
- Usa Restore Center si deseas deshacer cambios.

---

## Licencia

UNLICENSED. Uso según indiquen los autores del proyecto.


