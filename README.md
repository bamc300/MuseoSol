# Museo Arqueologico Eliecer Silva Celis

## Introduction

Este proyecto está destinado a cumplir dos objetivos principales: el primero es que es un sitio web genérico, receptivo y con un estilo completo para usar como punto de partida para su propio desarrollo de Apostrophe. El segundo es una demostración de una compilación de Apostrophe lista para producción con todas las funciones: una serie de widgets (tanto simples como complejos), redes de contenido relacionado (piezas), taxonomía de contenido personalizado, importación de piezas, mapas, configuración avanzada de Apostrophe, etc. mientras organizamos el código de la forma en que nosotros, los mantenedores de ApostropheCMS, lo hacemos en producción.

## Getting Started

Debes [configurar tu entorno](https://apostrophecms.org/docs/tutorials/getting-started/setting-up-your-environment.html) con las necesidades a desarrollar con Apostrophe.

Ejecutar

```

cd MuseoSol
npm install
node app apostrophe-users:add admin admin
[ingrese la contraseña cuando se le solicite]
node app
```

Ahora debería poder acceder al sitio en: [http://localhost:3000](http://localhost:3000), e inicia sesión para empezar editar visitando [/login](http://localhost:3000/login).

Ver el [Apostrophe tutorials](https://apostrophecms.org/docs/tutorials/getting-started/index.html) para más información.

## Request for Feedback

Actualmente estamos buscando comentarios sobre lo que haría que esta sea una demostración más útil y un repositorio más amigable para aquellos que recién comienzan con Apostrophe.

Algunas cosas que estamos tratando de lograr:
- Mantenibilidad y legibilidad
- organización
- Nomenclatura clara
- Alcanzando un punto ideal entre amigable para principiantes e instructivo para intermedios.

Algunas cosas que intentamos evitar:
- Herramientas de desarrollo excesivas que no son específicas de Apostrophe
- Estilo de codificación inconsistente
- Prácticas que no seguiríamos en los proyectos de nuestros propios clientes

## Overview of Scope

Este sitio de demostración está diseñado para ilustrar las necesidades básicas de un sitio web de museo de arte. Además del concepto de páginas, incluye los siguientes tipos de contenido:

- Colecciones
- Galeria
- Tipos de objetos ("Estatua", "Escultura", etc.)
- Ubicaciones
- Miembros del personal del museo
- Artículos (Blog)
- Eventos

Las relaciones entre los tipos de contenido se ven así:

```

                               +---------------+
                               |               |
                               |               |
         +--------------------->  Colecciones  |
         |                     |               |
         |                     |               |
         |                     +--+---------+--+
         |                        |         |
         |                        |         |
         v                        v         v
   
+----------------+   +---------------+   +--------------+    +--------------+
|                |   |               |   |              |    |              |
|                |   |               |   |              |    |              |
| Tipo de Objeto |   |   Ubicacion   |   |   Galeria    |    |   Articulos  |
|                |   |               |   |              |    |              |
|                |   |               |   |              |    |              |
+----------------+   +---------------+   +--------------+    +--------------+

                                 ^
                                 |
                     +-----------+---+   +----------------+
                     |               |   |                |
                     |               |   |                |
                     |    Eventos    |   | Equipo trabajo |
                     |               |   |                |
                     |               |   |                |
                     +---------------+   +----------------+

```

Estas relaciones están representadas con Apostrophe [joins](https://apostrophecms.org/docs/tutorials/getting-started/schema-guide.html#code-join-by-array-code) entre tipos de piezas.

Junto con los tipos de piezas preconfigurados, este proyecto también incluye una variedad de widgets que puede usar para ilustrar su contenido en las páginas. Estos van desde varias formas de mostrar imágenes, características y marquesinas, extractos de piezas existentes y widgets de diseño que le permiten anidar widgets dentro de otros widgets.

## Technical Details

### Front-end assets
Todo el CSS de origen está escrito en LESS, que es el preprocesador de CSS incluido con Apostrophe. Todo el CSS de origen reside en `/lib/modules/apostrophe-assets/public/css` y está organizado según el [ITCSS method](https://www.xfive.co/blog/itcss-scalable-maintainable-css-architecture/). Tenga en cuenta que también puede usar Webpack y SASS en su propio proyecto, siempre que genere un archivo CSS como salida y lo envíe a Apostrophe como activo.

#### Exceptions
`apostrophe-widgets` Los módulos que requieren que JS front-end se ejecute cada vez que el módulo se 'prepara' (se carga por primera vez, cambia) tienen un 'reproductor' que Apostrophe espera encontrar en el módulo `/public/js/always.js` archivo de forma predeterminada. El widget de presentación de diapositivas es un buen ejemplo de esto  `/lib/modules/slideshow-widgets`. Para obtener más información sobre este patrón, consulte [adding a JavaScript widget player on the browser side](https://apostrophecms.org/docs/tutorials/getting-started/custom-widgets.html#adding-a-java-script-widget-player-on-the-browser-side)

### MapQuest API + locations piece type
Este sitio tiene widgets de mapas integrados y funcionalidad de codificación geográfica para su `locations` tipo de pieza. Ejecuta ambas operaciones a través de la API gratuita de MapQuest.. [You can register a key and secret here](https://developer.mapquest.com/plan_purchase/steps/business_edition/business_edition_free/register).

> No es necesario que ingrese una "URL de devolución de llamada" al registrarse y generar su clave API. Simplemente deje ese campo en blanco tipo de pieza. Ejecuta ambas operaciones a través de la API gratuita de MapQuest..

#### MapQuest configuration
Este sitio espera que la clave y el secreto de MapQuest formen parte del objeto `options` para el tipo de pieza` locations`. Este repositorio no viene con una clave y un secreto, por lo que verá advertencias de la consola del servidor y del cliente cuando haga cosas que las requieran.

Puede agregar su clave y secreto de esta manera:

En `/app.js`

```

const apos = require('apostrophe')({
  shortName: 'apostrophe-open-museum',
  modules: {
    // ... other module configurations
    'locations': {
      'mapQuest': {
        key: 'xxxxxxxx',
        secret: 'yyyyyyy'
      }
    }
  };
```

También puede colocar una configuración como esta en el propio archivo index.js del módulo, es decir, `lib/modules/locations/index.js`

** No debe poner las credenciales de API en repositorios públicos. ** [This guide illustrates creating environment-specific module settings](https://apostrophecms.org/docs/tutorials/getting-started/settings.html#changing-the-value-for-a-specific-server-only) Sin agregarlos a su repositorio.

### Using piece types as taxonomy

En la mayoría de los casos de uso de "piezas de apóstrofo", el desarrollador desea poder aprovechar la visualización en pantalla de ese contenido. Por ejemplo, cuando tiene un blog, espera que haya páginas que representen publicaciones de blog y widgets que muestren extractos de esas publicaciones. También esperaría una "página de blog" que muestre todas las publicaciones, con la capacidad de usar paginatio o desplazamiento infinito para llegar al contenido más antiguo.

Pero las piezas tienen otras aplicaciones. También puede utilizar "metapiezas" para crear una taxonomía de las piezas visualizadas. En esta demostración, el tipo de pieza `categorías-tipos de objetos` es una" meta pieza "que no se representa en la pantalla, sino que se une a las` obras de arte` para ayudar a clasificarlas.

El beneficio de hacer este tipo de categorización como una pieza en lugar de una etiqueta es que el conjunto de categorías no se contamina por el panorama global de etiquetas, y solo aquellos con permiso para editar las categorías pueden cambiar la lista de posibilidades. También obtiene la interfaz de administrador familiar para administrar sus meta piezas.

[As with tags, you get built-in filtering of your joins with some added configuration](https://apostrophecms.org/docs/tutorials/intermediate/cursors.html#filtering-joins-browsing-profiles-by-market).

Un ejemplo completo de esto se ilustra en el esquema de `artworks` `/lib/modules/artworks/index.js`

