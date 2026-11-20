🎙️ Wavko

Comunicación por voz en movimiento.

Wavko es una aplicación Android de comunicación por voz en tiempo real, basada en el concepto Push-to-Talk (PTT). Permite crear canales, conectar con amigos y hablar instantáneamente manteniendo pulsado un botón, como un walkie-talkie.

Diseñada para ofrecer comunicación rápida, sencilla y segura, utilizando Android, Cloudflare Workers y Durable Objects.

<p align="center">"Android" (https://img.shields.io/badge/Android-App-3DDC84?logo=android&logoColor=white)
"Kotlin" (https://img.shields.io/badge/Kotlin-Android-7F52FF?logo=kotlin&logoColor=white)
"Cloudflare" (https://img.shields.io/badge/Cloudflare-Workers-F38020?logo=cloudflare&logoColor=white)
"WebSocket" (https://img.shields.io/badge/Communication-WebSocket-blue)
"Version" (https://img.shields.io/badge/Version-2.3-brightgreen)
"Platform" (https://img.shields.io/badge/Platform-Android-black?logo=android)

</p>---

📑 Tabla de contenidos

- "✨ Características principales" (#-características-principales)
- "📱 Funcionalidades" (#-funcionalidades)
- "🔐 Seguridad y privacidad" (#-seguridad-y-privacidad)
- "🏗️ Arquitectura del proyecto" (#️-arquitectura-del-proyecto)
- "🚀 Instalación y configuración" (#-instalación-y-configuración)
- "☁️ Configurar Cloudflare" (#️-configurar-cloudflare)
- "📦 Compilar APK" (#-compilar-apk)
- "🔔 Notificaciones push" (#-notificaciones-push)
- "⚙️ Funcionamiento técnico" (#️-funcionamiento-técnico)
- "🔄 Actualizaciones" (#-actualizaciones)
- "📋 Historial de versiones" (#-historial-de-versiones)

---

✨ Características principales

Funcionalidad| Descripción
🎙️ Push-to-Talk| Mantén pulsado para hablar en tiempo real.
📡 Canales| Crea canales públicos o protegidos con clave.
👥 Amigos| Añade contactos mediante códigos únicos.
💬 Chat| Envía mensajes de texto dentro de los canales.
🎧 Mensajes de voz| Envía grabaciones privadas a tus amigos.
🔐 Cifrado| AES-256-GCM en canales protegidos con clave.
🔔 Notificaciones| Recibe invitaciones y mensajes con la app cerrada.
📱 Segundo plano| Mantiene la conexión con la pantalla apagada.
🎨 Personalización| Temas, skins y avatares.
🔄 Reconexión| Recuperación automática ante cortes de conexión.

📱 Funcionalidades

🎙️ Comunicación Push-to-Talk

- Mantén pulsado el botón para transmitir tu voz.
- Los participantes escuchan el audio en directo.
- Un solo usuario transmite a la vez por canal.
- Indicador de canal ocupado.
- Turnos de transmisión de hasta 60 segundos.
- Liberación automática del turno si el hablante deja de transmitir durante 3 segundos.
- Compatibilidad experimental con Audio HD mediante Opus.

👥 Amigos y contactos

- Código de amigo único de 6 caracteres.
- Solicitudes de amistad.
- Aceptar, rechazar o bloquear contactos.
- Estado de conexión: en línea o en un canal.
- Invitaciones directas a canales.
- Lista de amigos y usuarios bloqueados.

💬 Chat y mensajes de voz

- Chat de texto integrado en los canales.
- Mensajes de texto cifrados en canales protegidos.
- Los mensajes del chat desaparecen al abandonar el canal.
- Mensajes de voz privados de hasta 20 segundos.
- Bandeja de mensajes de voz.
- Conservación de mensajes durante 7 días, con un máximo de 30.
- Funcionamiento entre amigos aceptados y respeto de los bloqueos.

🔐 Cuentas y recuperación

- Creación automática de una cuenta al iniciar la aplicación.
- Conservación del código de amigo.
- Clave de recuperación de 20 caracteres.
- Recuperación de cuentas al cambiar de teléfono o reinstalar.
- Cierre de las demás sesiones al recuperar una cuenta.

🎨 Personalización

- Tema claro, oscuro o automático.
- 16 skins disponibles.
- Avatar personalizado de walkie-talkie.
- Canales recientes y favoritos.
- Interfaz en español e inglés según el idioma del dispositivo.
- Animaciones al cambiar de pestaña.

---

🔐 Seguridad y privacidad

Wavko incorpora mecanismos de protección para las cuentas, los canales y las comunicaciones.

Cifrado de extremo a extremo

En canales protegidos con contraseña se utiliza:

- PBKDF2 para derivación de claves.
- AES-256-GCM para cifrar el audio.
- Separación entre la prueba de contraseña y la clave de cifrado.
- La clave de cifrado permanece en el dispositivo.

El servidor verifica la contraseña, pero no puede escuchar el audio cifrado.

Otras medidas de seguridad:

- Bloqueo temporal después de 5 intentos incorrectos de contraseña.
- Bloqueo durante 5 minutos por IP o cuenta en ese canal.
- Bloqueo de solicitudes e invitaciones de usuarios bloqueados.
- Expulsión de participantes por parte del propietario del canal.
- Restricción de acceso para usuarios expulsados hasta que el canal quede vacío.

«Importante: Los canales sin contraseña no utilizan este cifrado. Además, las invitaciones enviadas desde la aplicación pueden transportar la clave a través del servidor. Para una privacidad estricta, comparte la contraseña por otro medio.»

---

🏗️ Arquitectura del proyecto

El proyecto está organizado en dos componentes principales: aplicación Android y servidor en Cloudflare.

wavko/
│
├── cloudflare/
│   └── Servidor Cloudflare Workers
│       └── Durable Objects
│
├── android/
│   └── Aplicación Android
│       └── Kotlin
│
└── .github/
    └── workflows/
        ├── build-apk.yml
        └── deploy-cloudflare.yml

Tecnologías utilizadas

Tecnología| Función
Kotlin| Aplicación Android
Cloudflare Workers| Backend y comunicación
Durable Objects| Gestión de canales y conexiones
WebSocket| Comunicación en tiempo real
GitHub Actions| Automatización de compilaciones
Firebase Cloud Messaging| Notificaciones push opcionales
AES-256-GCM| Cifrado de comunicaciones protegidas
PBKDF2| Derivación de claves

---

🚀 Instalación y configuración

1. Subir el proyecto a GitHub

Puedes subir el proyecto comprimido sin necesidad de descomprimirlo previamente.

1. Crea un repositorio en GitHub.
2. Sube el archivo ".zip" mediante Add file → Upload files.
3. Crea los siguientes archivos mediante Add file → Create new file:
   - ".github/workflows/build-apk.yml"
   - ".github/workflows/deploy-cloudflare.yml"
4. Pega el contenido correspondiente de cada workflow.

Los workflows se encargan de descomprimir y preparar el proyecto automáticamente.

También puedes subir las carpetas directamente si prefieres trabajar con el proyecto descomprimido.

2. Configurar secretos de GitHub

Entra en:

Settings → Secrets and variables → Actions → New repository secret

Nombre| Descripción
"CLOUDFLARE_API_TOKEN"| Token de acceso a Cloudflare.
"CLOUDFLARE_ACCOUNT_ID"| Identificador de tu cuenta Cloudflare.
"ACCESS_KEY"| Clave de acceso opcional para el servidor.

---

☁️ Configurar Cloudflare

La aplicación utiliza Cloudflare Workers y Durable Objects para gestionar la comunicación.

Configuración inicial:

1. Accede al dashboard de Cloudflare.
2. Entra en Workers & Pages.
3. Configura tu subdominio "workers.dev" si todavía no lo tienes.
4. Ve a My Profile → API Tokens.
5. Selecciona Create Token.
6. Utiliza la plantilla Edit Cloudflare Workers.
7. Copia el token generado.
8. Obtén tu Account ID desde el panel principal de Workers & Pages.
9. Guarda ambos valores en los secretos de GitHub.

Desplegar el servidor

En GitHub:

1. Entra en la pestaña Actions.
2. Selecciona Deploy Cloudflare Worker.
3. Pulsa Run workflow.

También se ejecuta automáticamente cuando se realiza un push a "main".

El servidor utiliza una dirección con este formato:

"wss://voxgo.<tu-subdominio>.workers.dev"

Esta dirección debe coincidir con el campo Servidor de la aplicación.

Probar localmente

cd cloudflare
npm install
npx wrangler dev

En la aplicación Android puedes utilizar:

"ws://IP-DE-TU-PC:8787"

---

📦 Compilar APK

La compilación se realiza automáticamente mediante GitHub Actions.

Pasos:

1. Realiza un commit o push a la rama "main".
2. Accede a Actions.
3. Selecciona Build APK.
4. Espera a que finalice la compilación.
5. Busca la sección Artifacts.
6. Descarga "wavko-apk".
7. Descomprime el archivo y encontrarás "wavko.apk".

También puedes iniciar la compilación manualmente mediante Run workflow.

📲 Instalar en Android

1. Transfiere el archivo APK al teléfono.
2. Ábrelo.
3. Autoriza la instalación de aplicaciones desconocidas cuando Android lo solicite.
4. Instala Wavko.
5. Abre la aplicación y configura tu perfil.

El APK generado es una versión debug firmada automáticamente, adecuada para pruebas y uso personal.

🚀 Publicar mediante Releases

También puedes generar una versión identificada mediante etiquetas de Git:

git tag v1.0
git push --tags

El APK se publicará en GitHub Releases, facilitando su descarga desde cualquier dispositivo.

---

🔔 Notificaciones push

Las notificaciones push son opcionales y utilizan Firebase Cloud Messaging (FCM).

Permiten recibir avisos de:

- Invitaciones a canales.
- Mensajes de voz de amigos.
- Actividad relevante cuando la aplicación está cerrada.

Configuración de Firebase

1. Entra en https://console.firebase.google.com

2. Crea un proyecto.

3. Añade una aplicación Android con el paquete:
   
   "app.wavko"

4. Descarga "google-services.json".

5. Copia su contenido en un secreto de GitHub llamado:
   
   "GOOGLE_SERVICES_JSON"

6. En Firebase, entra en Configuración del proyecto → Cuentas de servicio.

7. Genera una nueva clave privada.

8. Guarda el JSON completo en un secreto llamado:
   
   "FCM_SERVICE_ACCOUNT"

9. Ejecuta el despliegue de Cloudflare.

10. Ejecuta la compilación del APK.

«Sin Firebase, las funciones principales de Wavko siguen funcionando. Las notificaciones push opcionales no estarán disponibles; los avisos a amigos conectados pueden seguir funcionando dentro de la aplicación.»

---

⚙️ Funcionamiento técnico

Audio y transmisión

- Audio PCM de 16 kHz mono.
- Compresión G.711 µ-law.
- Tasa aproximada de 128 kbps.
- Paquetes de audio de 40 ms.
- Un hablante simultáneo por canal.
- Turno máximo de transmisión de 60 segundos.

Conexión

- WebSocket para comunicación en tiempo real.
- Durable Objects para administrar los canales.
- Reconexión automática ante pérdidas de conexión.
- Servicio en primer plano para mantener la conexión con la pantalla apagada.
- Notificación persistente de Android mientras el servicio está activo.

Consumo de Cloudflare

La arquitectura utiliza Durable Objects con API de hibernación.

Cuando un canal permanece en silencio, las conexiones pueden mantenerse abiertas sin consumir cómputo continuamente.

Los mensajes WebSocket entrantes se contabilizan con una relación de 20:1 respecto a las solicitudes. Consulta los límites y condiciones vigentes del plan de Cloudflare antes de desplegar a gran escala.

---

🔄 Actualizaciones

Para actualizar Wavko es importante respetar el orden de despliegue.

Orden recomendado:

1. Actualizar el código del servidor.
2. Ejecutar Actions → Deploy Cloudflare Worker.
3. Esperar a que finalice correctamente.
4. Ejecutar Actions → Build APK.
5. Instalar la nueva versión en los dispositivos.

«Importante: Las versiones que incorporan cambios incompatibles en el cifrado o las cuentas deben actualizarse en todos los dispositivos que participan en los canales.»

Para las migraciones de cuentas de la versión 2.2, el despliegue del Worker debe ejecutarse antes de compilar e instalar el APK.

---

📋 Historial de versiones

🟢 Wavko 2.3 — Mensajes de voz, chat y notificaciones

- Mensajes de voz privados entre amigos.
- Grabaciones de hasta 20 segundos.
- Bandeja de mensajes de voz.
- Retención de mensajes durante 7 días.
- Chat de texto integrado en canales.
- Cifrado del chat en canales protegidos.
- Notificaciones push opcionales mediante Firebase.

🔵 Wavko 2.2 — Seguridad y cuentas

- Creación de cuentas reales.
- Claves de recuperación de 20 caracteres.
- Recuperación de cuentas.
- Solicitudes de amistad.
- Sistema de bloqueo y desbloqueo.
- Expulsión de participantes.
- Límite de intentos de contraseñas.
- Cifrado AES-256-GCM en canales protegidos.
- Derivación de claves mediante PBKDF2.

🟣 Wavko 2.1 — Invitaciones y experiencia de usuario

- Invitaciones directas a canales.
- Mejoras para funcionamiento en segundo plano.
- Audio HD experimental mediante Opus.
- Temas claro, oscuro y automático.
- 16 skins.
- Canales recientes y favoritos.
- Soporte en español e inglés.
- Eliminación del servidor Node.js alternativo.

⚫ Wavko 2.0 — Nueva interfaz y funcionalidades

- Navegación mediante pestañas.
- Sistema de amigos.
- Códigos únicos de usuario.
- Perfil personalizable.
- Avatares de walkie-talkie.
- Ajustes de servidor y conexión.
- Widget de acceso rápido Push-to-Talk.

---

🌐 Servidor predeterminado

La aplicación incluye inicialmente el siguiente servidor:

"wss://voxgo.pedroxhile.workers.dev"

Puedes modificarlo desde el campo Servidor dentro de la aplicación.

También puedes establecer una dirección personalizada mediante la variable de GitHub Actions:

"WAVKO_SERVER"

Ruta:

Settings → Secrets and variables → Actions → Variables

Si existe el secreto "ACCESS_KEY", la clave también puede precargarse durante la compilación.

«⚠️ Una clave precargada queda incorporada dentro del APK. No compartas ese archivo con personas que no deban tener acceso a dicha clave.»

---

<p align="center">🎙️ WAVKO

Comunicación instantánea. Sin complicaciones.

Hecho para conectar personas.

</p>
