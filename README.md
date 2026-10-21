# Práctica de CI - Docker + GitHub Actions + GHCR

## Objetivo
Construir una aplicación web sencilla, verificarla y publicar una imagen de contenedor identificada por el commit de Git.

## Requisitos
- Docker instalado y funcionando para la prueba local.
- Cuenta de GitHub y repositorio público con rama `main`.

## Paso 1 - Docker local
```bash
docker build -t dds-devops-demo:local .
docker run --rm -p 8080:80 dds-devops-demo:local
```
Abrir http://localhost:8080 y verificar el mensaje **Hola DevOps**.

## Paso 2 - GitHub Actions
Crear un repositorio público, subir todos los archivos manteniendo las carpetas, y realizar un push a `main`.
Ir a **Actions** y verificar que termine correctamente el workflow **CI - Docker y GHCR**.

## Paso 3 - GitHub Container Registry
En GitHub, abrir la página principal del repositorio y ubicar **Packages** en el panel lateral derecho. Allí debería aparecer el paquete publicado. También puede consultarse desde la sección **Packages** del perfil de la cuenta u organización propietaria. La imagen se identifica como `ghcr.io/USUARIO/REPOSITORIO:SHA_CORTO` (siete caracteres).

**Importante**: un paquete GHCR recién publicado puede ser privado aun si el repositorio es público. Para permitir descargas anónimas, abrir los ajustes del paquete y cambiar su visibilidad a público. Esta operación es distinta de hacer público el repositorio.

Ejecutar la imagen publicada desde una máquina con Docker:
```bash
docker run --rm -p 8081:80 ghcr.io/USUARIO/REPOSITORIO:SHA_CORTO
```
Si la imagen todavía no existe localmente, Docker la descargará automáticamente desde GHCR antes de iniciar el contenedor. Abrir http://localhost:8081 y verificar que se muestra la aplicación. Si la imagen es privada, será necesario autenticarse antes de ejecutarla.

## Alcance
Este workflow realiza integración continua y publicación de un artefacto. No realiza despliegue de una aplicación en la nube.
