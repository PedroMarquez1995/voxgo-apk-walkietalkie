Wavko

Comunicación por voz en tiempo real para Android.

Wavko es una aplicación de comunicación Push-to-Talk que permite hablar instantáneamente mediante canales, conectar con amigos y enviar mensajes de voz y texto.

Desarrollada con Kotlin y Cloudflare Workers.

"Android" (https://img.shields.io/badge/Android-Kotlin-3DDC84?logo=android&logoColor=white)
"Backend" (https://img.shields.io/badge/Backend-Cloudflare-F38020?logo=cloudflare&logoColor=white)
"Version" (https://img.shields.io/badge/Version-2.3-blue)
"License" (https://img.shields.io/badge/License-Personal-lightgrey)

---

Contenido

- "Características" (#características)
- "Arquitectura" (#arquitectura)
- "Instalación" (#instalación)
- "Configuración de Cloudflare" (#configuración-de-cloudflare)
- "Compilación del APK" (#compilación-del-apk)
- "Notificaciones push" (#notificaciones-push)
- "Funcionamiento técnico" (#funcionamiento-técnico)
- "Seguridad" (#seguridad)
- "Actualizaciones" (#actualizaciones)
- "Historial de versiones" (#historial-de-versiones)

---

Características

Función| Descripción
Push-to-Talk| Comunicación de voz en tiempo real.
Canales| Crear y unirse a canales con o sin contraseña.
Amigos| Contactos mediante códigos únicos.
Chat| Mensajes de texto dentro de los canales.
Mensajes de voz| Grabaciones privadas de hasta 20 segundos.
Cuentas| Registro y recuperación de usuarios.
Seguridad| Cifrado AES-256-GCM en canales protegidos.
Segundo plano| Conexión con la pantalla apagada.
Notificaciones| Invitaciones y mensajes mediante Firebase.
Personalización| Temas, avatares y 16 skins.

Arquitectura

El proyecto utiliza una arquitectura compuesta por una aplicación Android y un servidor alojado en Cloudflare.

wavko/
├── android/
├── cloudflare/
└── .github/
    └── workflows/
        ├── build-apk.yml
        └── deploy-cloudflare.yml

Tecnologías

- Kotlin: aplicación Android.
- Cloudflare Workers: servidor.
- Durable Objects: administración de canales.
- WebSocket: comunicación en tiempo real.
- GitHub Actions: automatización.
- Firebase Cloud Messaging: notificaciones.
- AES-256-GCM: cifrado de comunicaciones protegidas.

Instalación

1. Preparar el repositorio

1. Crea un repositorio en GitHub.
2. Sube el archivo ZIP del proyecto mediante "Add file → Upload files".
3. Crea los siguientes archivos:

.github/workflows/build-apk.yml
.github/workflows/deploy-cloudflare.yml

4. Copia en ellos el contenido de los workflows correspondientes.

Los workflows pueden descomprimir el proyecto automáticamente. También puedes subir las carpetas directamente.

2. Configurar los secretos

En GitHub, accede a:

"Settings → Secrets and variables → Actions"

Crea los siguientes secretos:

Secreto| Descripción
"CLOUDFLARE_API_TOKEN"| Token de Cloudflare.
"CLOUDFLARE_ACCOUNT_ID"| Identificador de cuenta.
"ACCESS_KEY"| Clave de acceso opcional.

Configuración de Cloudflare

1. Accede al dashboard de Cloudflare.
2. Abre Workers & Pages.
3. Configura tu subdominio "workers.dev", si es necesario.
4. Entra en My Profile → API Tokens.
5. Crea un token utilizando la plantilla "Edit Cloudflare Workers".
6. Copia tu Account ID.
7. Añade ambos valores a los secretos de GitHub.

Desplegar el servidor

En GitHub:

"Actions → Deploy Cloudflare Worker → Run workflow"

El despliegue también se ejecuta automáticamente al realizar un push a "main".

La dirección del servidor tendrá este formato:

wss://voxgo.<tu-subdominio>.workers.dev

Esta dirección se utiliza en el campo Servidor de la aplicación.

Prueba local

cd cloudflare
npm install
npx wrangler dev

En Android puedes utilizar:

ws://IP-DE-TU-PC:8787

Compilación del APK

La compilación se realiza mediante GitHub Actions.

1. Realiza un commit o push a "main".
2. Accede a Actions.
3. Selecciona Build APK.
4. Espera a que termine el proceso.
5. Busca la sección Artifacts.
6. Descarga "wavko-apk".
7. Extrae el archivo ZIP y localiza "wavko.apk".

También puedes iniciar la compilación manualmente mediante Run workflow.

Instalación en Android

1. Transfiere el APK al dispositivo.
2. Autoriza la instalación de aplicaciones desconocidas.
3. Instala Wavko.
4. Abre la aplicación y configura tu perfil.

El APK generado es una versión debug firmada automáticamente, adecuada para uso personal y pruebas.

GitHub Releases

Para publicar una compilación mediante etiquetas:

git tag v1.0
git push --tags

El APK se publica en GitHub Releases, permitiendo descargarlo directamente desde el teléfono.

Notificaciones push

Wavko utiliza Firebase Cloud Messaging (FCM) de forma opcional.

Permite recibir notificaciones de invitaciones y mensajes de voz incluso cuando la aplicación está cerrada.

Configuración

1. Crea un proyecto en "Firebase Console" (https://console.firebase.google.com).
2. Añade una aplicación Android con el paquete "app.wavko".
3. Descarga "google-services.json".
4. Guarda su contenido en el secreto "GOOGLE_SERVICES_JSON".
5. En Firebase, accede a Configuración del proyecto → Cuentas de servicio.
6. Genera una clave privada.
7. Guarda el JSON completo en el secreto "FCM_SERVICE_ACCOUNT".
8. Ejecuta Deploy Cloudflare Worker.
9. Ejecuta Build APK.

Sin Firebase, las funciones principales de Wavko continúan funcionando. Las notificaciones push opcionales no estarán disponibles.

Funcionamiento técnico

Audio

- PCM de 16 kHz mono.
- Compresión G.711 µ-law.
- Aproximadamente 128 kbps.
- Paquetes de audio de 40 ms.
- Un hablante simultáneo por canal.
- Turnos de transmisión de hasta 60 segundos.
- Liberación automática del turno después de 3 segundos sin audio.

Conectividad

- Comunicación mediante WebSocket.
- Gestión de canales mediante Durable Objects.
- Reconexión automática.
- Servicio Android en primer plano.
- Conexión mantenida con la pantalla apagada.

Gestión de recursos

El servidor utiliza Durable Objects con API de hibernación para mantener conexiones abiertas sin consumir cómputo continuamente cuando los canales están inactivos.

Los mensajes WebSocket entrantes se contabilizan con una relación de 20:1 respecto a las solicitudes. Los límites dependen del plan de Cloudflare.

Seguridad

Wavko incorpora diferentes mecanismos de protección.

Cifrado en canales protegidos:

- PBKDF2 para derivación de claves.
- AES-256-GCM para cifrado.
- Separación entre la prueba de contraseña y la clave de cifrado.
- La clave de cifrado permanece en el dispositivo.

Protección de cuentas y canales:

- Claves de recuperación de 20 caracteres.
- Solicitudes de amistad.
- Bloqueo y desbloqueo de usuarios.
- Expulsión de participantes.
- Límite de cinco intentos incorrectos.
- Bloqueo temporal de cinco minutos por IP o cuenta en ese canal.

Los canales sin contraseña no utilizan cifrado de extremo a extremo.

Las invitaciones enviadas desde la aplicación pueden transportar la contraseña a través del servidor. Para privacidad estricta, compártela mediante otro medio.

Configuración adicional

Servidor predeterminado

La aplicación incluye esta dirección:

wss://voxgo.pedroxhile.workers.dev

Puede modificarse desde el campo Servidor de la aplicación.

Variables opcionales de GitHub

En Settings → Secrets and variables → Actions → Variables puedes configurar:

Variable| Función
"WAVKO_SERVER"| Cambia el servidor predeterminado del APK.

Si existe el secreto "ACCESS_KEY", la clave también puede precargarse durante la compilación.

Importante: una clave precargada queda incorporada dentro del APK. No compartas ese archivo con personas que no deban tener acceso.

Actualizaciones

Para actualizar la aplicación, respeta este orden:

1. Actualizar el código del servidor.
2. Ejecutar Deploy Cloudflare Worker.
3. Esperar a que termine el despliegue.
4. Ejecutar Build APK.
5. Instalar la nueva versión en los dispositivos.

Las versiones con cambios incompatibles deben actualizarse en todos los dispositivos.

En particular, la migración de cuentas de la versión 2.2 requiere desplegar primero el Worker y después compilar el APK.

Historial de versiones

Wavko 2.3

Mensajes de voz, chat y notificaciones

- Mensajes de voz de hasta 20 segundos.
- Conservación durante 7 días, máximo 30 mensajes.
- Chat de texto dentro de los canales.
- Cifrado del chat en canales protegidos.
- Notificaciones push opcionales mediante Firebase.

Wavko 2.2

Seguridad y cuentas

- Creación de cuentas reales.
- Recuperación mediante claves de 20 caracteres.
- Solicitudes de amistad.
- Bloqueo y desbloqueo.
- Expulsión de participantes.
- Protección contra intentos incorrectos.
- Cifrado AES-256-GCM.
- Derivación de claves PBKDF2.

Wavko 2.1

Invitaciones y personalización

- Invitaciones directas a canales.
- Mejoras para segundo plano.
- Audio HD experimental mediante Opus.
- Temas claro, oscuro y automático.
- 16 skins.
- Canales recientes y favoritos.
- Español e inglés.
- Eliminación del servidor Node.js alternativo.

Wavko 2.0

Nueva interfaz y gestión de contactos

- Navegación mediante pestañas.
- Sistema de amigos.
- Códigos únicos de usuario.
- Perfil y avatar personalizable.
- Ajustes de conexión.
- Widget Push-to-Talk.

---

<p align="center">Wavko

Comunicación instantánea. Sin complicaciones.

</p>
