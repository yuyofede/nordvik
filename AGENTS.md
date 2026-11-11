# AGENTS.md

## Descripción general

Sitio de marketing de Nordvik: seis páginas HTML estáticas escritas a mano más un formulario de contacto en PHP. No hay build, gestor de paquetes, tests, linter, formateador ni CI. Para verificar cambios, sirve los archivos y revísalos en el navegador.

## Vista previa

Desde la raíz del repositorio:

```sh
python3 -m http.server 8000   # http://localhost:8000
```

No hay un intérprete de PHP en `PATH`, así que `contact.php` no se puede probar en local; la vista previa estática cubre todo lo demás.

## Estructura

- Las páginas son independientes, sin plantillas ni includes: `index.html`, `about.html`, `services.html`, `pricing.html`, `case-study.html`, `contact.html`. La cabecera, la navegación, el pie y los fragmentos de analítica están copiados en cada archivo: aplica los cambios en todas las páginas afectadas.
- Todas las páginas deben mantener `<body class="seo_version">`; esa clase activa el esquema SEO de `css/versions.css` (también hay overrides en `style.css` y `css/responsive.css`).
- El orden de las hojas de estilo en cada `<head>` es fijo: `css/bootstrap.min.css` → `style.css` (en la raíz) → `css/colors.css` → `css/versions.css` → `css/responsive.css` → `css/custom.css`. `style.css` importa animate/flaticon/prettyPhoto/owl/font-awesome con `@import`.
- Los ajustes propios del sitio van en `css/custom.css` (se carga al final y actualmente solo contiene un comentario marcador), no en el CSS de terceros ni en el `style.css` del tema.
- `js/all.js` es un bundle de terceros versionado en el repo (jQuery 1.12.4 + Bootstrap y plugins). El comportamiento del sitio está en `js/custom.js`; no edites `all.js` para cambiar lógica del sitio.
- Scripts específicos de página: `js/portfolio.js` + `js/hoverdir.js` en `case-study.html`; Google Maps (`js/mapsed.js` + `js/01-custom-places-example.js`, con la clave de API escrita directamente) en `contact.html`.
- `js/map.js`, `js/scroll.js`, `js/retina.js`, `js/animate.js`, `js/jquery.vide.js` y `js/modernizer.js` no se referencian desde ninguna página. `js/owl.carousel.js`, `js/jquery.prettyPhoto.js` y similares son fuentes sin minificar de librerías ya incluidas en `all.js`.
- `fonts/flaticon.html` es la demo de la librería de iconos, no una página del sitio.

## Formulario de contacto

- `js/custom.js` intercepta `#contactform` y lo envía por AJAX a `contact.php`; el PHP valida y llama a `mail()`. `$address` sigue siendo `example@yourdomain.com`, así que el envío no está configurado.
- El formulario necesita un hosting con PHP. El origen de producción es un despliegue estático en Vercel, donde PHP no se ejecuta.
- Al modificar el formulario, mantén sincronizados los valores de `name`/`id` entre `contact.html`, `js/custom.js` y `contact.php`. Ya hay desajustes: el HTML no tiene `select_price` ni `verify` (el JS los envía igual) y el PHP lee `subject`, que nadie envía.
- `contact.php` está escrito para PHP antiguo: usa `get_magic_quotes_gpc()` (eliminada en PHP 8) y accede a `$_POST` sin comprobar índices.

## SEO y despliegue

- El origen de producción `https://nordvik-kohl.vercel.app` está escrito directamente en `sitemap.xml`, en `robots.txt` y en el canonical de `index.html`; las demás páginas aún no tienen canonical. Si añades o renombras una página, actualiza su enlace canonical y la entrada del sitemap.
- `robots.txt` termina la línea `Sitemap:` con un carácter invisible U+2060 y un espacio; elimínalos si editas ese archivo.
- El despliegue es externo (GitHub `yuyofede/nordvik`, rama `main` → Vercel). En el repositorio no hay `vercel.json`, flujos de CI ni scripts de release.
- `google263aa281860d9c96.html` es la verificación de Google Search Console; no lo borres.
- Las seis páginas enlazan `images/favicon.ico`, que no existe (404).

## Contenido pendiente

- Cinco páginas siguen usando el título de plantilla `FlaxSEO - Responsive HTML5 Template`; `index.html` conserva el título provisional en español `titulo de la paginasive`.
- Solo `index.html` tiene meta description (con la errata `decripcion de la pagina`); el resto está vacía. Quedan restos de texto de relleno en `index.html` y `contact.html`.
- Todas las páginas declaran `<html lang="en">`, pero el contenido de `index.html` está en noruego.
- Google Analytics `G-KY1QKPK9Y6` está en cinco páginas, falta en `case-study.html`, y su fragmento está colocado después de `</head>`.
- Trata todo esto como contenido pendiente, no como una convención.
