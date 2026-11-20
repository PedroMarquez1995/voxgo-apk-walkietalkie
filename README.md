Escritura
🎙️ Wavko
Comunicación por voz en movimiento.
Wavko es una aplicación de comunicación por voz estilo walkie-talkie, inspirada en Zello. Permite hablar en tiempo real con otras personas mediante canales, utilizando el sistema Push-to-Talk (PTT): mantén pulsado el botón para hablar y suéltalo para dejar de transmitir.
📑 Tabla de contenidos
📂 Estructura del proyecto
🚀 Subir el proyecto a GitHub
☁️ Configurar Cloudflare
📱 Compilar la aplicación Android
🔐 Canales y claves
⚙️ Cómo funciona
✨ Novedades de Wavko 2.3
🔒 Novedades de Wavko 2.2
🆕 Novedades de Wavko 2.1
🚀 Novedades de Wavko 2.0
🛠️ Próximas mejoras
📂 Estructura del proyecto
wavko/
├── cloudflare/                  # Servidor Cloudflare Workers + Durable Objects
├── android/                     # Aplicación Android (Kotlin)
└── .github/
    └── workflows/
        ├── build-apk.yml        # Compilación automática del APK
        └── deploy-cloudflare.yml # Despliegue del servidor
🚀 Subir el proyecto a GitHub
Crea un repositorio nuevo en GitHub.
Sube el archivo .zip del proyecto directamente mediante Add file → Upload files.
Crea los siguientes archivos mediante Add file → Create new file, pegando el contenido de los workflows correspondientes:
.github/workflows/build-apk.yml
.github/workflows/deploy-cloudflare.yml
Los workflows se encargarán de descomprimir el ZIP automáticamente.
💡 También puedes subir directamente las carpetas del proyecto ya descomprimidas.
☁️ Configurar Cloudflare
La configuración inicial se realiza una sola vez desde tu cuenta de Cloudflare.
1. Crear y configurar el servidor
Accede a Workers & Pages desde el dashboard de Cloudflare.
Si es necesario, configura tu subdominio workers.dev.
Crea un API Token:
My Profile → API Tokens → Create Token
Selecciona la plantilla Edit Cloudflare Workers.
Copia tu Account ID, disponible en la página principal de Workers & Pages, en la barra lateral derecha.
2. Configurar los secretos de GitHub
En tu repositorio, dirígete a:
Settings → Secrets and variables → Actions → New repository secret
Añade los siguientes secretos:
Nombre
Descripción
CLOUDFLARE_API_TOKEN
Token generado en Cloudflare.
CLOUDFLARE_ACCOUNT_ID
Identificador de tu cuenta Cloudflare.
ACCESS_KEY
Clave de acceso opcional y recomendada.
3. Desplegar el servidor
Una vez configurados los secretos:
Accede a la pestaña Actions.
Selecciona Deploy Cloudflare Worker.
Pulsa Run workflow.
El despliegue también se ejecutará automáticamente con cada actualización enviada a la rama main.
El servidor quedará disponible en:
wss://voxgo.<tu-subdominio>.workers.dev
Esta será la dirección que utilizará la aplicación Android en el campo Servidor.
⚡ Arquitectura del servidor
Wavko utiliza un Durable Object por canal, implementado mediante la API de hibernación de Cloudflare.
Cuando un canal permanece en silencio, no consume cómputo de forma continua.
Las conexiones WebSocket permanecen abiertas.
Los mensajes entrantes por WebSocket se contabilizan en una proporción de 20:1 como requests.
Consulta los límites vigentes del plan gratuito en la documentación oficial de Cloudflare.
🧪 Ejecutar el servidor localmente
cd cloudflare
npm install
npx wrangler dev
En la aplicación Android, configura:
ws://IP-DE-TU-PC:8787
📱 Compilar la aplicación Android
GitHub Actions permite generar el APK automáticamente sin necesidad de compilar manualmente desde Android Studio.
Generación del APK
Realiza un commit y envíalo a la rama main.
Accede a Actions → Build APK.
Busca la sección Artifacts.
Descarga el archivo wavko-apk.
Dentro del ZIP encontrarás wavko.apk.
También puedes iniciar la compilación manualmente mediante Run workflow.
📦 Publicar una versión en Releases
Para publicar una versión etiquetada:
git tag v1.0
git push --tags
El APK aparecerá en la sección Releases de GitHub, permitiendo descargarlo directamente desde el teléfono.
El APK generado es una versión debug firmada automáticamente, suficiente para uso propio.
Configuración predeterminada
La aplicación ya incluye el siguiente servidor:
wss://voxgo.pedroxhile.workers.dev
Puedes modificarlo desde el campo Servidor de la aplicación.
Solo necesitas seleccionar un canal (por ejemplo, general) e introducir tu nombre.
Variables opcionales
Puedes personalizar la compilación desde:
Settings → Secrets and variables → Actions → Variables
Variable
Función
WAVKO_SERVER
Sustituye la dirección del servidor predeterminado.
ACCESS_KEY
Precarga la clave de acceso en la aplicación.
⚠️ Una clave precargada queda incluida dentro del APK. No compartas ese APK con personas que no deban tener acceso a ella.
🔐 Canales y claves
Wavko permite crear canales de comunicación y protegerlos mediante una contraseña.
Funcionalidades
Crear canal: eliges un nombre libre y, opcionalmente, una clave. Te conviertes en el primer participante.
Unirme a canal: introduces el nombre del canal y la clave correspondiente.
Invitaciones: puedes compartir el nombre y la clave mediante WhatsApp u otro medio.
Eliminación automática: un canal existe mientras haya participantes conectados. Cuando sale la última persona, desaparece junto con su clave.
Nombres duplicados: si dos personas utilizan el mismo nombre, el servidor añade automáticamente (2), (3) y así sucesivamente.
Desconectar: permite salir del canal.
Pantalla de bienvenida: al abrir la aplicación aparece una pantalla con el botón Seguir. Se omite si ya estás dentro de un canal.
🔄 Actualización de funciones
Las funcionalidades de los canales se gestionan desde el Worker de Cloudflare.
Después de actualizarlo, ejecuta:
Deploy Cloudflare Worker
Build APK
⚙️ Cómo funciona
🎧 Sistema de audio
Audio PCM de 16 kHz en mono.
Compresión G.711 µ-law.
Bitrate aproximado de 128 kbps.
Paquetes de audio de 40 ms.
🎙️ Push-to-Talk
Un solo participante puede hablar por canal a la vez.
Cuando alguien está hablando, el botón de los demás indica que está ocupado.
Tiempo máximo de transmisión: 60 segundos.
Si el hablante queda conectado sin transmitir audio durante 3 segundos, otro participante puede tomar el turno.
📲 Funcionamiento en segundo plano
Servicio en primer plano.
Conexión mantenida con la pantalla apagada.
Notificación permanente mientras permanece conectado.
Reconexión automática cuando se pierde la conexión de red.
Tecla Volumen − como botón PTT opcional, únicamente mientras la aplicación está abierta.
✨ Novedades de Wavko 2.3
💬 Mensajes de voz, notificaciones y chat
Esta versión incorpora nuevas herramientas para mejorar la comunicación entre amigos y participantes de los canales.
🎤 Mensajes de voz
En la sección Amigos, mantén pulsado el micrófono de un amigo para grabar.
Duración máxima: 20 segundos.
Al soltar el botón, el mensaje se envía automáticamente.
Los mensajes llegan a la bandeja Mensajes de voz, incluso si el destinatario está desconectado.
Se conservan durante 7 días, con un máximo de 30 mensajes.
Funcionan exclusivamente entre amigos y respetan los bloqueos.
No es posible grabar mientras estás dentro de un canal.
💬 Chat de texto
Chat integrado dentro de cada canal.
Acceso mediante el botón Chat, ubicado en la parte superior derecha.
En canales protegidos con contraseña, los mensajes se cifran igual que el audio.
Los mensajes no se almacenan permanentemente: se eliminan al salir del canal.
🔔 Notificaciones push
Notificaciones opcionales mediante Firebase para recibir avisos cuando:
Alguien te envía una invitación.
Recibes un mensaje de voz.
Con Firebase puedes recibir notificaciones incluso cuando la aplicación está cerrada.
Sin Firebase, el resto de las funciones continúa funcionando. Únicamente se notificará a los amigos que estén conectados.
Configurar notificaciones push
La configuración se realiza una sola vez.
Accede a Firebase Console.
Crea un proyecto gratuito.
Añade una aplicación Android con el nombre de paquete:
app.wavko
Descarga el archivo google-services.json.
Copia su contenido completo en un secreto de GitHub llamado:
GOOGLE_SERVICES_JSON
En Firebase, accede a:
Configuración del proyecto → Cuentas de servicio → Generar nueva clave privada
Copia el JSON completo en otro secreto de GitHub:
FCM_SERVICE_ACCOUNT
Ejecuta los siguientes workflows:
Deploy Cloudflare Worker
Build APK
🔄 Orden de actualización
Primero despliega el Worker y después compila el APK.
⚠️ Todos los usuarios deben actualizar la aplicación. El cifrado introducido en la versión 2.2 no es compatible con versiones anteriores.
🔒 Novedades de Wavko 2.2
Seguridad, cuentas y privacidad
Esta versión incorpora un sistema de cuentas, solicitudes de amistad, bloqueo de usuarios y cifrado de extremo a extremo en canales protegidos.
👤 Cuentas de usuario
Al abrir la aplicación por primera vez, se crea una cuenta en el servidor.
Se conserva el código de amigo existente.
La aplicación genera una clave de recuperación de 20 caracteres.
Es importante guardar esta clave en un lugar seguro.
Si cambias de teléfono o reinstalas la aplicación, puedes recuperar tu cuenta mediante tu código y clave de recuperación.
La recuperación cierra las sesiones de los demás teléfonos.
🤝 Solicitudes de amistad
Añadir un código de amigo envía una solicitud.
El destinatario puede aceptar, rechazar o bloquear.
Solo los amigos aceptados pueden ver tu estado.
Los estados disponibles son En línea y En un canal.
Los amigos aceptados pueden enviarte invitaciones.
Si dos personas se envían solicitudes mutuamente, se convierten en amigos automáticamente.
Los amigos anteriores se convierten automáticamente en solicitudes.
🚫 Bloqueo de usuarios
Puedes bloquear personas desde:
La sección Amigos.
Una solicitud de amistad.
El perfil de un participante dentro de un canal.
Los usuarios bloqueados:
No pueden enviarte solicitudes.
No pueden enviarte invitaciones.
No puedes escuchar su voz en los canales.
La lista de bloqueados y el botón Desbloquear están disponibles en Ajustes.
👑 Administración de canales
La persona que crea el canal se convierte en su propietario.
El propietario puede seleccionar a un participante y utilizar Expulsar del canal.
El usuario expulsado queda vetado hasta que el canal se vacíe.
El bloqueo se realiza mediante la cuenta o, si no tiene cuenta, mediante la dirección IP.
Si el propietario abandona el canal, el participante más antiguo hereda automáticamente el cargo.
🛡️ Protección contra intentos de contraseña
Se permiten hasta 5 intentos incorrectos consecutivos.
Al superar ese límite, la IP o cuenta queda bloqueada durante 5 minutos para ese canal.
🔐 Cifrado de extremo a extremo
Los canales protegidos mediante
