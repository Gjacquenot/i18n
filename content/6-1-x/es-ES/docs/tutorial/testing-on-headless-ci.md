# Pruebas de sistemas de CI sin cabeceras (Travis CI, Jenkins)

Al estar basado en Chromium, Electron requiere un controlador para funcionar. Si chromium no puede encontrar un controlador de pantalla, Electron no se lanzará y por lo tanto no ejecuta ningunas de sus pruebas, independientemente de como usted las está corriendo. Probar aplicaciones basadas en Electron en Travis, Circle, Jenkins o sistemas similares requiere un poco de configuración. En esencia, necesitamos un controlador de pantalla virtual.

## Configurando un servidor de pantalla virtual

Primero, instala [Xvfb](https://en.wikipedia.org/wiki/Xvfb). Es un framebuffer virtual, implementando el protocolo de servidor de pantalla X11 - realiza todas las operaciones gráficas en la memoria sin mostrar nada en el monitor, que es exactamente lo que necesitamos.

Luego, crea una pantalla xvfb virtual y exporta una variable de entorno llamada PANTALLA que apunta a él. Chromium en electron buscará automáticamente por `$DISPLAY`, así que su aplicación no requerirá más configuraciones. Este paso puede ser automatizado con el [xvfb-maybe](https://github.com/paulcbetts/xvfb-maybe) de Paul Betts: Anteponga los comandos de su prueba con `xvfb-maybe` y la pequeña herramienta configurará automáticamente xvfb, si es requerido por el sistema. En Windows o macOS, no hará nada.

```sh
## En Windows o macOS, esto invoca electron-mocha
## En Linux, si estamos en un entorno sin cabeza, esto será equivalente
## a xvfb-run electron-mocha ./test/*.js
xvfb-maybe electron-mocha ./test/*.js
```

### Travis CI

En Travis, su `. travis.yml` debería verse más o menos así:

```yml
addons:
  apt:
    packages:
      - xvfb

install:
  - export DISPLAY=':99.0'
  - Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 &
```

### Jenkins

Para Jenkins, un [plugin de Xvfb está disponible](https://wiki.jenkins-ci.org/display/JENKINS/Xvfb+Plugin).

### Circle CI

Circle CI es impresionante y tiene xvfb and `$DISPLAY` [ya configurados, por lo que no es necesaria ninguna configuración adicional](https://circleci.com/docs/environment#browsers).

### AppVeyor

AppVeyor corre en Windows, soportando Selenium, Chromium, electron y herramientas fuera de la caja similares - no se requiere configuración.
