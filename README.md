# Qt6 Book

*Copyright(C) 2012-2021 Johan Thelin and Jürgen Bocklage-Ryannel*

El nuevo hogar para el libro Qt6 (basado en QmlBook)

Siempre puede encontrar la última versión publicada del libro creado en [https://www.qt.io/product/qt6/qml-book](https://www.qt.io/product/qt6/qml-book), y la última instantánea en [https://distracted-dijkstra-f5d508.netlify.app/](https://distracted-dijkstra-f5d508.netlify.app/).

# Contents

1. Construyendo el Libro Localmente
2. Edificio para lanzamiento
3. Para revisores
4. Para autores

# 1. Construyendo el libro localmente

El contenido se construye en un sitio estático usando [VuePress](https://vuepress.vuejs.org/). Los paquetes se gestionan ysing [Yarn](https://yarnpkg.com/).

Para construir los contenidos localmente, ejecute:

```
$ yarnpkg
$ yarnpkg run docs:dev
```

Entonces visita [localhost:8080](http://localhost:8080) para ver el libro.

Para construir los ejemplos, ejecute:

```
$ yarnpkg run examples:build
```

Esto creará el directorio `_examples/` con el archivo build. Se asume que CMake puede encontrar Qt6. Mi línea de comando típica en una máquina Debian Linux se ve así:

```
$ CMAKE_PREFIX_PATH=/path/to/Qt/6.2.0/gcc_64/lib/cmake/ yarnpkg run examples:build
```

Subsequent calls do not need `CMAKE_PREFIX_PATH` to be specified.

# 2. Compilación para lanzamiento(Release)

Para compilar para el lanzamiento, primero cree los documentos, luego empaquete los ejemplos en una bola de alquitrán:

```
$ yarnpkg run docs:build
$ yarnpkg run examples:package
```

Esto crea `examples.tar.gz` en la raíz de su paquete, así como también donde VuePress coloca la salida, es decir, `docs/.vuepress/dist/`.

Tenga en cuenta que el comando `examples:package` asume que existe el directorio `dist/` de VuePress.

# 3. Para los revisores

Elija capítulos para revisar de la [Project Board](https://github.com/qmlbook/qt6book/projects/1). Busque también problemas etiquetados como [Questions](https://github.com/qmlbook/qt6book/issues?q=is%3Aissue+is%3Aopen+label%3Aquestion) in el projecto.

Las revisiones son bienvenidas como problemas o como solicitudes de extracción. ¡Elige el enfoque que sea más fácil para ti!.

# 4. Para los autores

Los capítulos se describen en `docs/.vuepress/config.js`. Etiquete los capítulos como `Qt5`, `Qt6 Draft` y `Qt 6` respectivamente.
