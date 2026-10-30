# Obrador — releases

Acá viven **solo los instaladores y los archivos de actualización** de la app de
escritorio de Obrador. El código está en
[`Gabotronico/Panaderia`](https://github.com/Gabotronico/Panaderia).

## Importante: no se commitea ningún binario

Los `.exe` **no** van al repositorio. Cada versión se publica como un
**Release** de GitHub; el repo solo guarda esta documentación.

Esto no es una preferencia: la actualización automática de la app
(`electron-updater`) lee el release más reciente del proveedor configurado y
necesita, además del instalador, el archivo `latest.yml` que genera
electron-builder. Un `.exe` commiteado en una carpeta no sirve para actualizar.

## Qué contiene un release

| Archivo | Para qué |
|---|---|
| `Obrador-Setup-<version>.exe` | Instalador NSIS. Es lo que descarga la web. |
| `latest.yml` | Manifiesto que lee el actualizador. Sin él, la app no ve la versión. |
| `*.exe.blockmap` | Descarga diferencial (opcional pero recomendado). |

Los tres los genera `php artisan native:publish` automáticamente.

## Cómo se publica una versión

En la máquina Windows, desde el proyecto `Panaderia`:

1. Subir la versión en `.env` (`NATIVEPHP_APP_VERSION=1.0.16`), en
   `config/nativephp.php` y en `.env.example`.
2. Configurar el token (una vez por sesión):

   ```powershell
   $env:GH_TOKEN = "<token con permiso repo>"
   ```

3. Publicar:

   ```powershell
   php artisan native:publish win --x64
   ```

Eso compila, firma el release y lo sube acá. El tag queda con `v` delante
(`v1.0.16`) porque así está configurado `vPrefixedTagName`.

## Cómo lo consume la app

- **Web** (`obrador.incgroupbo.com`): el botón *Descargar* del encabezado abre
  un modal con la última versión publicada acá y el enlace directo al `.exe`.
- **Escritorio**: el botón *Actualizar* aparece solo si hay una versión más
  nueva que la instalada. El modal descarga con barra de progreso y al
  terminar ofrece *Instalar ahora* o *Más tarde*.

La app consulta la API pública de GitHub con un caché de 10 minutos
(`config/actualizaciones.php`).

## Si el repo pasa a privado

La descarga desde la web dejaría de funcionar para el navegador y habría que
proxyearla desde el servidor. El actualizador seguiría andando configurando
`GITHUB_AUTOUPDATE_TOKEN`. Por ahora es público a propósito.
