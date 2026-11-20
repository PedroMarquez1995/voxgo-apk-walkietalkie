Wavko
Comunicación por voz en tiempo real para Android.
Wavko es una aplicación de comunicación Push-to-Talk que permite hablar instantáneamente mediante canales, conectar con amigos y enviar mensajes de voz y texto.
Desarrollada con Kotlin y Cloudflare Workers.
Contenido
Características
Arquitectura
Instalación
Configuración de Cloudflare
Compilación del APK
Notificaciones push
Funcionamiento técnico
Seguridad
Actualizaciones
Historial de versiones
Características
Push-to-Talk: comunicación de voz en tiempo real.
Canales: crear y unirse a canales con o sin contraseña.
Amigos: contactos mediante códigos únicos.
Chat: mensajes de texto dentro de los canales.
Mensajes de voz: grabaciones privadas de hasta 20 segundos.
Cuentas: registro y recuperación de usuarios.
Seguridad: cifrado AES-256-GCM en canales protegidos.
Segundo plano: conexión con la pantalla apagada.
Notificaciones: invitaciones y mensajes mediante Firebase.
Personalización: temas, avatares y 16 skins.
Arquitectura
El proyecto utiliza una aplicación Android y un servidor alojado en Cloudflare.
wavko/
├── android/
├── cloudflare/
└── .github/
    └── workflows/
        ├── build-apk.yml
        └── deploy-cloudflare.yml
Tecnologías
Kotlin: aplicación Android.
Cloudflare Workers: servidor.
Durable Objects: administración de canales.
WebSocket: comunicación en tiempo real.
GitHub Actions: automatización de tareas.
Firebase Cloud Messaging: notificaciones push.
AES-256-GCM: cifrado de comunicaciones protegidas.
Instalación
1. Preparar el repositorio
Crea un repositorio en GitHub.
Sube el proyecto mediante Add file → Upload files.
Comprueba que estén las carpetas android/ y cloudflare/.
Comprueba que existan los workflows dentro de .github/workflows/.
Los workflows utilizados son:
build-apk.yml
deploy-cloudflare.yml
2. Configurar los secretos de GitHub
Entra en:
Settings → Secrets and variables → Actions
Añade estos secretos:
CLOUDFLARE_API_TOKEN: token de Cloudflare.
CLOUDFLARE_ACCOUNT_ID: identificador de tu cuenta.
ACCESS_KEY: clave de acceso opcional.
Configuración de Cloudflare
Accede al dashboard de Cloudflare.
Abre Workers & Pages.
Configura tu subdominio workers.dev, si es necesario.
Entra en My Profile → API Tokens.
Crea un token con permisos para editar Cloudflare Workers.
Copia tu Account ID.
Añade ambos valores a los secretos de GitHub.
Desplegar el servidor
En GitHub, abre:
Actions → Deploy Cloudflare Worker → Run workflow
El despliegue también se ejecuta automáticamente al realizar un push a la rama main.
La dirección del servidor tendrá este formato:
wss://voxgo.<tu-subdominio>.workers.dev
Esta dirección se utiliza en el campo Servidor de la aplicación.
Prueba local
Desde la carpeta cloudflare/, ejecuta:
npm install
npx wrangler dev
Para probar desde Android en la misma red, utiliza la dirección local de tu equipo:
ws://IP-DE-TU-PC:8787
Compilación del APK
La compilación se realiza mediante GitHub Actions.
Realiza un commit o push a main.
Accede a Actions.
Selecciona Build APK.
Espera a que finalice el proceso.
Busca la sección Artifacts.
Descarga wavko-apk.
Extrae el ZIP y localiza wavko.apk.
También puedes iniciar la compilación manualmente mediante Run workflow.
Instalación en Android
Transfiere el APK al dispositivo.
Autoriza la instalación de aplicaciones desconocidas.
Instala Wavko.
Abre la aplicación y configura tu perfil.
El APK generado es una versión debug firmada automáticamente, adecuada para uso personal y pruebas.
GitHub Releases
Para publicar una compilación mediante etiquetas:
git tag v1.0
git push --tags
El APK se publica en GitHub Releases para que pueda descargarse directamente desde el teléfono.
Notificaciones push
Wavko utiliza Firebase Cloud Messaging (FCM) de forma opcional.
Permite recibir notificaciones de invitaciones y mensajes de voz incluso cuando la aplicación está cerrada.
Configuración de Firebase
Crea un proyecto en Firebase Console.
Añade una aplicación Android con el paquete app.wavko.
Descarga google-services.json.
Guarda su contenido en el secreto GOOGLE_SERVICES_JSON.
En Firebase, accede a Configuración del proyecto → Cuentas de servicio.
Genera una clave privada.
Guarda el JSON completo en el secreto FCM_SERVICE_ACCOUNT.
Ejecuta Deploy Cloudflare Worker.
Ejecuta Build APK.
Sin Firebase, las funciones principales de Wavko continúan funcionando. Las notificaciones push opcionales no estarán disponibles; la aplicación seguirá avisando de la actividad de los amigos que estén conectados.
Funcionamiento técnico
Audio
PCM de 16 kHz mono.
Compresión G.711 µ-law.
Aproximadamente 128 kbps.
Paquetes de audio de 40 ms.
Un hablante simultáneo por canal.
Turnos de transmisión de hasta 60 segundos.
Liberación automática del turno después de 3 segundos sin audio.
Conectividad
Comunicación mediante WebSocket.
Gestión de canales mediante Durable Objects.
Reconexión automática.
Servicio Android en primer plano.
Conexión mantenida con la pantalla apagada.
Gestión de recursos
El servidor utiliza Durable Objects con API de hibernación para mantener conexiones abiertas sin consumir cómputo continuamente cuando los canales están inactivos.
Los mensajes WebSocket entrantes se contabilizan con una relación de 20:1 respecto a las solicitudes. Los límites dependen del plan de Cloudflare.
Seguridad
Wavko incorpora mecanismos de protección para las cuentas y las comunicaciones.
Cifrado en canales protegidos
PBKDF2 para derivación de claves.
AES-256-GCM para cifrado.
La clave de cifrado permanece en el dispositivo.
Los canales sin contraseña no utilizan cifrado de extremo a extremo.
Las invitaciones enviadas desde la aplicación pueden transportar la contraseña a través del servidor. Para una privacidad más estricta, comparte la contraseña mediante otro medio.
Protección de cuentas y canales
Claves de recuperación de 20 caracteres.
Solicitudes de amistad.
Bloqueo y desbloqueo de usuarios.
Expulsión de participantes.
Límite de cinco intentos incorrectos.
Bloqueo temporal de cinco minutos por IP o cuenta en ese canal.
Configuración adicional
Servidor predeterminado
La aplicación incluye esta dirección:
wss://voxgo.pedroxhile.workers.dev
Puede modificarse desde el campo Servidor de la aplicación.
Variables opcionales de GitHub
En Settings → Secrets and variables → Actions → Variables puedes configurar:
WAVKO_SERVER: cambia el servidor predeterminado del APK.
Si existe el secreto ACCESS_KEY, la clave también puede precargarse durante la compilación.
Importante: una clave precargada queda incorporada dentro del APK. No compartas ese archivo con personas que no deban tener acceso.
Actualizaciones
Para actualizar la aplicación, respeta este orden:
Actualiza el código del servidor.
Ejecuta Deploy Cloudflare Worker.
Espera a que termine el despliegue.
Ejecuta Build APK.
Instala la nueva versión en los dispositivos.
Las versiones con cambios incompatibles deben actualizarse en todos los dispositivos.
La migración de cuentas de la versión 2.2 requiere desplegar primero el Worker y después compilar el APK.
Historial de versiones
Wavko 2.3
Mensajes de voz, chat y notificaciones
Mensajes de voz de hasta 20 segundos.
Conservación durante 7 días, con un máximo de 30 mensajes.
Chat de texto dentro de los canales.
Cifrado del chat en canales protegidos.
Notificaciones push opcionales mediante Firebase.
Wavko 2.2
Seguridad y cuentas
Creación de cuentas reales.
Recuperación mediante claves de 20 caracteres.
Solicitudes de amistad.
Bloqueo y desbloqueo.
Expulsión de participantes.
Protección contra intentos incorrectos.
Cifrado AES-256-GCM.
Derivación de claves PBKDF2.
Wavko 2.1
Invitaciones y personalización
Invitaciones directas a canales.
Mejoras para segundo plano.
Audio HD experimental mediante Opus.
Temas claro, oscuro y automático.
16 skins.
Canales recientes y favoritos.
Español e inglés.
Eliminación del servidor Node.js alternativo.
Wavko 2.0
Nueva interfaz y gestión de contactos
Navegación mediante pestañas.
Sistema de amigos.
Códigos únicos de usuario.
Perfil y avatar personalizable.
Ajustes de conexión.
Widget Push-to-Talk.
Wavko — Comunicación instantánea. Sin complicaciones.
