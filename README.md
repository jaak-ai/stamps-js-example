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
**[https://github.com/jaak-ai/stamps-js-example](https://github.com/jaak-ai/stamps-js-example)**

Un componente web para la detección y captura automática de documentos de identificación usando visión artificial y redes neuronales.

## Características

- 📷 **Detección automática** de documentos de identificación en tiempo real
- 🎯 **Guía visual** para posicionamiento óptimo del documento
- 🤖 **Clasificación inteligente** de documentos para determinar si requieren reverso
- 📋 **Captura adaptativa** - automáticamente detecta si el documento tiene reverso
- 🖼 **Doble salida** para cada lado: imagen completa y recorte del documento
- ✂️ **Recorte configurable** con margen personalizable alrededor del documento
- 🔧 **API programática** para control total del proceso
- 📱 **Responsive** y compatible con dispositivos móviles
- ⚡ **Optimización automática** para mejor rendimiento

## Instalación

### NPM
```bash
npm install @jaak.ai/stamps
```

### Yarn
```bash
yarn add @jaak.ai/stamps
```

### CDN
```html
<script type="module" src="https://unpkg.com/@jaak.ai/stamps/dist/jaak-stamps-webcomponent/jaak-stamps-webcomponent.esm.js"></script>
```

## Uso Básico

### HTML Vanilla

```html
<!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script type="module" src="https://unpkg.com/@jaak.ai/stamps/dist/jaak-stamps-webcomponent/jaak-stamps-webcomponent.esm.js"></script>
</head>
<body>
    <jaak-stamps id="detector" debug="false" mask-size="90" crop-margin="10" use-document-classification="false"></jaak-stamps>
    
    <button onclick="startCapture()">Iniciar Captura</button>
    <button onclick="stopCapture()">Detener</button>
    <button onclick="resetCapture()">Reiniciar</button>

    <script>
        const detector = document.getElementById('detector');
        
        // Escuchar evento de captura completada
        detector.addEventListener('captureCompleted', (event) => {
            console.log('Captura completada:', event.detail);
            // event.detail contiene las imágenes capturadas
        });
        
        // Escuchar evento de componente listo
        detector.addEventListener('isReady', (event) => {
            console.log('Componente listo:', event.detail);
            // event.detail indica si el componente está listo (true/false)
        });
        
        function startCapture() {
            detector.startCapture();
        }
        
        function stopCapture() {
            detector.stopCapture();
        }
        
        function resetCapture() {
            detector.resetCapture();
        }
        
        // Precargar el modelo (opcional - mejora el rendimiento)
        async function preloadModel() {
            try {
                const result = await detector.preloadModel();
                console.log('Modelo precargado:', result);
            } catch (error) {
                console.error('Error al precargar:', error);
            }
        }
        
        // Obtener imágenes después de la captura
        async function getImages() {
            try {
                const images = await detector.getCapturedImages();
                console.log('Imágenes capturadas:', images);
            } catch (error) {
                console.error('Error:', error.message);
            }
        }
    </script>
</body>
</html>
```

### Angular

#### 1. Instalar el componente
```bash
npm install @jaak.ai/stamps
```

#### 2. Configurar el módulo (app.module.ts)
```typescript
import { NgModule, CUSTOM_ELEMENTS_SCHEMA } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';

// Importar el componente web
import '@jaak.ai/stamps/dist/jaak-stamps-webcomponent/jaak-stamps-webcomponent.esm.js';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent],
  schemas: [CUSTOM_ELEMENTS_SCHEMA] // Permitir elementos personalizados
})
export class AppModule { }
```

#### 3. Usar en el componente
```typescript
// app.component.ts
import { Component, ElementRef, ViewChild, OnInit } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <div class="container">
      <h1>Detector de Documentos</h1>
      
      <jaak-stamps 
        #detector
        [debug]="false"
        [maskSize]="90"
        [cropMargin]="10"
        [useDocumentClassification]="false"
        (captureCompleted)="onCaptureCompleted($event)"
        (isReady)="onComponentReady($event)">
      </jaak-stamps>
      
      <div class="controls">
        <button (click)="preloadModel()">Precargar Modelo</button>
        <button (click)="startCapture()">Iniciar Captura</button>
        <button (click)="stopCapture()">Detener</button>
        <button (click)="resetCapture()">Reiniciar</button>
        <button (click)="getImages()" [disabled]="!isCompleted">Obtener Imágenes</button>
      </div>
      
      <div *ngIf="capturedData" class="results">
        <h3>Imágenes Capturadas:</h3>
        <p>Total de imágenes: {{ capturedData.metadata.totalImages }}</p>
        <img *ngIf="capturedData.front.cropped" [src]="capturedData.front.cropped" alt="Frente">
        <img *ngIf="capturedData.back.cropped" [src]="capturedData.back.cropped" alt="Reverso">
      </div>
    </div>
  `,
  styles: [`
    .container { max-width: 800px; margin: 0 auto; padding: 20px; }
    .controls { margin: 20px 0; }
    .controls button { margin: 0 10px; padding: 10px 20px; }
    .results img { max-width: 300px; margin: 10px; border: 1px solid #ccc; }
  `]
})
export class AppComponent implements OnInit {
  @ViewChild('detector', { static: true }) detectorRef!: ElementRef;
  
  capturedData: any = null;
  isCompleted = false;
  
  ngOnInit() {
    // El elemento ya está disponible después de la vista
  }
  
  async preloadModel() {
    try {
      const result = await this.detectorRef.nativeElement.preloadModel();
      console.log('Modelo precargado:', result);
    } catch (error) {
      console.error('Error al precargar modelo:', error);
    }
  }

  async startCapture() {
    try {
      await this.detectorRef.nativeElement.startCapture();
    } catch (error) {
      console.error('Error al iniciar captura:', error);
    }
  }
  
  async stopCapture() {
    await this.detectorRef.nativeElement.stopCapture();
  }
  
  async resetCapture() {
    await this.detectorRef.nativeElement.resetCapture();
    this.capturedData = null;
    this.isCompleted = false;
  }
  
  async getImages() {
    try {
      const images = await this.detectorRef.nativeElement.getCapturedImages();
      console.log('Imágenes obtenidas:', images);
    } catch (error) {
      console.error('Error al obtener imágenes:', error);
    }
  }
  
  onCaptureCompleted(event: any) {
    console.log('Captura completada en Angular:', event.detail);
    this.capturedData = event.detail;
    this.isCompleted = true;
  }
  
  onComponentReady(event: any) {
    console.log('Componente listo en Angular:', event.detail);
    // El componente está listo para iniciar detección
  }
}
```

### React

#### 1. Instalar el componente
```bash
npm install @jaak.ai/stamps
```

#### 2. Crear componente wrapper (opcional pero recomendado)
```jsx
// JaakStampsWrapper.jsx
import React, { useRef, useEffect, useImperativeHandle, forwardRef } from 'react';

// Importar el web component
import '@jaak.ai/stamps/dist/jaak-stamps-webcomponent/jaak-stamps-webcomponent.esm.js';

const JaakStampsWrapper = forwardRef(({ debug = false, maskSize = 90, cropMargin = 0, useDocumentClassification = false, onCaptureCompleted, onIsReady }, ref) => {
  const elementRef = useRef(null);
  
  useImperativeHandle(ref, () => ({
    preloadModel: () => elementRef.current?.preloadModel(),
    startCapture: () => elementRef.current?.startCapture(),
    stopCapture: () => elementRef.current?.stopCapture(),
    resetCapture: () => elementRef.current?.resetCapture(),
    getCapturedImages: () => elementRef.current?.getCapturedImages(),
    getStatus: () => elementRef.current?.getStatus(),
    skipBackCapture: () => elementRef.current?.skipBackCapture(),
    isProcessCompleted: () => elementRef.current?.isProcessCompleted()
  }));
  
  useEffect(() => {
    const element = elementRef.current;
    
    const handleCaptureCompleted = (event) => {
      if (onCaptureCompleted) {
        onCaptureCompleted(event.detail);
      }
    };
    
    const handleIsReady = (event) => {
      if (onIsReady) {
        onIsReady(event.detail);
      }
    };
    
    if (element) {
      element.addEventListener('captureCompleted', handleCaptureCompleted);
      element.addEventListener('isReady', handleIsReady);
      
      return () => {
        element.removeEventListener('captureCompleted', handleCaptureCompleted);
        element.removeEventListener('isReady', handleIsReady);
      };
    }
  }, [onCaptureCompleted, onIsReady]);
  
  return (
    <jaak-stamps 
      ref={elementRef} 
      debug={debug}
      mask-size={maskSize}
      crop-margin={cropMargin}
      use-document-classification={useDocumentClassification}
    />
  );
});

export default JaakStampsWrapper;
```

#### 3. Usar en tu aplicación React
```jsx
// App.jsx
import React, { useRef, useState } from 'react';
import JaakStampsWrapper from './components/JaakStampsWrapper';

function App() {
  const detectorRef = useRef(null);
  const [capturedData, setCapturedData] = useState(null);
  const [isCompleted, setIsCompleted] = useState(false);
  const [status, setStatus] = useState(null);
  
  const handleCaptureCompleted = (data) => {
    console.log('Captura completada:', data);
    setCapturedData(data);
    setIsCompleted(true);
  };
  
  const handleIsReady = (isReady) => {
    console.log('Componente listo:', isReady);
    // El componente está listo para iniciar detección
  };
  
  const preloadModel = async () => {
    try {
      const result = await detectorRef.current?.preloadModel();
      console.log('Modelo precargado:', result);
    } catch (error) {
      console.error('Error al precargar modelo:', error);
    }
  };
  
  const startCapture = async () => {
    try {
      await detectorRef.current?.startCapture();
    } catch (error) {
      console.error('Error al iniciar captura:', error);
    }
  };
  
  const stopCapture = async () => {
    await detectorRef.current?.stopCapture();
  };
  
  const resetCapture = async () => {
    await detectorRef.current?.resetCapture();
    setCapturedData(null);
    setIsCompleted(false);
  };
  
  const getImages = async () => {
    try {
      const images = await detectorRef.current?.getCapturedImages();
      console.log('Imágenes obtenidas:', images);
    } catch (error) {
      console.error('Error al obtener imágenes:', error);
    }
  };
  
  const checkStatus = async () => {
    const currentStatus = await detectorRef.current?.getStatus();
    setStatus(currentStatus);
  };
  
  return (
    <div className="App">
      <header className="App-header">
        <h1>Detector de Documentos - React</h1>
        
        <JaakStampsWrapper 
          ref={detectorRef}
          debug={false}
          maskSize={90}
          cropMargin={10}
          useDocumentClassification={false}
          onCaptureCompleted={handleCaptureCompleted}
          onIsReady={handleIsReady}
        />
        
        <div className="controls">
          <button onClick={preloadModel}>Precargar Modelo</button>
          <button onClick={startCapture}>Iniciar Captura</button>
          <button onClick={stopCapture}>Detener</button>
          <button onClick={resetCapture}>Reiniciar</button>
          <button onClick={getImages} disabled={!isCompleted}>
            Obtener Imágenes
          </button>
          <button onClick={checkStatus}>Verificar Estado</button>
        </div>
        
        {status && (
          <div className="status">
            <h3>Estado Actual:</h3>
            <p>Video activo: {status.isVideoActive ? 'Sí' : 'No'}</p>
            <p>Paso: {status.captureStep}</p>
            <p>Mensaje: {status.statusMessage}</p>
            <p>Proceso completado: {status.isProcessCompleted ? 'Sí' : 'No'}</p>
            <p>Modelo precargado: {status.isModelPreloaded ? 'Sí' : 'No'}</p>
          </div>
        )}
        
        {capturedData && (
          <div className="results">
            <h3>Resultados de la Captura:</h3>
            <p>Total de imágenes: {capturedData.metadata.totalImages}</p>
            <p>Timestamp: {capturedData.timestamp}</p>
            
            {capturedData.front.cropped && (
              <div>
                <h4>Frente del Documento:</h4>
                <img 
                  src={capturedData.front.cropped} 
                  alt="Frente del documento" 
                  style={{ maxWidth: '300px', border: '1px solid #ccc', margin: '10px' }}
                />
              </div>
            )}
            
            {capturedData.back.cropped && (
              <div>
                <h4>Reverso del Documento:</h4>
                <img 
                  src={capturedData.back.cropped} 
                  alt="Reverso del documento" 
                  style={{ maxWidth: '300px', border: '1px solid #ccc', margin: '10px' }}
                />
              </div>
            )}
          </div>
        )}
      </header>
    </div>
  );
}

export default App;
```

## API Reference

### Props/Atributos

| Propiedad | Tipo | Predeterminado | Descripción |
|-----------|------|---------------|-------------|
| `debug` | `boolean` | `false` | Activa el modo debug para mostrar información de depuración |
| `alignmentTolerance` | `number` | `10` | Tolerancia en píxeles para considerar un lado alineado |
| `maskSize` | `number` | `90` | Porcentaje del video que ocupará la máscara de detección (50-100) |
| `cropMargin` | `number` | `0` | Margen en píxeles agregado al recorte del documento (0-100) |
| `useDocumentClassification` | `boolean` | `false` | Habilita la clasificación automática de documentos para determinar si solicitar reverso. Solo carga el modelo de clasificación cuando está habilitada. |

### Métodos Públicos

| Método | Retorna | Descripción |
|--------|---------|-------------|
| `startCapture()` | `Promise<void>` | Inicia el proceso de detección y captura |
| `stopCapture()` | `Promise<void>` | Detiene el proceso y libera la cámara |
| `resetCapture()` | `Promise<void>` | Reinicia el proceso de captura |
| `preloadModel()` | `Promise<{success: boolean, message?: string, error?: string}>` | Precarga el modelo de detección |
| `getCapturedImages()` | `Promise<CapturedImages>` | Obtiene las imágenes capturadas (solo después de completar) |
| `isProcessCompleted()` | `Promise<boolean>` | Verifica si el proceso ha sido completado |
| `getStatus()` | `Promise<Status>` | Obtiene el estado actual del detector |
| `skipBackCapture()` | `Promise<void>` | Omite la captura del reverso (solo disponible en paso 'back') |

### Eventos

| Evento | Payload | Descripción |
|--------|---------|-------------|
| `captureCompleted` | `CapturedImages` | Se emite cuando se completa el proceso de captura |
| `isReady` | `boolean` | Se emite cuando el componente está listo para comenzar la detección (modelos cargados) |

### Tipos de Datos

```typescript
interface CapturedImages {
  front: {
    fullFrame: string | null;    // Base64 de la imagen completa del frente
    cropped: string | null;      // Base64 del documento recortado del frente
  };
  back: {
    fullFrame: string | null;    // Base64 de la imagen completa del reverso
    cropped: string | null;      // Base64 del documento recortado del reverso
  };
  timestamp: string;             // ISO timestamp de la captura
  metadata: {
    totalImages: number;         // Número total de imágenes capturadas
    processCompleted: boolean;   // Si el proceso fue completado
    backCaptureSkipped?: boolean; // Si se omitió la captura del reverso
  };
}

interface Status {
  isVideoActive: boolean;        // Si la cámara está activa
  captureStep: 'front' | 'back' | 'completed'; // Paso actual del proceso
  statusMessage: string;         // Mensaje de estado actual
  hasImages: boolean;           // Si hay imágenes capturadas
  isProcessCompleted: boolean;  // Si el proceso está completado
  isModelPreloaded: boolean;    // Si el modelo está precargado
}
```

## Flujo de Trabajo

### Flujo Estándar (useDocumentClassification = false)
1. **Inicialización**: El componente se prepara para la detección
2. **Inicio de captura**: Se carga automáticamente el modelo de detección
3. **Captura del frente**: El usuario posiciona el documento y se captura automáticamente
4. **Solicitud de reverso**: Siempre se solicita voltear el documento para capturar el reverso
5. **Botón "Saltar reverso"**: Disponible para omitir manualmente la captura del reverso
6. **Completado**: Se emite el evento `captureCompleted` con las imágenes correspondientes

### Flujo con Clasificación Inteligente (useDocumentClassification = true)
1. **Inicialización**: El componente se prepara para la detección
2. **Inicio de captura**: Se cargan automáticamente los modelos de detección y clasificación
3. **Captura del frente**: El usuario posiciona el documento y se captura automáticamente
4. **Clasificación automática**: El sistema determina si el documento requiere captura de reverso
5. **Flujo adaptativo**: 
   - **Si es pasaporte**: Se completa automáticamente el proceso (sin reverso)
   - **Si es otro documento**: Se solicita voltear el documento para capturar el reverso
6. **Completado**: Se emite el evento `captureCompleted` con las imágenes correspondientes

### Flujo con Precarga (Recomendado)
1. **Precarga de modelos**: Se llama a `preloadModel()` para cargar ambos modelos en memoria
2. **Inicio optimizado**: Al iniciar la captura, se usan los modelos ya cargados
3. **Captura del frente**: Detección y clasificación más rápidas con modelos precargados
4. **Flujo inteligente**: 
   - **Documento sin reverso**: Proceso completado inmediatamente
   - **Documento con reverso**: Se solicita captura del reverso
5. **Completado**: Se emite el evento `captureCompleted` con todas las imágenes

### Clasificación de Documentos

#### Modo con Clasificación Habilitada (`useDocumentClassification = true`)
Cuando la propiedad está habilitada, el sistema utiliza un modelo de inteligencia artificial especializado para determinar automáticamente si el documento requiere captura de reverso:
- **Pasaportes**: Se omite automáticamente la captura del reverso
- **Otros documentos**: Requieren captura de ambos lados (frente y reverso)
- **Botón "Saltar reverso"**: Siempre disponible para permitir flexibilidad manual
- **Carga optimizada**: El modelo de clasificación se descarga solo cuando es necesario

#### Modo Estándar (`useDocumentClassification = false`, por defecto)
Cuando está deshabilitada, el comportamiento es más liviano y directo:
- **Todos los documentos**: Siempre se solicita captura del reverso
- **Botón "Saltar reverso"**: Disponible para omitir manualmente la captura
- **Sin clasificación**: NO se carga el modelo de clasificación, solo el de detección
- **Menor uso de ancho de banda**: Solo descarga el modelo de detección (~5MB menos)

#### Consideraciones de Rendimiento
- **Con clasificación deshabilitada**: Carga más rápida y menor uso de datos
- **Con clasificación habilitada**: Experiencia más inteligente pero requiere descargar modelo adicional
- **Carga bajo demanda**: El modelo de clasificación se carga solo cuando se captura el primer documento

## Requisitos del Sistema

- **Navegadores**: Chrome/Edge 88+, Firefox 85+, Safari 14+
- **Cámara**: Acceso a cámara trasera (móvil) o webcam (escritorio)
- **Conexión**: Conexión a internet para cargar los modelos de IA

## Consideraciones de Privacidad y Rendimiento

### Privacidad
- Las imágenes se procesan localmente en el navegador
- No se envían datos a servidores externos
- Los modelos de IA (detección y clasificación) se ejecutan completamente en el cliente
- La clasificación de documentos se realiza de forma privada y local
- Las imágenes capturadas solo están disponibles en el contexto de tu aplicación

### Optimización de Recursos
- **Carga condicional**: Solo descarga modelos necesarios según configuración
- **Modelo de detección**: ~15MB (siempre requerido)
- **Modelo de clasificación**: ~5MB (solo si `useDocumentClassification = true`)
- **Lazy loading**: Los modelos se cargan cuando realmente se necesitan
- **Cache del navegador**: Los modelos se almacenan localmente después de la primera descarga

## Desarrollo

### Clonar el repositorio
```bash
git clone [repository-url]
cd jaak-stamps-webcomponent
```

### Instalar dependencias
```bash
npm install
```

### Desarrollo local
```bash
npm start
```

### Construir para producción
```bash
npm run build
```

### Ejecutar tests
```bash
npm test
```

## Soporte

Para reportar bugs o solicitar características, por favor crea un issue en el repositorio del proyecto.

## Licencia

MIT License - ver archivo LICENSE para detalles.