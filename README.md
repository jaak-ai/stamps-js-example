# Documentación Técnica: Jaak Stamps

## Introducción

**Jaak Stamps** es un componente web diseñado para facilitar la detección y captura automática de documentos de identificación en aplicaciones web modernas. Este componente forma parte de un ecosistema de **componentes web interoperables**, compatibles con los principales estándares de la Web. Su arquitectura basada en **shadow DOM**, **slots (ranuras)** y encapsulamiento de estilos permite una integración limpia, segura y mantenible en cualquier entorno con soporte para Web Components.

## Funcionalidades Clave

* **Detección automática en tiempo real:** Identifica documentos de identificación a través de la cámara del dispositivo.
* **Guía visual para posicionamiento:** Ayuda al usuario a alinear el documento para una captura óptima.
* **Clasificación inteligente:** Determina si el documento requiere una captura del reverso, como en el caso de pasaportes.
* **Captura adaptativa:** Se ajusta automáticamente si el documento tiene o no reverso.
* **Doble salida de imágenes:** Proporciona tanto la imagen completa del cuadro de video como un recorte preciso del documento para cada lado capturado.
* **Control mediante API:** Ofrece métodos para iniciar, detener, y reiniciar el proceso de captura de forma programática.
* **Responsivo:** Compatible con dispositivos móviles y de escritorio.

***

## Instalación

1.  **Pre-requisitos:**
    * Node.js y npm instalados.
    * Descargar desde: [https://nodejs.org](https://nodejs.org)
    * Verifica la instalación:
        ```bash
        node -v
        npm -v
        ```

2.  **Inicializa tu proyecto (si aplica):**
    ```bash
    npm init
    ```

3.  **Instala el paquete:**
    ```bash
    npm install @jaak.ai/stamps
    ```
    o con Yarn:
    ```bash
    yarn add @jaak.ai/stamps
    ```

4.  **Confirma la instalación:** Verifica que el paquete `@jaak.ai/stamps` esté listado en tu archivo `package.json`.

### Opción alternativa de instalación

También puedes instalar el componente directamente mediante una etiqueta `<script>` en la cabecera `head` HTML, ideal para implementaciones rápidas o en entornos que no utilizan npm.

```html
<script 
  type="module" 
  src="[https://unpkg.com/@jaak.ai/stamps@2.0.0-beta.1](https://unpkg.com/@jaak.ai/stamps@2.0.0-beta.1)"></script>
```

***

## Configuración del Componente

Las propiedades de configuración se pueden establecer como atributos directamente en la etiqueta HTML del componente.

| Propiedad | Descripción | Requerido | Tipo | Valor por defecto |
| :--- | :--- | :--- | :--- | :--- |
| `debug` | Activa el modo debug para mostrar información de depuración. | No | `boolean` | `false` |
| `alignmentTolerance`| Tolerancia en píxeles para considerar un lado del documento como alineado. | No | `number` | `10` |
| `maskSize` | Porcentaje del video que ocupará la máscara de detección (rango: 50-100). | No | `number` | `90` |
| `cropMargin` | Margen en píxeles que se agrega al recorte del documento (rango: 0-100). | No | `number` | `0` |
| `useDocumentClassification` | Habilita la clasificación automática para determinar si se debe solicitar el reverso. | No | `boolean` | `false` |

***

## Ejemplo de Implementación Básica

Este ejemplo, basado en el archivo `index.html` proporcionado, muestra cómo integrar y controlar el componente **Jaak Stamps** en una página web con HTML y JavaScript puros.

```html
<!DOCTYPE html>
<html>
	<head>
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<script
			type="module"
			src="[https://unpkg.com/@jaak.ai/stamps@2.0.0-beta.1](https://unpkg.com/@jaak.ai/stamps@2.0.0-beta.1)"></script>
	</head>
	<body>
		<div>
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
					<button class="card-btn" onclick="resetCapture()">Reiniciar</button>
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
							<h3>Imagen Completa</h3>
							<img id="front-full" src="" alt="Imagen completa del frente" />
						</div>
						<div class="image-card">
							<h3>Documento Recortado</h3>
							<img id="front-cropped" src="" alt="Documento recortado del frente" />
						</div>
					</div>
				</div>
				<div class="images-section back-section">
					<h2>Reverso del Documento</h2>
					<div class="image-row">
						<div class="image-card">
							<h3>Imagen Completa</h3>
							<img id="back-full" src="" alt="Imagen completa del reverso" />
						</div>
						<div class="image-card">
							<h3>Documento Recortado</h3>
							<img id="back-cropped" src="" alt="Documento recortado del reverso" />
						</div>
					</div>
				</div>
			</div>
		</div>

		<script>
			const detector = document.getElementById('detector');

			// 1. Escuchar eventos del componente
			detector.addEventListener('captureCompleted', (event) => {
				console.log('Captura completada:', event.detail);
			});
			detector.addEventListener('isReady', (event) => {
				console.log('Componente listo:', event.detail);
			});

			// 2. Funciones para los botones
			function startCapture() {
				detector.startCapture();
			}
			function stopCapture() {
				detector.stopCapture();
			}
			function resetCapture() {
				detector.resetCapture();
			}
			async function getImages() {
				try {
					const images = await detector.getCapturedImages();
					displayCapturedImages(images);
				} catch (error) {
					console.error('Error:', error.message);
				}
			}

			// 3. Función para mostrar imágenes en la UI
			function displayCapturedImages(images) {
				const container = document.getElementById('captured-images-container');
				container.style.display = 'flex';
				// Lógica para asignar src de imágenes...
				const frontFullImg = document.getElementById('front-full');
				if (images.front && images.front.fullFrame) {
					frontFullImg.src = images.front.fullFrame;
				}
				// ... (repetir para las otras imágenes) ...
			}
		</script>
	</body>
</html>
```

***

## Eventos Emitidos

| Evento | Payload | Descripción |
| :--- | :--- | :--- |
| `captureCompleted` | `CapturedImages` | Se emite cuando el proceso de captura (frente y reverso, si aplica) se ha completado exitosamente. |
| `isReady` | `boolean` | Se emite cuando el componente ha cargado sus modelos y está listo para iniciar la detección. |

***

## Métodos Públicos

| Método | Retorna | Descripción |
| :--- | :--- | :--- |
| `startCapture()` | `Promise<void>` | Inicia el proceso de detección y captura de imágenes. |
| `stopCapture()` | `Promise<void>` | Detiene el proceso de captura y libera el uso de la cámara. |
| `resetCapture()` | `Promise<void>` | Reinicia el estado del componente para una nueva captura. |
| `preloadModel()` | `Promise<{success: boolean, ...}>`| Precarga el modelo de detección para mejorar el rendimiento al iniciar la captura. |
| `getCapturedImages()`| `Promise<CapturedImages>` | Obtiene las imágenes capturadas después de que el proceso ha finalizado. |
| `isProcessCompleted()`| `Promise<boolean>` | Verifica si el proceso de captura ha sido completado. |
| `getStatus()` | `Promise<Status>` | Obtiene un objeto con el estado actual del detector. |
| `skipBackCapture()` | `Promise<void>` | Permite omitir la captura del reverso cuando el componente lo está solicitando. |

### Tipos de Datos de la API

A continuación se describen las interfaces para los datos que maneja el componente.

**`CapturedImages`**: Objeto devuelto por `getCapturedImages()` y en el evento `captureCompleted`.
```typescript
interface CapturedImages {
  front: {
    fullFrame: string | null;    // Imagen completa del frente en Base64
    cropped: string | null;      // Recorte del documento del frente en Base64
  };
  back: {
    fullFrame: string | null;    // Imagen completa del reverso en Base64
    cropped: string | null;      // Recorte del documento del reverso en Base64
  };
  timestamp: string;             // Timestamp de la captura en formato ISO
  metadata: {
    totalImages: number;         // Número total de imágenes capturadas
    processCompleted: boolean;   // Indica si el proceso fue completado
    backCaptureSkipped?: boolean; // Indica si se omitió la captura del reverso
  };
}
```

**`Status`**: Objeto devuelto por el método `getStatus()`.
```typescript
interface Status {
  isVideoActive: boolean;        // Indica si la cámara está activa
  captureStep: 'front' | 'back' | 'completed'; // Paso actual del proceso
  statusMessage: string;         // Mensaje de estado actual para el usuario
  hasImages: boolean;           // Indica si ya hay imágenes capturadas
  isProcessCompleted: boolean;  // Indica si el proceso está completado
  isModelPreloaded: boolean;    // Indica si el modelo fue precargado
}
```

***

## Recursos Complementarios

### 📦 Repositorio de ejemplo

Puedes apoyarte con un ejemplo funcional en el siguiente repositorio:
**[https://github.com/jaak-ai/document-detector-js-example](https://github.com/jaak-ai/document-detector-js-example)**