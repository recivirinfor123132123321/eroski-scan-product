# Mi compra Eroski

Web para llevar la cuenta de la compra en el supermercado desde el móvil. Escaneas el código de barras de un producto, confirmas cuántos has cogido y cuánto cuesta, y la web va sumando el total. Al terminar, la compra se guarda en un historial.

Es un único archivo HTML (sin build, sin dependencias que instalar) y todos los datos se guardan en tu propio dispositivo.

> Proyecto personal y no oficial. No está afiliado a Eroski.

## Características

- **Escáner de códigos de barras** con la cámara (EAN-13, EAN-8, UPC-A y UPC-E).
- **Doble comprobación de la lectura**: el código solo se acepta si se lee el mismo número dos veces. Si las lecturas no coinciden, o no se detecta nada, se reintenta. Tras 3 intentos fallidos la web pide escribir el número a mano.
- **Búsqueda automática del producto** (nombre y foto) y, si existe, un **precio de referencia**.
- **Confirmación antes de añadir**: eliges la cantidad y confirmas el precio. Si el precio ya se conoce, solo pulsas «Es correcto, añadir» o «Corregir».
- **Lista de la compra** con total en tiempo real, cambio de unidades, edición y borrado con confirmación.
- **Terminar compra**: guarda la compra con su fecha y vacía la lista.
- **Historial** con dos vistas: compras anteriores (con opción de repetirlas) y todos los productos comprados sumados.
- **Sin cuenta ni servidor**: los datos se quedan en el navegador.
- Añadir productos **sin código de barras** (fruta a granel, por ejemplo).

## Cómo usarlo

1. Pulsa **Escanear producto** y apunta al código de barras. Mantén el móvil quieto: la web lo lee dos veces para asegurarse.
2. Revisa el nombre, elige **cuántos has cogido** y confirma el **precio** comparándolo con la etiqueta del lineal.
3. Repite con cada producto. El total aparece siempre arriba.
4. Al acabar, pulsa **Terminar compra**. Se guarda en el **Historial**.

La primera vez que escanees un producto tendrás que escribir el precio si la web no lo conoce. Se guarda y, a partir de entonces, se propone automáticamente.

## Ponerlo en marcha

La cámara del navegador solo funciona en **contextos seguros**: una página servida por `https://` o desde `localhost`. Si abres el archivo directamente (`file://`) o por `http://` desde otra IP, la cámara no se activará, aunque el resto sí funciona y siempre puedes escribir el código a mano.

### Opción 1: publicarlo (recomendado para usarlo en el móvil)

Sube el archivo a cualquier hosting estático con HTTPS:

- **GitHub Pages**: sube `index.html` a un repositorio y activa Pages en _Settings → Pages_.
- **Netlify Drop**: arrastra la carpeta a <https://app.netlify.com/drop>.
- **Cloudflare Pages**, **Vercel**, etc.

Después abre la URL desde el móvil y usa «Añadir a pantalla de inicio» del navegador para tenerla como una app.

### Opción 2: probarlo en local

```bash
python3 -m http.server 8000
```

Abre <http://localhost:8000>. Funciona con la cámara del propio equipo (o del móvil si el servidor corre en el móvil). Desde otro dispositivo de la red local, la cámara no se activará porque no es HTTPS.

## Cómo funciona

### Búsqueda del producto y del precio

Eroski **no ofrece una API pública** de productos ni de precios, y un navegador no puede consultar su web directamente. Por eso la web usa fuentes abiertas:

| Dato                       | Fuente                                             | Notas                                                                              |
| -------------------------- | -------------------------------------------------- | ---------------------------------------------------------------------------------- |
| Nombre y foto              | [Open Food Facts](https://world.openfoodfacts.org) | No incluye todos los productos.                                                    |
| Precio de referencia       | [Open Prices](https://prices.openfoodfacts.org)    | Base colaborativa con pocos datos. Se priorizan los precios anotados en un Eroski. |
| Precio de tu última compra | Tu propio dispositivo                              | Es la fuente más fiable.                                                           |

Orden de prioridad del precio propuesto: **tu última compra → precio de un Eroski en Open Prices → precio de otro comercio**. Los precios de otros comercios se rellenan pero se dejan editables con un aviso, para que se comparen con la etiqueta. **El precio siempre lo confirma la persona.**

También hay un enlace para comprobar el producto en [supermercado.eroski.es](https://supermercado.eroski.es/).

### Doble comprobación del escaneo

1. Se descartan las lecturas cuyo dígito de control no es válido.
2. Una lectura solo se acepta si el mismo número se lee dos veces, separadas al menos 0,35 s.
3. Si las lecturas no coinciden, o pasan 8 s sin confirmar, cuenta como un intento fallido.
4. Tras 3 intentos fallidos se detiene la cámara y se pide escribir el código a mano (con botón para volver a escanear).

Los valores se pueden ajustar en las constantes `MAX_ATTEMPTS`, `NEED_MATCHES`, `MIN_GAP_MS` y `ATTEMPT_MS`, al principio del bloque del escáner en el JavaScript.

### Almacenamiento y privacidad

Todo se guarda con `localStorage` en el navegador:

| Clave                 | Contenido                                              |
| --------------------- | ------------------------------------------------------ |
| `eroski.lista.v1`     | Lista de la compra actual.                             |
| `eroski.catalogo.v1`  | Productos ya escaneados: nombre, foto y último precio. |
| `eroski.historial.v1` | Compras terminadas.                                    |

No hay servidor propio ni cuentas. Las únicas peticiones externas son la consulta a Open Food Facts y Open Prices al escanear un código nuevo, la carga del lector de códigos y las fuentes tipográficas.

Ten en cuenta que:

- Los datos **no se sincronizan** entre dispositivos ni entre navegadores.
- Cada dirección web tiene su propio almacenamiento: si cambias de dominio, empezarás de cero.
- Borrar los datos del navegador borra también la lista y el historial.

## Estructura del proyecto

```
.
├── index.html   # Toda la aplicación: HTML, CSS y JavaScript
└── README.md
```

Si tu archivo se llama `mi-compra-eroski.html`, renómbralo a `index.html` para publicarlo con GitHub Pages.

## Tecnologías y créditos

- HTML, CSS y JavaScript sin frameworks.
- [html5-qrcode](https://github.com/mebjas/html5-qrcode) 2.3.8 (Apache-2.0) para leer los códigos, cargado desde jsDelivr (con unpkg de respaldo). Usa el detector nativo del navegador cuando está disponible.
- [Open Food Facts](https://world.openfoodfacts.org) y [Open Prices](https://prices.openfoodfacts.org), datos abiertos bajo licencia ODbL. Si reutilizas los datos, respeta sus condiciones de atribución.
- Tipografías [Bricolage Grotesque](https://fonts.google.com/specimen/Bricolage+Grotesque) y [Figtree](https://fonts.google.com/specimen/Figtree) desde Google Fonts, con fuentes del sistema como alternativa.

## Limitaciones conocidas

- No obtiene el precio oficial de Eroski automáticamente: depende de tus compras anteriores y de datos colaborativos.
- Open Food Facts no conoce todos los productos, sobre todo marcas blancas nuevas o productos frescos con código propio del supermercado.
- La lectura depende de la cámara, la luz y el navegador. Si no se lee, siempre se puede escribir el código.
- Requiere conexión para buscar productos nuevos y para cargar el lector la primera vez.

## Ideas para más adelante

- Exportar e importar los datos en un archivo, como copia de seguridad.
- Sincronización entre dispositivos.
- Presupuesto máximo con aviso.
- Funcionamiento sin conexión como PWA.

## Licencia

Sin licencia definida todavía. Añade un archivo `LICENSE` con la que prefieras (por ejemplo, MIT).
