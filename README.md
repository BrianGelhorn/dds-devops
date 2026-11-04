# Práctica de CI - Docker + GitHub Actions + GHCR + Docker Hub + Render

## Objetivo
Construir y verificar la aplicación web una sola vez, publicar la misma imagen en GHCR y Docker Hub con las etiquetas `latest` y el SHA corto del commit, y disparar el despliegue de Render después de publicar.

## Requisitos
- Docker instalado y funcionando para la prueba local.
- Cuenta de GitHub y repositorio público con rama `main`.
- Cuenta de Docker Hub y repositorio público `dds-devops-demo`.
- Cuenta gratuita de Render y un Web Service conectado a la imagen de Docker Hub.

## Paso 1 - Docker local
```bash
docker build -t dds-devops-demo:local .
docker run --rm -p 8080:80 dds-devops-demo:local
```
Abrir http://localhost:8080 y verificar el mensaje **Hola DevOps**.

## Paso 2 - Configurar GitHub Actions para publicar las imágenes
En el repositorio de GitHub, abrir **Settings > Secrets and variables > Actions** y configurar:

- En **Variables**, crear `DOCKERHUB_USERNAME` con el usuario de Docker Hub.
- En **Secrets**, crear `DOCKERHUB_TOKEN` con un access token de Docker Hub con permiso de lectura y escritura.

El workflow construye la imagen una vez, valida que sirva la página y publica esa misma imagen en GHCR y Docker Hub con las etiquetas `latest` y `SHA_CORTO`. La publicación se ejecuta en cambios a `main` o al iniciar manualmente el workflow desde `main`.

## Paso 3 - Crear el Web Service en Render
En Render, crear un **Web Service** con **Existing Image** y usar la imagen pública de Docker Hub:

```text
docker.io/broogel/dds-devops-demo:latest
```

La imagen escucha en el puerto 80. Desplegar el servicio y comprobar que la URL pública de Render muestra **Hola DevOps**.

## Paso 4 - Configurar el Deploy Hook
En los ajustes del servicio de Render, copiar el **Deploy Hook URL**. En **Settings > Secrets and variables > Actions** de GitHub, crear el secreto:

- `RENDER_DEPLOY_HOOK_URL`: URL del Deploy Hook del servicio.

Mantener esta URL como secreto. El workflow la llama después de que las publicaciones en GHCR y Docker Hub terminan correctamente. Los servicios basados en una imagen de registro no se redespliegan solo porque cambie la etiqueta de la imagen.

## Paso 5 - Comprobar el despliegue automático
Modificar el texto de `index.html` conservando **Hola DevOps** para que la validación del workflow siga funcionando. Luego, integrar los cambios en `main`.

En **Actions**, verificar que la ejecución termine correctamente y que aparezca el paso **Desplegar en Render**. Esperar a que termine ese despliegue y abrir la URL pública del servicio para confirmar que muestra el nuevo texto. No iniciar un despliegue manual desde Render.

## Verificación de las imágenes
Reemplazar `SHA_CORTO` por los siete primeros caracteres del commit que ejecutó el workflow:

```bash
docker run --rm -p 8081:80 ghcr.io/USUARIO/REPOSITORIO:SHA_CORTO
docker run --rm -p 8082:80 broogel/dds-devops-demo:SHA_CORTO
```

Abrir http://localhost:8081 y http://localhost:8082 y confirmar que ambas muestran la aplicación.

## Entrega
- URL de Docker Hub con tag del commit: `broogel/dds-devops-demo:SHA_CORTO`.
- Captura de **Actions** con la ejecución exitosa y el paso de despliegue en Render.
- URL pública del Web Service de Render.

## Alcance
El workflow realiza integración continua, publica imágenes en dos registros y dispara el despliegue de la aplicación en Render. No cambia el Dockerfile ni la forma en que se construye la aplicación.
