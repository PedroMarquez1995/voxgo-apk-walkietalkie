Wavko
Comunicación por voz en movimiento.
App tipo Zello (push-to-talk): mantén pulsado para hablar, los demás en el canal te escuchan en vivo.
wavko/
├── cloudflare/                  Servidor en Cloudflare Workers + Durable Objects (recomendado)
├── android/                     App Android (Kotlin)
└── .github/workflows/
    ├── build-apk.yml            Compila el APK
    └── deploy-cloudflare.yml    Despliega el servidor en Cloudflare
1. Subir el proyecto a GitHub (sin descomprimir)
Crea un repositorio y sube el .zip tal cual (Add file → Upload files).
Crea dos archivos con Add file → Create new file, pegando el contenido de los .yml que vienen aparte:
.github/workflows/build-apk.yml
.github/workflows/deploy-cloudflare.yml
Los workflows descomprimen el zip solos. Si prefieres, también puedes subir las carpetas ya descomprimidas.
2. Servidor en Cloudflare
Una sola vez, en tu cuenta de Cloudflare:
Entra a Workers & Pages en el dashboard y, si te lo pide, elige tu subdominio workers.dev.
Crea un API Token: My Profile → API Tokens → Create Token → plantilla "Edit Cloudflare Workers".
Copia tu Account ID (en la página principal de Workers & Pages, barra lateral derecha).
En tu repo de GitHub, Settings → Secrets and variables → Actions → New repository secret:
Secret
Valor
CLOUDFLARE_API_TOKEN
el token del paso 2
CLOUDFLARE_ACCOUNT_ID
el Account ID del paso 3
ACCESS_KEY (opcional, recomendado)
una clave; la app deberá enviarla
Luego ejecuta Actions → Deploy Cloudflare Worker → Run workflow (también corre en cada push a main). Tu servidor quedará en wss://voxgo.<tu-subdominio>.workers.dev; ese es el valor del campo "Servidor" de la app.
Cómo funciona: un Durable Object por canal, con la API de hibernación. Con el canal en silencio no gasta cómputo y las conexiones siguen abiertas. Los mensajes WebSocket entrantes se cuentan 20:1 como requests; consulta los límites vigentes del plan gratis en la documentación de Cloudflare.
Probarlo en tu PC: cd cloudflare && npm install && npx wrangler dev y en la app ws://IP-DE-TU-PC:8787.
3. Compilar el APK
Al hacer commit en main, entra en Actions → Build APK → abajo, en Artifacts, descarga wavko-apk (zip con wavko.apk).
También puedes lanzarlo a mano con Run workflow.
Si subes un tag (git tag v1.0 && git push --tags), el APK queda en Releases, útil para bajarlo directo desde el celular.
Instálalo en el teléfono (permite "instalar apps desconocidas"). Es un APK debug, firmado automáticamente, suficiente para uso propio.
La app ya viene con el servidor wss://voxgo.pedroxhile.workers.dev por defecto (se puede cambiar en el campo "Servidor"). Solo falta elegir un canal (ej. general) y tu nombre.
Opcional, al compilar: si en GitHub creas la variable WAVKO_SERVER (Settings → Secrets and variables → Actions → Variables) el APK usa esa dirección en vez de la de arriba; y si existe el secret ACCESS_KEY, la clave también queda precargada. Ojo: una clave precargada va dentro del APK, así que no compartas ese APK con quien no deba tenerla.
Canales y claves
Crear canal: eliges un nombre libre y, si quieres, una clave. Quedas dentro como primera persona.
Unirme a canal: escribes el nombre del canal y la clave que te dieron. Si el canal no existe o la clave es incorrecta, la app te lo avisa.
Para invitar a alguien, pásale por otro medio (WhatsApp, por ejemplo) el nombre del canal y la clave.
Un canal existe mientras haya alguien dentro. Cuando sale la última persona, el canal y su clave desaparecen; para volver a usarlo hay que crearlo de nuevo.
Si dos personas entran con el mismo nombre, el servidor añade (2), (3)… para distinguirlas.
Desconectar te saca del canal.
Al abrir la app aparece una pantalla de bienvenida con el botón Seguir (se salta si ya estás dentro de un canal).
Estas funciones viven en el Worker de Cloudflare: después de actualizarlo hay que ejecutar Deploy Cloudflare Worker y luego Build APK.
Cómo funciona
Audio: PCM 16 kHz mono comprimido con G.711 µ-law (≈128 kbps), en paquetes de 40 ms.
Un solo hablante por canal a la vez: si alguien habla, tu botón suena "ocupado". Turno máximo de 60 s; si el hablante queda colgado (sin audio 3 s) otro puede tomar el turno.
Servicio en primer plano: sigue conectado con la pantalla apagada (se ve una notificación fija).
Reconexión automática si se cae la red.
Tecla Volumen − como PTT: opcional, funciona solo con la app abierta.
Siguientes mejoras posibles
Códec Opus (menos datos móviles).
Cuentas y canales privados.
PTT con la pantalla apagada vía auricular Bluetooth / servicio de accesibilidad.
Mensajes de voz grabados e historial.
Novedades de Wavko 2.3 (mensajes de voz, push y chat)
Mensajes de voz: en Amigos, mantén pulsado el micrófono de un amigo (máx. 20 s) y suelta para enviar. Le llega a su bandeja "Mensajes de voz" (se guardan 7 días, máx. 30) aunque esté sin conexión. Solo entre amigos y respetando los bloqueos. No se puede grabar mientras estás dentro de un canal.
Chat de texto dentro del canal (botón Chat arriba a la derecha). En canales con clave el texto va cifrado igual que el audio. No se guarda: se borra al salir.
Notificaciones push (opcionales, con Firebase): avisan de una invitación o de un mensaje de voz con la app cerrada. Sin Firebase todo lo demás funciona; solo se avisa a los amigos que están en línea.
Activar las notificaciones push (opcional, una sola vez)
En https://console.firebase.google.com crea un proyecto (gratis) y añade una app Android con el nombre de paquete app.wavko.
Descarga google-services.json y pega todo su contenido en un secret de GitHub llamado GOOGLE_SERVICES_JSON.
En Firebase: Configuración del proyecto → Cuentas de servicio → Generar nueva clave privada. Pega el JSON completo en un secret de GitHub llamado FCM_SERVICE_ACCOUNT.
Ejecuta Deploy Cloudflare Worker y después Build APK.
Orden para actualizar
Primero Deploy Cloudflare Worker y luego Build APK. Todos deben actualizar la app (el cifrado de 2.2 no es compatible con versiones anteriores).
Novedades de Wavko 2.2 (seguridad y cuentas)
Cuenta real: al abrir la app por primera vez se crea una cuenta en tu servidor (conserva el código de amigo que ya tenías). Te muestra una clave de recuperación de 20 caracteres: guárdala. Si cambias de teléfono o reinstalas, en la pantalla de bienvenida o en Perfil → Recuperar otra cuenta entras con tu código + esa clave. Recuperar la cuenta cierra la sesión de los demás teléfonos.
Solicitudes de amistad: añadir un código envía una solicitud; la otra persona la acepta, la rechaza o la bloquea. Solo los amigos aceptados ven tu estado ("en línea" / "en un canal") y pueden invitarte. Si ambos se envían solicitud, quedan como amigos al instante. Tus amigos anteriores se envían como solicitudes automáticamente.
Bloquear: desde Amigos, desde una solicitud o tocando a alguien en un canal. Quien bloqueas no puede enviarte solicitudes ni invitaciones y no oyes su voz en los canales. Lista de bloqueados y botón Desbloquear en Ajustes.
Expulsar: el que crea el canal es el dueño; toca a un participante → Expulsar del canal. Queda vetado (por cuenta, o por IP si no tenía cuenta) hasta que el canal se vacíe. Si el dueño se va, el participante más antiguo hereda el cargo.
Límite de intentos de clave: 5 claves de canal incorrectas seguidas bloquean 5 minutos a esa IP/cuenta en ese canal.
Cifrado de extremo a extremo en canales con clave: del texto de la clave se derivan dos valores distintos (PBKDF2): una prueba que se manda al servidor y una clave AES-256-GCM con la que se cifra el audio, que nunca sale del teléfono. El servidor puede comprobar la clave pero no puede escuchar el canal. Un canal sin clave no se cifra. Límite: si invitas a un amigo desde la app, la clave pasa por tu servidor dentro de la invitación; si necesitas privacidad estricta, compártela por otro medio.
Orden para actualizar
Actions → Deploy Cloudflare Worker (crea las cuentas: migración v3) y espera a que termine.
Actions → Build APK. Todos los del canal deben tener esta versión (los cambios del cifrado no son compatibles con la anterior).
Novedades de Wavko 2.1
Invitar amigos a tu canal: en Amigos, el botón Invitar aparece junto a cada amigo en línea cuando estás en un canal. Le llega un aviso (en pantalla, o como notificación si estás en un canal con la app en segundo plano). Solo se entrega si el amigo también te tiene en su lista. La invitación lleva la clave del canal.
Segundo plano: aviso (una sola vez) y sección en Ajustes para quitar la optimización de batería, con enlace a la guía de tu marca (dontkillmyapp.com).
Audio HD (Opus, experimental): Ajustes → Audio HD. Usa el códec Opus del sistema (Android 10+). Desactivado por defecto; si el teléfono no lo soporta se usa el audio normal. Todos los del canal deben tener esta versión: una versión antigua oiría ruido.
Tema claro / oscuro / del sistema, 16 skins, animación al cambiar de pestaña.
Canales recientes y favoritos en la pestaña Canales (mantén pulsado para marcar ★).
Español e inglés (según el idioma del teléfono).
Se eliminó el servidor Node.js alternativo: solo se mantiene el de Cloudflare.
Novedades de Wavko 2.0
Pestañas: Canales, Amigos, Perfil y Ajustes (menú inferior).
Amigos: cada instalación tiene un código de 6 caracteres; al añadirse ven si el otro está en línea o en un canal. Quien tenga tu código puede ver tu estado.
Perfil: nombre y avatar de walkie-talkie con skins; se ven en las listas de participantes y de amigos.
Ajustes: servidor, clave del servidor y "Volumen − como PTT (app abierta)".
Widget "Toca para hablar": un toque empieza a transmitir y otro termina (Android no permite "mantener pulsado" en widgets). En la pantalla de bloqueo depende del teléfono; la notificación trae el botón Hablar / Parar.
Orden para actualizar
Actions → Deploy Cloudflare Worker (añade las invitaciones).
Actions → Build APK e instala wavko.apk (misma app app.wavko: se actualiza encima).
Nota: el Worker sigue llamándose voxgo para no cambiar la dirección del servidor.
