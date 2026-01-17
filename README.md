# Taiga Docker

[![Managed with Taiga.io](https://img.shields.io/badge/managed%20with-TAIGA.io-709f14.svg)](https://tree.taiga.io/project/taiga/ "Managed with Taiga.io")

> **Fork UPC**: Esta versión incluye soporte para Google SSO y sistema de métricas para Learning Dashboard.

## Tabla de Contenidos

- [Características Añadidas](#características-añadidas)
- [Requisitos](#requisitos)
- [Quick Start](#quick-start)
- [Configuración Básica](#configuración-básica)
- [Variables de Entorno](#variables-de-entorno)
- [Google SSO Setup](#google-sso-setup)
- [Sistema de Métricas](#sistema-de-métricas)
- [Configuraciones Adicionales](#configuraciones-adicionales)
- [Configuración Avanzada](#configuración-avanzada)
- [Proxy y SSL](#proxy-y-ssl)
- [Producción](#producción)
- [Troubleshooting](#troubleshooting)

---

## Características Añadidas

Esta versión fork incluye:

1. **Google SSO**: Variables de entorno para configurar autenticación con Google
2. **Métricas**: Configuración para Learning Dashboard integration
3. **CORS**: Configuración extendida para orígenes permitidos

---

## Requisitos

- Docker 19.03.0+
- Docker Compose 2.0+
- 4GB RAM mínimo (8GB recomendado)
- 20GB espacio en disco

---

## Quick Start

### 1. Clonar repositorios

```bash
# Crear directorio de trabajo
mkdir taiga && cd taiga

# Clonar los tres repositorios
git clone <url-taiga-back> taiga-back
git clone <url-taiga-front> taiga-front
git clone <url-taiga-docker> taiga-docker

cd taiga-docker
```

### 2. Configurar variables de entorno

```bash
# Editar el archivo .env
nano .env
```

**Cambios mínimos requeridos:**
- `SECRET_KEY`: Cambiar a un valor único y seguro
- `POSTGRES_PASSWORD`: Cambiar contraseña de base de datos

### 3. Iniciar Taiga

```bash
./launch-taiga.sh
```

### 4. Crear superusuario

```bash
./taiga-manage.sh createsuperuser
```

### 5. Acceder a Taiga

Abre en tu navegador: **http://localhost:9000**

---

## Configuración Básica

### Archivo `.env`

El archivo `.env` contiene todas las variables de configuración:

```bash
# ===========================================
# CONFIGURACIÓN DE TAIGA
# ===========================================

# --- URLs y Dominio ---
TAIGA_SCHEME=http                    # http o https
TAIGA_DOMAIN=localhost:9000          # Dominio de Taiga
SUBPATH=""                           # Subpath (ej: "/taiga" o "")
WEBSOCKETS_SCHEME=ws                 # ws o wss

# --- Seguridad (CAMBIAR EN PRODUCCIÓN) ---
SECRET_KEY="taiga-secret-key"        # Clave secreta única

# --- Base de Datos ---
POSTGRES_USER=taiga
POSTGRES_PASSWORD=taiga              # Cambiar en producción

# --- Email ---
EMAIL_BACKEND=console                # console o smtp
EMAIL_HOST=smtp.example.com
EMAIL_PORT=587
EMAIL_HOST_USER=user
EMAIL_HOST_PASSWORD=password
EMAIL_DEFAULT_FROM=taiga@example.com
EMAIL_USE_TLS=True
EMAIL_USE_SSL=False

# --- RabbitMQ ---
RABBITMQ_USER=taiga
RABBITMQ_PASS=taiga
RABBITMQ_VHOST=taiga
RABBITMQ_ERLANG_COOKIE=secret-erlang-cookie

# --- Otros ---
ATTACHMENTS_MAX_AGE=360
ENABLE_TELEMETRY=True

# ===========================================
# GOOGLE SSO (NUEVO)
# ===========================================
GOOGLE_AUTH_ENABLED=false
GOOGLE_AUTH_CLIENT_ID=""
GOOGLE_AUTH_ALLOWED_DOMAINS=""       # Comma-separated: "upc.edu,domain.com"
GOOGLE_AUTH_AUTO_CREATE=true

# ===========================================
# MÉTRICAS / LEARNING DASHBOARD (NUEVO)
# ===========================================
TAIGA_METRICS_PROVIDER=internal      # internal o external
TAIGA_METRICS_SNAPSHOT_TTL=60        # TTL en minutos
LD_TAIGA_BACKEND_URL=""              # URL del backend de LD
LD_TAIGA_TIMEOUT=15                  # Timeout en segundos

# ===========================================
# CORS (NUEVO)
# ===========================================
CORS_ALLOWED_ORIGINS=""              # Comma-separated URLs adicionales
```

---

## Variables de Entorno

### Variables Principales

| Variable | Descripción | Valor por Defecto | Requerido |
|----------|-------------|-------------------|-----------|
| `TAIGA_SCHEME` | Protocolo (http/https) | `http` | Sí |
| `TAIGA_DOMAIN` | Dominio completo | `localhost:9000` | Sí |
| `SUBPATH` | Subpath de la aplicación | `""` | No |
| `WEBSOCKETS_SCHEME` | Protocolo WebSocket | `ws` | Sí |
| `SECRET_KEY` | Clave secreta Django | - | **Sí** |

### Variables de Base de Datos

| Variable | Descripción | Valor por Defecto |
|----------|-------------|-------------------|
| `POSTGRES_USER` | Usuario PostgreSQL | `taiga` |
| `POSTGRES_PASSWORD` | Contraseña PostgreSQL | `taiga` |

### Variables de Email

| Variable | Descripción | Valor por Defecto |
|----------|-------------|-------------------|
| `EMAIL_BACKEND` | `console` o `smtp` | `console` |
| `EMAIL_HOST` | Servidor SMTP | `smtp.host.example.com` |
| `EMAIL_PORT` | Puerto SMTP | `587` |
| `EMAIL_HOST_USER` | Usuario SMTP | `user` |
| `EMAIL_HOST_PASSWORD` | Contraseña SMTP | `password` |
| `EMAIL_DEFAULT_FROM` | Email remitente | `changeme@example.com` |
| `EMAIL_USE_TLS` | Usar TLS | `True` |
| `EMAIL_USE_SSL` | Usar SSL | `False` |

### Variables de RabbitMQ

| Variable | Descripción | Valor por Defecto |
|----------|-------------|-------------------|
| `RABBITMQ_USER` | Usuario RabbitMQ | `taiga` |
| `RABBITMQ_PASS` | Contraseña RabbitMQ | `taiga` |
| `RABBITMQ_VHOST` | Virtual host | `taiga` |
| `RABBITMQ_ERLANG_COOKIE` | Cookie Erlang | `secret-erlang-cookie` |

### Variables de Google SSO (NUEVO)

| Variable | Descripción | Valor por Defecto |
|----------|-------------|-------------------|
| `GOOGLE_AUTH_ENABLED` | Habilitar Google SSO | `false` |
| `GOOGLE_AUTH_CLIENT_ID` | Client ID de Google OAuth | `""` |
| `GOOGLE_AUTH_ALLOWED_DOMAINS` | Dominios permitidos (comma-separated) | `""` |
| `GOOGLE_AUTH_AUTO_CREATE` | Auto-crear usuarios | `true` |

### Variables de Métricas (NUEVO)

| Variable | Descripción | Valor por Defecto |
|----------|-------------|-------------------|
| `TAIGA_METRICS_PROVIDER` | `internal` o `external` | `internal` |
| `TAIGA_METRICS_SNAPSHOT_TTL` | TTL del caché (minutos) | `60` |
| `LD_TAIGA_BACKEND_URL` | URL de Learning Dashboard | `""` |
| `LD_TAIGA_TIMEOUT` | Timeout (segundos) | `15` |

---

## Google SSO Setup

### Paso 1: Obtener Credenciales de Google

1. Ve a [Google Cloud Console](https://console.cloud.google.com/)
2. Crea un proyecto o selecciona uno existente
3. Ve a **APIs & Services > Credentials**
4. Crea **OAuth client ID** (Web application)
5. Configura **Authorized JavaScript origins**:
   ```
   http://localhost:9000          # Desarrollo
   https://tu-dominio.com         # Producción
   ```

### Paso 2: Configurar Variables de Entorno

En `.env`:

```bash
GOOGLE_AUTH_ENABLED=true
GOOGLE_AUTH_CLIENT_ID=tu-client-id.apps.googleusercontent.com
GOOGLE_AUTH_ALLOWED_DOMAINS=tu-dominio.com,otro-dominio.com
GOOGLE_AUTH_AUTO_CREATE=true
```

### Paso 3: Actualizar docker-compose.yml

Añadir a `&default-back-environment`:

```yaml
x-environment:
  &default-back-environment
  # ... otras variables ...
  GOOGLE_AUTH_ENABLED: "${GOOGLE_AUTH_ENABLED}"
  GOOGLE_AUTH_CLIENT_IDS: "${GOOGLE_AUTH_CLIENT_ID}"
  GOOGLE_AUTH_ALLOWED_DOMAINS: "${GOOGLE_AUTH_ALLOWED_DOMAINS}"
  GOOGLE_AUTH_AUTO_CREATE: "${GOOGLE_AUTH_AUTO_CREATE}"
```

Añadir a servicio `taiga-front`:

```yaml
taiga-front:
  environment:
    # ... otras variables ...
    GOOGLE_AUTH_ENABLED: "${GOOGLE_AUTH_ENABLED}"
    GOOGLE_AUTH_CLIENT_ID: "${GOOGLE_AUTH_CLIENT_ID}"
    GOOGLE_AUTH_ALLOWED_DOMAINS: "${GOOGLE_AUTH_ALLOWED_DOMAINS}"
```

### Paso 4: Reiniciar

```bash
docker compose down
docker compose up -d
```

### Notas Importantes

- El **Client ID debe ser el mismo** en backend y frontend
- **HTTPS es obligatorio** en producción para Google OAuth
- Los dominios en `ALLOWED_DOMAINS` deben coincidir exactamente

---

## Sistema de Métricas

### Configuración

En `.env`:

```bash
# Usar calculador interno (por defecto)
TAIGA_METRICS_PROVIDER=internal
TAIGA_METRICS_SNAPSHOT_TTL=60

# O usar Learning Dashboard externo
TAIGA_METRICS_PROVIDER=external
LD_TAIGA_BACKEND_URL=https://ld-backend.example.com
LD_TAIGA_TIMEOUT=15
```

### Actualizar docker-compose.yml

Añadir a `&default-back-environment`:

```yaml
TAIGA_METRICS_PROVIDER: "${TAIGA_METRICS_PROVIDER}"
TAIGA_METRICS_SNAPSHOT_TTL: "${TAIGA_METRICS_SNAPSHOT_TTL}"
LD_TAIGA_BACKEND_URL: "${LD_TAIGA_BACKEND_URL}"
LD_TAIGA_TIMEOUT: "${LD_TAIGA_TIMEOUT}"
```

---

## Configuraciones Adicionales

### Registro Público

```yaml
# En &default-back-environment
PUBLIC_REGISTER_ENABLED: "True"

# En taiga-front
PUBLIC_REGISTER_ENABLED: "true"
```

### GitHub OAuth

```yaml
# En &default-back-environment
ENABLE_GITHUB_AUTH: "True"
GITHUB_API_CLIENT_ID: "tu-client-id"
GITHUB_API_CLIENT_SECRET: "tu-client-secret"
PUBLIC_REGISTER_ENABLED: "True"

# En taiga-front
ENABLE_GITHUB_AUTH: "true"
GITHUB_CLIENT_ID: "tu-client-id"
PUBLIC_REGISTER_ENABLED: "true"
```

### GitLab OAuth

```yaml
# En &default-back-environment
ENABLE_GITLAB_AUTH: "True"
GITLAB_API_CLIENT_ID: "tu-client-id"
GITLAB_API_CLIENT_SECRET: "tu-client-secret"
GITLAB_URL: "https://gitlab.com"
PUBLIC_REGISTER_ENABLED: "True"

# En taiga-front
ENABLE_GITLAB_AUTH: "true"
GITLAB_CLIENT_ID: "tu-client-id"
GITLAB_URL: "https://gitlab.com"
PUBLIC_REGISTER_ENABLED: "true"
```

### Slack Integration

```yaml
# En &default-back-environment
ENABLE_SLACK: "True"

# En taiga-front
ENABLE_SLACK: "true"
```

---

## Configuración Avanzada

### Mapear archivos de configuración

**Backend `config.py`:**

```bash
cp ../taiga-back/settings/config.py.prod.example config.py
# Editar config.py
```

En `docker-compose.yml`:
```yaml
volumes:
  - ./config.py:/taiga-back/settings/config.py
```

**Frontend `conf.json`:**

```bash
cp ../taiga-front/conf/conf.example.json conf.json
# Editar conf.json
```

---

## Proxy y SSL

### Nginx como Proxy Reverso

```nginx
server {
    server_name taiga.example.com;

    location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Scheme $scheme;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_redirect off;
        proxy_pass http://localhost:9000/;
    }

    location /events {
        proxy_pass http://localhost:9000/events;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_connect_timeout 7d;
        proxy_send_timeout 7d;
        proxy_read_timeout 7d;
    }

    # SSL
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/taiga.example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/taiga.example.com/privkey.pem;
}

server {
    listen 80;
    server_name taiga.example.com;
    return 301 https://$server_name$request_uri;
}
```

---

## Producción

### Checklist de Producción

- [ ] Cambiar `SECRET_KEY` a valor único de 64+ caracteres
- [ ] Cambiar `POSTGRES_PASSWORD` a contraseña segura
- [ ] Configurar `TAIGA_SCHEME=https`
- [ ] Configurar `WEBSOCKETS_SCHEME=wss`
- [ ] Configurar email SMTP real
- [ ] Configurar proxy reverso con SSL
- [ ] Configurar backups de base de datos
- [ ] Cambiar `RABBITMQ_PASS` y `RABBITMQ_ERLANG_COOKIE`
- [ ] Configurar dominios de Google OAuth si se usa

### Generar Claves Seguras

```bash
# Generar SECRET_KEY
python3 -c "import secrets; print(secrets.token_urlsafe(64))"

# Generar contraseñas
openssl rand -base64 32
```

### Backups

```bash
# Backup de base de datos
docker compose exec taiga-db pg_dump -U taiga taiga > backup.sql

# Backup de media
docker compose cp taiga-back:/taiga-back/media ./media-backup
```

---

## Troubleshooting

### Los contenedores no inician

```bash
docker compose logs -f
docker compose ps
```

### Error de conexión a base de datos

```bash
docker compose exec taiga-db pg_isready -U taiga
```

### Google SSO no funciona

1. Verificar URLs en Google Console
2. Verificar `GOOGLE_AUTH_CLIENT_ID`
3. Verificar dominios configurados

### WebSocket no conecta

- Verificar `WEBSOCKETS_SCHEME` coincida con SSL
- Verificar proxy pase headers de WebSocket

### Error CORS

- Añadir dominio a `CORS_ALLOWED_ORIGINS`

---

## Comandos Útiles

```bash
# Iniciar
docker compose up -d

# Parar
docker compose down

# Ver logs
docker compose logs -f [servicio]

# Ejecutar comando de gestión
./taiga-manage.sh [comando]

# Crear superusuario
./taiga-manage.sh createsuperuser

# Reconstruir
docker compose build --no-cache

# Limpiar todo
docker compose down -v
```

---

## Estructura del Proyecto

```
taiga-docker/
├── .env                    # Variables de entorno
├── docker-compose.yml      # Configuración principal
├── docker-compose-inits.yml # Inicialización
├── launch-taiga.sh         # Script de inicio
├── taiga-manage.sh         # Wrapper para manage.py
└── taiga-gateway/
    └── taiga.conf          # Configuración Nginx interno
```

---

## Community

If you **need help to setup Taiga**, want to **talk about some cool enhancement** or you have **some questions**, please go to [Taiga community](https://community.taiga.io/).

## License

Every code patch accepted in Taiga codebase is licensed under [MPL 2.0](LICENSE).

---

*Modificado por Pol Alcoverro* ❤️
