# Interfaz Interactiva de Visualización de Datos Anatómicos en 3D

Este proyecto es una instalación interactiva y sistema de renderizado en tiempo real desarrollado en TouchDesigner. Utiliza visión por computadora para permitir la manipulación espacial de nubes de puntos (point clouds) anatómicas de un cerebro y un cráneo mediante el seguimiento gestual de manos y rostros.

## Características Principales

*   **Seguimiento Gestual en Tiempo Real:** Integración con modelos de aprendizaje automático para el rastreo tridimensional de extremidades superiores y geometría facial.
*   **Procesamiento de Nubes de Puntos:** Renderizado masivo de vértices cargados de manera externa mediante archivos de datos espables.
*   **Filtrado de Señal y Dinámica:** Interpolación de movimiento y mitigación de ruido cinemático mediante operadores de retraso y remapeo de rangos.
*   **Estética Basada en Partículas:** Renderizado optimizado mediante materiales de sprites y estructuras alámbricas en entornos tridimensionales controlados.

---

## Interacción y Control Gestual

El sistema implementa un esquema de control a dos manos coordinado a través de los datos cinemáticos provistos por MediaPipe:

### Control de Escala y Espacio (Mano Derecha)
*   **Mapeo Háptico Virtual:** El sistema calcula continuamente la distancia euclidiana entre los landmarks tridimensionales del **dedo pulgar** e **índice** de la mano derecha.
*   **Línea de Medición:** Se genera un renderizado procedural de una línea guía que une ambos dedos a modo de sensor háptico visual.
*   **Caja de Contención (Cage):** El valor de esta distancia es remapeado y escalado dinámicamente dentro de un contenedor en forma de cubo alámbrico (`wireframe1`), el cual define los límites espaciales del renderizado actual.

### Control de Estados del Modelo (Mano Izquierda)
*   **Cambio de Modelo:** El gesto de **palma abierta (Open Palm)** detectado en la mano izquierda actúa como un conmutador lógico.
*   **Flujo de Señal:** Al activarse, envía un trigger al módulo de control que conmuta los operadores `switch1` y `switch2`, alternando la visualización en pantalla entre el archivo de datos del cerebro y el del cráneo.

---

## Arquitectura del Sistema (Estructura de Nodos)

El entorno del proyecto está dividido en cuatro capas funcionales diferenciadas dentro de la red de TouchDesigner:

### 1. Percepción y Entrada de Datos (Perception Layer)
*   **MediaPipe / hand_tracking2:** Contenedores modulares que ejecutan el motor de visión basado en Chromium. Reciben el feed de video y decodifican las cadenas JSON con los landmarks de posición.
*   **keyboardin1:** Operador de captura de eventos de hardware para el control de estados del sistema, triggers internos y reinicio de variables dinámicas.

### 2. Capa de Datos Geométricos (Data Asset Layer)
*   **skull_map / pointfilein1:** Operadores encargados del streaming y decodificación de los archivos de nubes de puntos hacia la GPU.
*   **pointfileselect[1-2]:** Selectores matriciales que aíslan y segmentan las matrices de coordenadas necesarias para los operadores de transformación de puntos.

### 3. Núcleo de Control y Lógica (CHOP & DAT Control Core)
*   **Filtrado Cinemático:** Implementación de nodos `lag`, `limit` y `math` para suavizar las coordenadas procedentes de MediaPipe, eliminando el jitter del tracking físico.
*   **Máquina de Estados y Ruteo:** El flujo lógico es coordinado mediante operadores `logic`, `count` y `switch`, permitiendo alternar entre diferentes modos de renderizado o activar animaciones basadas en umbrales de posición.
*   **Mapeo de Constantes:** Tablas DAT (`constant_export`) encargadas de exportar parámetros operacionales fijos hacia los elementos de la escena 3D.

### 4. Motor de Gráficos y Postprocesamiento (Render & Compositing Pipeline)
*   **Instanciación 3D:** Los nodos geométricos (`geo1`, `geo2`, `geo3`) manejan la instanciación de formas primitivas (`sphere1`, `box2`) usando los datos de posición remapeados.
*   **Escena Lumínica y Óptica:** Configuración del espacio tridimensional mediante la interacción de `cam1`, `light1` y el motor de renderizado por hardware `render1`.
*   **Ajustes de Salida (TOPs):** Normalización de la relación de aspecto mediante operadores `fit` (`color_bra`, `pos_brain`, `pos_skull`) y composición de capas finales con operadores `over`.

---

## Componentes de la Interfaz de Usuario (UI)

El proyecto cuenta con una jerarquía de interfaces y elementos de control visual basados en operadores de tipo `panelCOMP`:

*   **interface:** Nodo base maestro que unifica los parámetros globales expuestos para la calibración del sistema.
*   **container1:** Contenedor de salida asignado a la ventana de visualización a pantalla completa (Perform Mode).
*   **annotate[1-8]:** Sub-páneles internos integrados en el módulo de MediaPipe. Despliegan visualizadores de depuración (debugging), navegadores web integrados (`webBrowser1`) y la proyección directa de las mallas de seguimiento sobre la imagen original.

---

## Registro Visual y Documentación Gráfica

### Render de Estructura Cerebral
![Visualización del Cerebro](ParticulesTPv1/Image/brain.png)
*Muestra del renderizado y comportamiento de las partículas correspondiente al archivo PLY de la nube de puntos del cerebro.*

### Render de Estructura Craneal
![Visualización del Cráneo](ParticulesTPv1/Image/skull.png)
*Renderizado de los datos anatómicos procedentes del CT scan de cabeza completa (Visible Human Female).*

### Interfaz de Control Háptico y Escala
![Control Háptico de Escala](ParticulesTPv1/Image/control.png)
*Renderizado de la línea analógica de distancia entre los dedos pulgar e índice de la mano derecha controlando las dimensiones de la geometría encapsulada en el cubo estructural.*

---

## Dependencias y Recursos Externos

Debido a las restricciones de tamaño de almacenamiento en repositorios de Git estándar (límite de 100 MB), los binarios pesados, modelos y recursos de nubes de puntos deben descargarse manualmente e incorporarse en las rutas especificadas antes de ejecutar el archivo principal `.toe`.

### 1. Plugins y Componentes del Entorno
*   **MediaPipe TouchDesigner Plugin:** Es indispensable clonar o descargar los módulos base del plugin desarrollado por Torin Blankensmith.
    *   *Repositorio Oficial:* [mediapipe-touchdesigner](https://github.com)
    *   *Ubicación recomendada:* Asegurarse de situar el componente `MediaPipe.tox` en la carpeta `ParticulesTPv1/toxes/`.

### 2. Modelos 3D y Nubes de Puntos (Archivos .PLY)
Los datos biomédicos originales utilizados para generar los efectos de partículas se deben descargar desde los repositorios de Sketchfab de Terrie Simmons-Ehrhardt:
*   **Brain Point Cloud (Modelo del Cerebro):** [Descargar desde Sketchfab](https://sketchfab.com)
    *   *Ubicación requerida:* Guardar en `ParticulesTPv1/Maps/vhf_fullhead_pc.ply`.
*   **Full CT Head Point Cloud (Modelo del Cráneo/Cabeza Completa):** [Descargar desde Sketchfab](https://sketchfab.com)
    *   *Ubicación requerida:* Guardar en `ParticulesTPv1/Maps/APARAT_1.PLY`.

---

## Instalación y Despliegue

1.  Clonar este repositorio de manera local.
2.  Descargar las dependencias listadas en la sección de **Dependencias y Recursos Externos** y copiarlas en sus carpetas correspondientes (`/toxes` y `/Maps`).
3.  Ejecutar el archivo `.toe` del proyecto en TouchDesigner.
4.  Configurar el nodo **Video Device In** seleccionando la cámara web activa del sistema.
