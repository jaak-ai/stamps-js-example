# Guía de Implementación: Componente de Captura de Documentos JAAK Stamps con JavaScript

¡Bienvenido! En esta guía te acompañaremos paso a paso para integrar en tu página web nuestro más reciente componente de captura de documentos: **`@jaak.ai/stamps`**.

Este nuevo componente reemplaza a la versión anterior (`@jaak.ai/face-detector`), ofreciendo un modelo de inteligencia artificial más ligero, rápido y sensible para detectar y capturar identificaciones oficiales.

## ¿Qué necesitas para empezar?

Solo dos cosas muy simples:
1.  Un **editor de texto** para escribir código (como Visual Studio Code, Sublime Text, o incluso el Bloc de Notas).
2.  Un **navegador web** moderno (como Google Chrome, Firefox o Microsoft Edge) para ver el resultado.

## Paso 1: Crea tu archivo HTML

Primero, vamos a crear la estructura básica de nuestra página web.
Crea un nuevo archivo en tu computadora y nómbralo `index.html`. Luego, copia y pega el siguiente código en él.

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Captura de Documentos con JAAK</title>
</head>
<body>

    </body>
</html>
```

## Paso 2: Agrega el Componente de JAAK a tu página

Para que tu página pueda usar nuestro componente, necesitas incluirlo. La forma más sencilla es a través de nuestra CDN, que es un enlace directo al código del componente.

Añade la siguiente línea de código dentro de la etiqueta `<head>` de tu archivo `index.html`:

```html
<script type="module" src="[https://unpkg.com/@jaak.ai/stamps@2.0.0-beta.1](https://unpkg.com/@jaak.ai/stamps@2.0.0-beta.1)"></script>
```

Tu sección `<head>` debería verse así ahora:

```html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Captura de Documentos con JAAK</title>

    <script type="module" src="[https://unpkg.com/@jaak.ai/stamps@2.0.0-beta.1](https://unpkg.com/@jaak.ai/stamps@2.0.0-beta.1)"></script>
</head>
```

## Paso 3: Define la Estructura de la Página (HTML)

Ahora, vamos a añadir los elementos visibles en la página: un título, el espacio donde aparecerá la cámara, los botones de control y una sección para mostrar las imágenes capturadas.

Copia el siguiente código y pégalo dentro de la etiqueta `<body>`:

```html
<div class="content">
    <div class="card">
        <div class="card-title">
            <h1>Document Detector</h1>
        </div>
        <div class="frame">
            <div class="component-container">
                <jaak-stamps id="detector" debug="false"></jaak-stamps>
            </div>
        </div>
    </div>
</div>

<div class="button-group">
    <div class="button-with-desc">
        <button class="card-btn" onclick="startCapture()">Iniciar Captura</button>
    </div>
    <div class="button-with-desc">
        <button class="card-btn" onclick="stopCapture()">Detener</button>
    </div>
    <div class="button-with-desc">
        <button class="card-btn" onclick="getImages()">Ver Imágenes</button>
    </div>
</div>

<div id="captured-images-container" class="images-container">
    <div class="images-section front-section">
        <h2>Frente del Documento</h2>
        <div class="image-row">
            <div class="image-card">
                <h3>Documento Recortado</h3>
                <div class="image-placeholder">
                    <img id="front-cropped" src="" alt="Documento recortado del frente" />
                </div>
            </div>
        </div>
    </div>
    
    <div class="images-section back-section">
        <h2>Reverso del Documento</h2>
        <div class="image-row">
            <div class="image-placeholder">
                <img id="back-cropped" src="" alt="Documento recortado del reverso" />
            </div>
        </div>
    </div>
</div>
```

## Paso 4: Añade Estilos para que se vea increíble (CSS)

Para que nuestra página no se vea simple, vamos a agregar estilos. Esto definirá los colores, tamaños y la disposición de los elementos.

Copia y pega el siguiente bloque de código justo antes de la etiqueta de cierre `</head>`:

```html
<style>
    body, h1, h2, h3, p, button {
        font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
        background-color: #f8f9fc;
        color: #202945;
    }
    .button-group {
        display: flex;
        justify-content: center;
        gap: 2rem;
        margin: 2rem auto 0 auto;
        width: 100%;
        max-width: 900px;
        flex-wrap: wrap;
    }
    .button-with-desc {
        display: flex;
        flex-direction: column;
        align-items: center;
        min-width: 180px;
        flex: 1 1 0;
    }
    .btn-desc {
        margin-top: 0.8rem;
        font-size: 0.97rem;
        color: #4e5a7a;
        text-align: center;
        background: linear-gradient(90deg, #f7faff 60%, #e8f0ff 100%);
        border-radius: 6px;
        padding: 0.7em 1em 0.7em 1em;
        box-shadow: 0 2px 12px 0 rgba(32, 41, 69, 0.08);
    }
    .images-container {
        width: 90%;
        max-width: 900px;
        margin: 2rem auto;
        display: none; /* Oculto por defecto */
        flex-direction: column;
        gap: 2rem;
    }
    .images-section {
        border-radius: 10px;
        border: solid 1px #e5e5e5;
        box-shadow: 0 10px 30px 0 rgba(32, 41, 69, 0.08);
        padding: 1.5rem;
        background: #fff;
    }
    .images-section h2 {
        text-align: center;
        font-weight: 500;
        margin-bottom: 1.2rem;
    }
    .image-row {
        display: flex;
        gap: 2rem;
        justify-content: center;
    }
    .image-card {
        flex: 1;
        min-width: 280px;
        max-width: 400px;
        display: flex;
        flex-direction: column;
        align-items: center;
    }
    .image-card h3 {
        font-size: 1.2rem;
        margin-bottom: 0.8rem;
        font-weight: 500;
    }
    .image-placeholder {
        width: 100%;
        height: 240px;
        border-radius: 8px;
        background: #f7faff;
        display: flex;
        align-items: center;
        justify-content: center;
        border: 1px dashed #d1e4ff;
        overflow: hidden;
    }
    .image-placeholder img {
        max-width: 100%;
        max-height: 100%;
        object-fit: contain;
    }
    .card-btn {
        border-radius: 8px;
        border: 1px solid #e5e5e5;
        background: #fff;
        color: #202945;
        box-shadow: 0 4px 16px 0 rgba(32, 41, 69, 0.08);
        padding: 0.75rem 1.5rem;
        font-size: 1rem;
        font-weight: 500;
        cursor: pointer;
        transition: all 0.2s;
    }
    .card-btn:hover {
        background: #202945;
        color: #fff;
        box-shadow: 0 8px 24px 0 rgba(32, 41, 69, 0.18);
    }
    .content {
        min-height: 70vh;
        display: flex;
        align-items: center;
        justify-content: center;
        padding: 10px;
        margin-top: 1rem;
    }
    .card {
        border-radius: 10px;
        border: solid 1px #e5e5e5;
        box-shadow: 0 10px 40px 0 rgba(32, 41, 69, 0.1);
        height: 100%;
        max-height: 560px;
        width: 80%;
    }
    .card-title {
        text-align: center;
        margin: 20px 0;
    }
    .frame {
        align-items: center;
        overflow: hidden;
        position: relative;
        width: 100%;
        height: calc(560px - 80px);
    }
    .component-container {
        position: absolute;
        display: flex;
        align-items: center;
        left: 0; top: 0; bottom: 0; right: 0;
    }
</style>
```

## Paso 5: Dale vida con JavaScript

Aquí es donde ocurre la magia. El código JavaScript le dirá al navegador cómo interactuar con el componente de JAAK.

Copia y pega este bloque de código justo antes de la etiqueta de cierre `</body>`:

```html
<script>
    // 1. Nos conectamos con el componente en el HTML
    const detector = document.getElementById('detector');

    // 2. Escuchamos "notificaciones" del componente
    
    // Se activa cuando el componente está listo para usarse
    detector.addEventListener('isReady', (event) => {
        console.log('El componente está listo:', event.detail);
    });
    
    // Se activa cuando la captura (frente y reverso) ha terminado
    detector.addEventListener('captureCompleted', (event) => {
        console.log('¡Captura completada!', event.detail);
        alert('¡Captura completada! Haz clic en "Ver Imágenes" para ver el resultado.');
    });

    // 3. Creamos las funciones para nuestros botones

    function startCapture() {
        console.log('Iniciando captura...');
        detector.startCapture();
    }

    function stopCapture() {
        console.log('Deteniendo captura...');
        detector.stopCapture();
    }
    
    async function getImages() {
        try {
            const images = await detector.getCapturedImages();
            console.log('Imágenes obtenidas:', images);
            displayCapturedImages(images);
        } catch (error) {
            alert('Aún no se han capturado las imágenes. Completa el proceso primero.');
            console.error('Error al obtener imágenes:', error.message);
        }
    }
    
    // 4. Función para mostrar las imágenes en la página
    function displayCapturedImages(images) {
        const container = document.getElementById('captured-images-container');
        container.style.display = 'flex'; // Hacemos visible el contenedor
        
        // Función pequeña para poner una imagen en su lugar
        function displayImage(imageId, base64Data) {
            const imgElement = document.getElementById(imageId);
            if (base64Data) {
                // El formato Base64 es un texto que representa la imagen
                imgElement.src = 'data:image/jpeg;base64,' + base64Data;
            }
        }
        
        // Mostramos la imagen recortada del frente y del reverso
        if (images.front) {
            displayImage('front-cropped', images.front.cropped);
        }
        if (images.back) {
            displayImage('back-cropped', images.back.cropped);
        }
        
        // Hacemos scroll para que el usuario vea las imágenes
        container.scrollIntoView({ behavior: 'smooth', block: 'start' });
    }
</script>
```

## Paso 6: ¡Pruébalo!

¡Listo! Has completado todos los pasos. Ahora, guarda tu archivo `index.html` y ábrelo con tu navegador web. La forma más fácil es hacer doble clic sobre el archivo.

Verás la página con el componente de captura. Sigue estos pasos para probarlo:
1.  **Haz clic en "Iniciar Captura"**. Tu navegador te pedirá permiso para usar la cámara. Acéptalo.
2.  Apunta la cámara de tu dispositivo hacia el **frente** de una identificación (INE, licencia, etc.). El componente la detectará automáticamente.
3.  Cuando te lo indique, voltea la identificación y apunta al **reverso**.
4.  Una vez que el proceso termine, recibirás una alerta.
5.  Haz clic en el botón **"Ver Imágenes"** para mostrar las capturas en la parte inferior de la página.

¡Felicidades! Has implementado con éxito el componente de captura de documentos de JAAK. Desde aquí, puedes empezar a integrar esta funcionalidad con la lógica de tu negocio. Si tienes alguna duda, recuerda que en JAAK estamos para ayudarte a construir soluciones seguras y confiables.