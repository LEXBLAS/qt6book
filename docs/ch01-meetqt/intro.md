# Qt 6 Introduccíon

## Qt Quick

Qt Quick es el término general para la tecnología de interfaz de usuario utilizada en Qt 6. Se introdujo en Qt 5 y ahora se expandió para Qt 6. Qt Quick en sí mismo es una colección de varias tecnologías:

* QML - Lenguaje de marcado para interfaces de usuario
* JavaScript: el lenguaje de secuencias de comandos dinámico
* Qt C++: la biblioteca C++ mejorada altamente portátil

![](./assets/qt6_overview.png)

Similar a HTML, QML es un lenguaje de marcas. Se compone de etiquetas, denominadas tipos en Qt Quick, que están encerradas entre corchetes: `Item {}`. Fue diseñado desde cero para la creación de interfaces de usuario, velocidad y lectura más fácil para los desarrolladores. La interfaz de usuario se puede mejorar aún más utilizando código JavaScript. Qt Quick se puede ampliar fácilmente con su propia funcionalidad nativa utilizando Qt C++. En resumen, la interfaz de usuario declarativa se denomina front-end y las partes nativas se denominan back-end. Esto le permite separar la operación nativa y intensiva en computación de su aplicación de la parte de la interfaz de usuario.

En un proyecto típico, el front-end se desarrolla en QML/JavaScript. El código de back-end, que interactúa con el sistema y hace el trabajo pesado, se desarrolla utilizando Qt C++. Esto permite una división natural entre los más orientados al diseño Por lo general, el back-end se prueba con Qt Test, el marco de prueba de unidades de Qt, y se exporta para que lo usen los desarrolladores de front-end.

## Digerir una interfaz de usuario

Vamos a crear una interfaz de usuario simple usando Qt Quick, que muestra algunos aspectos del lenguaje QML. Al final, tendremos un molino de viento de papel con aspas giratorias.

![](./assets/showcase.png)

Comenzamos con un documento vacío llamado `main.qml`. Todos nuestros archivos QML tendrán el sufijo `.qml`. Como lenguaje de marcado (como HTML), un documento QML debe tener un único tipo raíz. En nuestro caso, este es el tipo `Image` con un ancho y alto basado en la geometría de la imagen de fondo
:

```qml
import QtQuick

Image {
    id: root
    source: "images/background.png"
}
```

Como QML no restringe la elección del tipo para el tipo raíz, usamos un tipo `Imagen` con la propiedad de origen establecida en nuestra imagen de fondo como raíz.

![](./assets/background.png)

::: tip
Cada tipo tiene propiedades. Por ejemplo, una imagen tiene las propiedades "ancho" y "alto", cada una con un recuento de píxeles. También tiene otras propiedades, como "fuente". Dado que el tamaño del tipo de imagen se deriva automáticamente del tamaño de la imagen, no necesitamos establecer las propiedades `width` y `height` nosotros mismos.
:::

Los tipos más estándar se encuentran en el módulo `QtQuick`, que está disponible mediante la declaración de importación al comienzo del archivo `.qml`.

El `id` es una propiedad especial y opcional que contiene un identificador que se puede usar para hacer referencia a su tipo asociado en cualquier otra parte del documento. El uso de `root` como id para el tipo raíz es una convención utilizada en este libro para hacer que la referencia al tipo más alto sea predecible en documentos QML más grandes.

Los elementos de primer plano, que representan el poste y el molinete en la interfaz de usuario, se incluyen como imágenes separadas.


![](./assets/pole.png)

![](./assets/pinwheel.png)

Queremos colocar el poste horizontalmente en el centro del fondo, pero desplazado verticalmente hacia el fondo, y queremos colocar el molinete en el medio del fondo.

Aunque este ejemplo para principiantes solo usa tipos de imagen, a medida que avancemos, creará interfaces de usuario más sofisticadas que se componen de muchos tipos diferentes.

```qml
Image {
    id: root
    ...
    Image {
        id: pole
        anchors.horizontalCenter: parent.horizontalCenter
        anchors.bottom: parent.bottom
        source: "images/pole.png"
    }

    Image {
        id: wheel
        anchors.centerIn: parent
        source: "images/pinwheel.png"
    }
    ...
}
```

Para colocar el molinete en el medio, usamos una propiedad compleja llamada `ancla`. El anclaje le permite especificar relaciones geométricas entre objetos padre y hermano. Por ejemplo, colóqueme en el centro de otro tipo ( `anchors.centerIn: parent` ). Hay relaciones izquierda, derecha, arriba, abajo, centerIn, fill, verticalCenter y horizontalCenter en ambos extremos. Naturalmente, cuando dos o más anclas se usan juntas, deberían complementarse entre sí: no tendría sentido, por ejemplo , para anclar el lado izquierdo de un tipo a la parte superior de otro tipo.

Para el molinete, el anclaje solo requiere un anclaje simple.

::: tip
A veces querrás hacer pequeños ajustes, por ejemplo, para empujar un tipo ligeramente fuera del centro. Esto se puede hacer con `anchors.horizontalCenterOffset` o con `anchors.verticalCenterOffset`. Propiedades de ajuste similares también están disponibles para todos los demás anclas Consulte la documentación para obtener una lista completa de las propiedades de los anclajes.
:::

::: tip    
Colocar una imagen como un tipo secundario de nuestro tipo raíz (la `Imagen`) ilustra un concepto importante de un lenguaje declarativo. Describe la apariencia visual de la interfaz de usuario en el orden de las capas y la agrupación, donde la capa superior (nuestro fondo image) se dibuja primero y las capas secundarias se dibujan encima en el sistema de coordenadas local del tipo contenedor.
:::

Para hacer que el escaparate sea un poco más interesante, hagamos que la escena sea interactiva. La idea es girar la rueda cuando el usuario presiona el mouse en algún lugar de la escena.

Usamos el tipo `MouseArea` y hacemos que cubra toda el área de nuestro tipo raíz.

```qml
Image {
    id: root
    ...
    MouseArea {
        anchors.fill: parent
        onClicked: wheel.rotation += 90
    }
    ...
}
```

El área del mouse emite señales cuando el usuario hace clic dentro del área que cubre. Puede conectarse a esta señal anulando la función 'onClicked'. Cuando se conecta una señal, significa que la función (o funciones) a las que corresponde se llama cada vez que la señal es emitida En este caso, decimos que cuando hay un clic del mouse en el área del mouse, el tipo cuyo 'id' es 'rueda' (es decir, la imagen del molinete) debe girar +90 grados.

::: tip
Esta técnica funciona para todas las señales, con la convención de nomenclatura `on` + `SignalName` en caso de título. Además, todas las propiedades emiten una señal cuando cambia su valor. Para estas señales, la convención de nomenclatura es:
:::

```js
    `on${property}Changed`
```

Por ejemplo, si se cambia una propiedad `width`, puede observarla con `onWidthChanged: print(width)`.

La rueda ahora girará cada vez que el usuario haga clic, pero la rotación se lleva a cabo en un salto, en lugar de un movimiento fluido a lo largo del tiempo. Podemos lograr un movimiento suave mediante la animación. Una animación define cómo se produce un cambio de propiedad durante un período de tiempo. Para habilita esto, usamos la propiedad del tipo `Animación` llamada `Behavior`. El `Behavior` especifica una animación para una propiedad definida para cada cambio aplicado a esa propiedad. En otras palabras, cada vez que la propiedad cambia, la animación se ejecuta. Esto es solo una de las muchas formas de hacer animación en QML.

```qml
Image {
    id: root
    Image {
        id: wheel
        Behavior on rotation {
            NumberAnimation {
                duration: 250
            }
        }
    }
}
```

Ahora, cada vez que cambie la propiedad de rotación de la rueda, se animará mediante una "Animación numérica" ​​con una duración de 250 ms. Por lo tanto, cada giro de 90 grados tardará 250 ms, lo que producirá un giro suave y agradable.

![](./assets/scene2.png)

::: tip
En realidad, no verá la rueda borrosa. Esto es solo para indicar la rotación. (Hay una rueda borrosa en la carpeta de activos, en caso de que desee experimentar con ella).
:::

Ahora la rueda se ve mucho mejor y se comporta muy bien, además de proporcionar una visión muy breve de los conceptos básicos de cómo funciona la programación Qt Quick.

