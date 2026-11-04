# Práctica de CI - Docker + GitHub Actions + GHCR + Docker Hub

## Objetivo
Construir y verificar la aplicación web una sola vez, y publicar la misma imagen en dos registros de contenedores. Cada publicación usa las etiquetas `latest` y los siete primeros caracteres del commit.

## Requisitos
- Docker instalado y funcionando para la prueba local.
- Cuenta de GitHub y repositorio público con rama `main`.
- Cuenta de Docker Hub y repositorio público llamado `dds-devops-demo`.

## Paso 1 - Docker local
```bash
docker build -t dds-devops-demo:local .
docker run --rm -p 8080:80 dds-devops-demo:local
```
Abrir http://localhost:8080 y verificar el mensaje **Hola DevOps**.

## Paso 2 - Configurar GitHub Actions
En Docker Hub, crear el repositorio público `dds-devops-demo` y un access token con permiso de lectura y escritura.

En el repositorio de GitHub, abrir **Settings > Secrets and variables > Actions** y configurar:

- En **Variables**, crear `DOCKERHUB_USERNAME` con el usuario de Docker Hub.
- En **Secrets**, crear `DOCKERHUB_TOKEN` con el access token de Docker Hub.

No guardar el token en el código fuente. El workflow construye y valida la imagen en cada pull request dirigido a `main`. Al recibir cambios en `main`, también publica esa misma imagen; la publicación requiere los ajustes de Docker Hub anteriores. Si se ejecuta manualmente, hacerlo desde la rama `main`.

Cada publicación usa las etiquetas `latest` y `SHA_CORTO` en GHCR y Docker Hub.

## Paso 3 - Verificar las imágenes publicadas

En GitHub, abrir **Packages** en la página principal del repositorio o desde el perfil propietario. La imagen de GHCR es `ghcr.io/USUARIO/REPOSITORIO`. Un paquete GHCR recién publicado puede ser privado; para descargarlo sin iniciar sesión, cambiar su visibilidad a pública en los ajustes del paquete.

En Docker Hub, abrir https://hub.docker.com/r/USUARIO/dds-devops-demo y confirmar que el repositorio está público.

Reemplazar `SHA_CORTO` por los siete primeros caracteres del commit que ejecutó el workflow. Se puede usar tanto la etiqueta del commit como `latest`:

```bash
docker run --rm -p 8081:80 ghcr.io/USUARIO/REPOSITORIO:SHA_CORTO
docker run --rm -p 8082:80 USUARIO/dds-devops-demo:SHA_CORTO
```

Docker descargará cada imagen si aún no está presente localmente. Abrir http://localhost:8081 y http://localhost:8082 para verificar que ambas muestran **Hola DevOps**. Para probar `latest`, sustituir `SHA_CORTO` por `latest`.

## Entrega
- URL de Docker Hub con tag del commit: `USUARIO/dds-devops-demo:SHA_CORTO`.
- Captura de **Actions** donde se vea que el workflow terminó exitosamente y publicó en GHCR y Docker Hub.

## Alcance
Este workflow realiza integración continua y publicación de imágenes. No despliega la aplicación en la nube.
