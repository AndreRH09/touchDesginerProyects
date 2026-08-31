    # Interactive Visualizer with TouchDesigner & MediaPipe

Este proyecto es una instalación/visualización interactiva construida en **TouchDesigner**. Utiliza seguimiento en tiempo real mediante **MediaPipe** para generar interactividad y manipular visuales generativos de forma fluida.

## Características
*   **Tracking en Tiempo Real:** Integración del componente `mediapipe.tox` para el seguimiento de cuerpo (Pose), manos (Hands) o rostro (Face Mesh) a través de la cámara web.
*   **Procesamiento de Datos (CHOPs):** Uso de Channel Operators (CHOPs) para extraer, filtrar, escalar (Math CHOP) y suavizar (Filter/Lag CHOP) los datos brutos obtenidos de MediaPipe, traduciendo el movimiento humano en señales de control estables.
*   **Visualización Generativa (TOPs):** Manipulación de texturas, video y renderizado en tiempo real mediante Texture Operators (TOPs). Los datos de interacción obtenidos de los CHOPs modifican parámetros visuales (ej. desplazamiento, bucles de feedback, composición de capas, reactividad).

## Requisitos Previos
*   [TouchDesigner](https://derivative.ca/download) (Se recomienda la compilación más reciente con soporte para Python 3.9+).
*   Cámara web (integrada o externa).
*   El componente `mediapipe.tox` (asegúrate de que esté ubicado en la ruta correcta dentro de la estructura de carpetas del proyecto).

## Instalación y Configuración
1.  Descarga o clona el directorio del proyecto.
2.  Abre el archivo principal (`.toe`) en TouchDesigner.
3.  Localiza el nodo **Video Device In** (TOP) y asegúrate de que tu cámara web activa esté seleccionada en los parámetros.
4.  Revisa el componente `mediapipe.tox`. Dependiendo del entorno, es posible que el componente requiera unos segundos de inicialización para cargar los modelos de Machine Learning.
5.  ¡Párate frente a la cámara e interactúa con los visuales!

## Estructura del Proyecto
El flujo de trabajo (Node Network) está diseñado de la siguiente manera:

1.  **Tracking y Captura (`mediapipe.tox`):** Analiza la señal de entrada del TOP de video y genera una lista de canales con las coordenadas (X, Y, Z) de los *landmarks* detectados.
2.  **Red de Control (CHOP Network):** 
    *   Nodos `Select` para aislar los puntos de interacción clave (ej. dedo índice o muñeca).
    *   Nodos `Math` para remapear los rangos normalizados de MediaPipe (0-1) a la resolución del lienzo o a los parámetros de los efectos.
3.  **Red Visual (TOP Network):**
    *   Sistemas de partículas, formas geométricas (SOP a TOP) o bucles de `Feedback` TOP.
    *   Canales exportados (CHOP References) que manejan dinámicamente el `Transform`, `Displace` o la mezcla (`Composite`) en base al movimiento.

## Autor
**André Renzo Añazco Huamanquispe**

---
*Proyecto creado para la exploración de interfaces naturales y arte generativo en tiempo real.*