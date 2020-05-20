# [Auto-postal](https://hub.docker.com/r/dued/auto-postal)

[![Docker Automated build](https://img.shields.io/docker/automated/dued/auto-postal.svg)](https://hub.docker.com/r/dued/auto-postal/)
[![](https://images.microbadger.com/badges/image/dued/auto-postal.svg)](https://microbadger.com/images/dued/auto-postal "Get your own image badge on microbadger.com")
[![](https://images.microbadger.com/badges/version/dued/auto-postal.svg)](https://microbadger.com/images/dued/auto-postal "Get your own version badge on microbadger.com")

## ¿Que es Auto-postal?

Es un retransmisor SMTP con cola local basado en postfix.

## ¿Por qué?

El envío de correos electrónicos a través de una red controlada (puede ser localhost o LAN) es más rápido y más controlado que enviarlos directamente a través de Internet.

Este contenedor retiene la cola de correo electrónico en un volumen inferior `/var/spool/postfix`, por lo que en caso de que su red falle o su servidor SMTP real esté inactivo por mantenimiento o lo que sea, la cola se enviará cuando se restablezca la conexión de red.

De esta forma, su aplicación puede enviar correos electrónicos más rápido, olvidarse de posibles fallas temporales de la red y concentrarse en su negocio.

## ¿Cómo?

Configure a través de estas variables de entorno:

- `MAILNAME`:  El host por defecto para los mail de trabajo cron.
- `MAIL_RELAY_HOST`: El servidor real de SMTP (p.ej. `smtp.email.pe`).
- `MAIL_RELAY_PORT`: El puerto en `MAIL_RELAY_HOST`. Dependiendo del puerto, se utilizará una configuración de seguridad específica.
- `MAIL_RELAY_USER`: El usuario para autenticarse en `MAIL_RELAY_HOST`.
- `MAIL_RELAY_PASS`: El password para autenticarse en `MAIL_RELAY_HOST`.
- `MAIL_CANONICAL_DOMAINS`: Una lista de dominios separados por espacios que se consideran [canónicos][].
- `MAIL_NON_CANONICAL_DEFAULT`: Un dominio que debe encontrarse en la lista de
  `MAIL_ANONICAL_DOMAINS`, que se utilizará como dominio de reemplazo cuando ingrese un mensaje non-[canonical][canónicos] Déjelo vacío para omitir ese sistema de reemplazo.
- `MAIL_CANONICAL_PREFIX`: El valor predeterminado es `noreply+`, y es lo que será prefijado a las direcciones de remitente non-[canonical][canónicos] reemplazadas.
- `MESSAGE_SIZE_LIMIT` en bytes, el valor predeterminado es 50MiB.La mayoría de los servidores generosos ofrecen un límite de 25iMB (Gmail, Mailgun...), por lo que, por defecto, a 50MiB, básicamente estamos obligando al servidor remoto a fallar en caso de un gran correo electrónico, en lugar de hacer que falle el relé local. Cambie a voluntad si prefiere un comportamiento diferente..
- `ROUTE_CUSTOM` lista de subredes separadas por espacios en la notación estándar CIDR 
  (p.ej. 192.168.0.0/16).

### Ejemplos

#### SMTP relay via Gmail

```
docker container run \
    -e MAIL_RELAY_HOST='smtp.gmail.com' \
    -e MAIL_RELAY_PORT='587' \
    -e MAIL_RELAY_USER='tu_direccion_gmail@gmail.com' \
    -e MAIL_RELAY_PASS='tu_password_gmail' \
    dued/auto-postal
```

## FAQ

### ¿Qué es un dominio canónico?

Significa "dominios que pueden enviar correo desde aquí".

Suponga que su aplicación permite a los usuarios definir sus propios correos electrónicos, y ese se usa para enviar correos electrónicos a otros usuarios desde el sistema.

Si solo posee los dominios ejemplo.com y ejemplo.net, pero alguien configura su correo electrónico como peruano@ejemplo.org. Si envía este correo electrónico tal como vino, los filtros de SPAM lo bloquearán.

Al definir `MAIL_CANONICAL_DOMAINS=ejemplo.com ejemplo.net` y `MAIL_NON_CANONICAL_DEFAULT=ejemplo.com`, el correo se modificaría como si viniera `noreply+peruano-ejemplo.org@ejemplo.com`, y los filtros de SPAM estarán contentos con eso.

[canónicos]: #¿Qué-es-un-dominio-canónico?
