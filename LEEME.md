# Producción Ateneo · Scope — puesta en marcha

App web (PWA) de un solo `index.html`, para GitHub Pages + Firebase Realtime Database + acceso con Google.
Si abres `index.html` sin configurar nada, arranca en **modo prueba**: usa eventos de ejemplo y guarda los datos solo en tu navegador.

## Archivos
- `index.html` — la app completa. Lleva dentro los inventarios iniciales, la plantilla de la hoja de servicio y JSZip.
- `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png` — para instalarla como app.

## 1. Firebase
1. Crea un proyecto en Firebase (o usa uno de Scope).
2. **Authentication → Sign-in method → Google**: actívalo. En *Settings → Authorized domains*, añade `TUUSUARIO.github.io`.
3. **Realtime Database**: créala (región europe-west1) y pega estas reglas (ya llevan los 5 correos):

```json
{
  "rules": {
    "ateneo": {
      ".read":  "auth != null && auth.token.email.matches(/^(asanmartinscope@gmail\\.com|pperez@scopeproducciones\\.com|hramosscopeproducciones@gmail\\.com|ssalas37@gmail\\.com|jmpazscopeproducciones@gmail\\.com)$/)",
      ".write": "auth != null && auth.token.email.matches(/^(asanmartinscope@gmail\\.com|pperez@scopeproducciones\\.com|hramosscopeproducciones@gmail\\.com|ssalas37@gmail\\.com|jmpazscopeproducciones@gmail\\.com)$/)"
    }
  }
}
```

4. En *Configuración del proyecto → Tus apps → Web*, copia el objeto `firebaseConfig` y pégalo en `index.html`:

```js
const FIREBASE_CONFIG = { apiKey:"…", authDomain:"…", databaseURL:"https://…europe-west1.firebasedatabase.app", projectId:"…", appId:"…" };
const ALLOWED_EMAILS = [ … ya están puestos los 5 correos … ];
```

La primera vez que alguien entra, la app carga sola el inventario del Ateneo y el de Scope.

## 2. Google Cloud (Calendar + Drive)
En el **mismo proyecto** de Google Cloud que crea Firebase:
1. *APIs y servicios → Biblioteca*: activa **Google Calendar API** y **Google Drive API**.
2. *Pantalla de consentimiento OAuth*:
   - Como hay cuentas @gmail además de @scopeproducciones.com → tipo **Externo**, en estado *Testing*, y añade los 5 correos como *usuarios de prueba*. (Al entrar saldrá un aviso de «app no verificada»: pulsar *Continuar*.)
   - Permisos: `calendar.readonly` y `drive`.
3. Cada una de las 5 personas debe **tener acceso al calendario del Ateneo** (compartido con su cuenta) y **permiso de edición en la carpeta de Drive** de las hojas.

## 3. Publicar
Sube los archivos a un repositorio y activa GitHub Pages, como en las otras apps.

## 4. Primeros ajustes (pestaña Ajustes)
- **Carpeta de Drive**: pega el enlace de la carpeta. Las hojas se guardan en subcarpetas por mes (`2026-11 NOVIEMBRE`), con el nombre de la plantilla: `20_NOV_V_19H_CÁTEDRA MAYOR_Hoja_de_Servicio`.
- **Códigos de sala**: [CAT] y [CCH] ya están asignados. Los demás (RYC, PG, UBE, LAR, BIB, PEC, EXP) son una suposición: corrígelos. Cualquier código nuevo que llegue del calendario aparece ahí para asignarlo.
- **Formato en Drive**: documento de Google (por defecto) o .docx.
- **Reserva por defecto**: citación 60 min antes y desmontaje 30 min después. Cada acto se puede ajustar.

## Cómo funciona
- **Sincronización**: lee el calendario al abrir la app y cada 10 min. También se puede lanzar con el botón. Los eventos se guardan en Firebase, así que los 5 veis lo mismo aunque solo uno sincronice. Los actos cancelados en el calendario se marcan, no se borran.
- **Comunicación**: quien lleva los emails pega la información en el acto y queda como *pendiente*. En *Hoja de servicio* el técnico la ve al lado del formulario y la marca como *pasada a la hoja* cuando la ha esquematizado.
- **Material**: cada acto reserva el equipo desde la citación hasta el fin del desmontaje. La app avisa en rojo si se reserva más de lo que hay a la misma hora. Avisa en naranja si se traslada equipo de una sala que tiene acto a la vez. En *Material* tienes «¿Qué hay libre?», «Traslados» y «Oficina y alquiler» de los próximos 14 días.
- **Inventario**: se edita en la app. Para dejar fuera de las reservas un equipo roto o perdido, ponle estado AVERIADO, DESAPARECIDO o NO ESTAN.
- **Copias de seguridad**: Ajustes → Exportar copia (JSON). El inventario también se puede exportar a CSV.

## Notas sobre los datos importados
- Inventario de Scope: las líneas con LOCALIZACIÓN «SCOPE» se han importado como **Scope oficina**, y el resto como **Scope en Ateneo**. Las líneas idénticas que solo cambian de nº de serie se han agrupado (129 → 114 líneas).
- Equipo del Ateneo: «BIBLIOTECA / SALA PARDO BAZÁN» se ha importado como BIBLIOTECA. PECERA BIBLIOTECA y EXPO ANSELMA están en la plantilla pero no tienen equipo propio en el Excel.
