# Guía para Migrar tu Página HTML/CSS a un Tema de Shopify

Esta guía te ayudará a convertir el sitio web estático que hemos creado (`index.html` y `style.css`) en un tema funcional y personalizable de Shopify.

## Introducción: ¿Qué vamos a hacer?

Shopify no usa HTML directamente. Usa un lenguaje de plantillas llamado **Liquid**. Vamos a "traducir" nuestro código HTML a la estructura de Shopify y a usar Liquid para que puedas gestionar el contenido de tu tienda (textos, imágenes, etc.) desde el panel de administración de Shopify.

La clave es dividir nuestra página de una sola pieza (`index.html`) en componentes reutilizables que Shopify llama **Sections** (Secciones).

---

## Paso 1: Preparar tu Entorno de Shopify

1.  **Crea una Tienda de Desarrollo (Opcional pero Recomendado):** Si aún no tienes una tienda, puedes crear una tienda de desarrollo gratuita en [Shopify Partners](https://www.shopify.com/partners). Es el entorno perfecto para construir y probar temas sin costo.
2.  **Instala un Tema Base Limpio:** Ve a `Tienda Online` > `Temas`. En la biblioteca de temas, busca el tema gratuito de Shopify llamado **"Dawn"**. Instálalo.
3.  **Duplica el Tema:** Una vez instalado, haz clic en los tres puntos (`...`) junto a "Dawn" y selecciona **"Duplicar"**. Trabajaremos sobre esta copia para no dañar el original. Puedes renombrar la copia a "JC Car Body Work Theme" para identificarla.

---

## Paso 2: Subir tus Archivos (Assets)

Todos nuestros archivos estáticos (imágenes, logo y el archivo CSS) deben subirse a la carpeta `assets` de tu tema de Shopify.

1.  En tu panel de Shopify, ve a `Tienda Online` > `Temas`.
2.  En el tema que duplicaste, haz clic en `...` > **"Editar código"**.
3.  Se abrirá el editor de código de Shopify. A la izquierda, verás una lista de carpetas. Busca y haz clic en la carpeta `assets`.
4.  Haz clic en **"Añadir un nuevo activo"**. Sube, uno por uno, los siguientes archivos:
    *   `logo_.png`
    *   Todo el contenido de la carpeta `assets/images` (`CarPainting.png`, `carDetailing.png`, etc.).
    *   Tu archivo `style.css`.

---

## Paso 3: Configurar la Plantilla Principal (`theme.liquid`)

El archivo `layout/theme.liquid` es el esqueleto principal de **todas** las páginas de tu tienda. Aquí pondremos el `<head>` y el `<footer>` de nuestro HTML.

1.  En el editor de código, abre el archivo `layout/theme.liquid`.
2.  **Reemplaza el contenido del `<head>`:**
    *   Copia todo el contenido de la etiqueta `<head>` de tu archivo `index.html`.
    *   Pega y reemplaza el contenido del `<head>` en `theme.liquid`.
    *   **¡MUY IMPORTANTE!** Justo antes de la etiqueta de cierre `</head>`, asegúrate de que esta línea de Liquid esté presente. Es esencial para que Shopify funcione:
        ```liquid
        {{ content_for_header }}
        ```
    *   **Actualiza el enlace a tu CSS:** Busca la línea `<link rel="stylesheet" href="style.css">` y modifícala para que use el filtro de Liquid `asset_url`. Así es como Shopify encuentra tus archivos en la carpeta `assets`:
        ```liquid
        <link rel="stylesheet" href="{{ 'style.css' | asset_url }}">
        ```
3.  **Configura el `<body>`:**
    *   El `<body>` de `theme.liquid` debe contener el header y el footer, ya que son comunes en todas las páginas. El contenido principal de cada página se cargará dinámicamente donde pongamos `{{ content_for_layout }}`.
    *   Por ahora, para simplificar, deja el `<body>` como está, pero asegúrate de que contenga la variable `{{ content_for_layout }}`. Más adelante crearemos el header y el footer como secciones separadas.

---

## Paso 4: Crear las Secciones

Ahora dividiremos nuestro `index.html` en bloques. Por cada bloque de nuestra página (Hero, Services, Gallery, etc.), crearemos un archivo en la carpeta `sections`.

1.  En el editor de código, busca la carpeta `sections`.
2.  Haz clic en **"Añadir una nueva sección"**.
3.  Nombra el archivo, por ejemplo, `jc-hero.liquid`. Asegúrate de que la extensión sea `.liquid`.

Repite este proceso para cada parte de la página:

*   `jc-header.liquid`
*   `jc-hero.liquid`
*   `jc-services.liquid`
*   `jc-gallery.liquid`
*   `jc-contact.liquid`
*   `jc-footer.liquid`

**Ahora, copia el código HTML correspondiente de tu `index.html` a cada uno de estos nuevos archivos.**

**Ejemplo para `jc-hero.liquid`:**

```html
<section id="hero">
    <div class="hero-content">
        <h2 class="brand-name">JC CAR BODY WORK</h2>
        <h1>Excellence and Passion in Every Detail</h1>
        <p>We transform your vehicle with high-end detailing and painting services.</p>
        <a href="#contact" class="btn">Get a Quote Now</a>
    </div>
</section>

{% schema %}
{
  "name": "Hero Banner",
  "settings": []
}
{% endschema %}
```

*   **Actualiza las rutas de las imágenes:** En cada sección, donde veas una imagen (ej. `<img src="assets/logo_.png">`), debes cambiar la ruta para que use Liquid, igual que hicimos con el CSS:
    ```html
    <!-- ANTES -->
    <img src="assets/logo_.png" alt="JC CAR BODY WORK Logo">

    <!-- DESPUÉS -->
    <img src="{{ 'logo_.png' | asset_url }}" alt="JC CAR BODY WORK Logo">
    ```
    Haz esto para **todas** las imágenes.

---

## Paso 5: Construir la Página de Inicio

La página de inicio en los temas modernos de Shopify se construye visualmente desde el personalizador de temas.

1.  Ve a `Tienda Online` > `Temas` y haz clic en el botón **"Personalizar"** de tu tema.
2.  En la barra lateral izquierda, verás las secciones que vienen por defecto. Elimínalas.
3.  Haz clic en **"Añadir sección"** y busca las secciones que acabas de crear (las verás con el nombre que pusiste en el `schema`, ej. "Hero Banner").
4.  Añade tus secciones en el orden correcto: Header, Hero, Services, Gallery, Contact, Footer.

¡Ya deberías ver tu página casi idéntica a la versión HTML!

---

## Paso 6: Hacer el Contenido Editable (La Magia de Shopify)

El verdadero poder de Shopify es poder cambiar textos e imágenes sin tocar el código. Haremos esto usando el `schema` que añadimos a cada sección.

Vamos a hacer editable el texto de la sección "Hero" como ejemplo.

1.  Abre el archivo `sections/jc-hero.liquid`.
2.  Modifica la parte del `schema` para añadir "settings" (configuraciones).

```liquid
{% schema %}
{
  "name": "Hero Banner",
  "settings": [
    {
      "type": "text",
      "id": "brand_name",
      "label": "Brand Name",
      "default": "JC CAR BODY WORK"
    },
    {
      "type": "text",
      "id": "main_heading",
      "label": "Main Heading",
      "default": "Excellence and Passion in Every Detail"
    },
    {
      "type": "textarea",
      "id": "subheading",
      "label": "Subheading",
      "default": "We transform your vehicle with high-end detailing and painting services."
    }
  ]
}
{% endschema %}
```

3.  Ahora, reemplaza el texto estático en el HTML con las variables de Liquid que acabas de definir. Las variables usan la sintaxis `{{ section.settings.ID }}`, donde `ID` es el `id` que definiste en el schema.

```html
<section id="hero">
    <div class="hero-content">
        <h2 class="brand-name">{{ section.settings.brand_name }}</h2>
        <h1>{{ section.settings.main_heading }}</h1>
        <p>{{ section.settings.subheading }}</p>
        <a href="#contact" class="btn">Get a Quote Now</a>
    </div>
</section>
```

4.  **Guarda el archivo.** Ahora, si vuelves al personalizador de temas (`Personalizar`) y haces clic en la sección "Hero Banner", verás campos de texto a la derecha para cambiar el "Brand Name", "Main Heading", etc.

**Puedes repetir este proceso para todo el contenido:** los títulos de los servicios, las descripciones, las imágenes de la galería, etc. Usa los diferentes `type` en el schema (`image_picker` para imágenes, `text` para textos, `richtext` o `textarea` para párrafos).

---

## Siguientes Pasos

Esta guía cubre lo esencial. Desde aquí, puedes explorar conceptos más avanzados:

*   **Bloques (`blocks`):** Dentro de una sección, puedes definir "bloques". Por ejemplo, en tu sección de servicios, cada servicio podría ser un bloque. Esto te permitiría añadir, eliminar y reordenar servicios directamente desde el personalizador.
*   **Configuración del Tema (`settings_schema.json`):** Para opciones globales como colores y tipografías que afecten a todo el sitio.
*   **Plantillas de Producto y Colecciones:** Crear el diseño para tus páginas de productos y colecciones.

¡Buena suerte con tu tienda!
