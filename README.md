# 🤖 Blog de Visión Robótica

---

## 👨‍💻 Autor

**Taref Bilel**
**Máster en Visión Artificial**
**Asignatura:** Visión Robótica

---

## 📌 Descripción

Este repositorio contiene las prácticas que he realizado en la asignatura de **Visión Robótica**.

En estas prácticas he trabajado con diferentes problemas relacionados con la visión por computador y la robótica. La idea principal fue aprender cómo un robot puede usar cámaras, sensores y algoritmos para entender mejor su entorno y tomar decisiones.

Durante estas prácticas he implementado varios sistemas, como seguimiento de líneas, reconstrucción 3D, localización visual y control visual usando Deep Learning.

Cada práctica incluye:

* Una explicación del problema.
* La idea principal del algoritmo.
* Los pasos que seguí para resolverlo.
* Los resultados obtenidos.
* Imágenes o videos de demostración cuando fue necesario.

El objetivo de este repositorio es mostrar de forma ordenada todo el trabajo que hice durante la asignatura.

---

# 📚 Prácticas

---

## 🔹 [Práctica 1: Seguimiento visual de una línea](P1_FollowLine.md)

En esta práctica hice que el robot siguiera una línea roja usando visión por computador.

Primero detecté la línea roja en la imagen usando el espacio de color HSV. Después calculé dónde estaba la línea respecto al centro de la imagen. Luego usé un controlador PD para corregir la dirección del robot.

La idea fue muy sencilla:

```text
mirar la imagen → detectar la línea → calcular el error → corregir el movimiento
```

En esta práctica aprendí cómo combinar procesamiento de imagen con control del robot.

---

## 🔹 [Práctica 2: Reconstrucción 3D](P2_3DReconstruction.md)

En esta práctica trabajé con reconstrucción 3D usando visión estéreo.

El robot tenía dos cámaras: una izquierda y una derecha. Yo usé esas dos imágenes para buscar puntos comunes entre ellas. Después, con geometría epipolar y triangulación, calculé puntos en 3D.

La idea fue parecida a cómo vemos los humanos con dos ojos.

Primero detecté bordes, luego busqué correspondencias entre las dos imágenes y finalmente generé una nube de puntos 3D.

En esta práctica aprendí cómo se puede obtener información de profundidad usando dos cámaras.

---

## 🔹 [Práctica 3: Localización visual](P3_VisualLocalization.md)

En esta práctica hice un sistema de localización visual usando AprilTags.

Los AprilTags son marcadores visuales que el robot puede detectar con la cámara. Cada marcador tiene una posición conocida en el mapa.

Cuando el robot detecta un AprilTag, puede calcular su propia posición en el entorno.

En el simulador:

* El robot verde representa la posición real.
* El robot azul representa la odometría.
* El robot rojo representa la posición estimada por mi sistema.

El objetivo fue hacer que el robot rojo siguiera al robot verde lo mejor posible.

En esta práctica aprendí cómo un robot puede saber dónde está usando referencias visuales del entorno.

---

## 🔹 [Práctica 4: Control visual End-to-End usando Deep Learning](P4_EndToEnd.md)

En esta práctica hice un sistema de conducción autónoma usando Deep Learning.

En vez de usar una solución clásica con detección de color y controlador PID, entrené una red neuronal para predecir directamente los comandos del robot.

El modelo recibía una imagen de la cámara frontal y devolvía dos valores:

* Velocidad lineal `v`.
* Velocidad angular `w`.

La idea fue:

```text
imagen de cámara → red neuronal → velocidad y giro
```

Después de entrenar el modelo, lo exporté a formato ONNX y lo integré en Unibotics para controlar el robot en tiempo real.

En esta práctica aprendí cómo se puede usar Deep Learning para controlar un robot directamente desde imágenes.

---

# 🛠️ Tecnologías utilizadas

En estas prácticas utilicé varias herramientas y librerías:

* Python
* OpenCV
* NumPy
* PyTorch
* ONNX
* ONNX Runtime
* AprilTags
* HAL API
* WebGUI
* RoboticsAcademy
* Unibotics

Cada herramienta fue útil para una parte diferente del trabajo.

Python fue el lenguaje principal.
OpenCV me ayudó a procesar imágenes.
NumPy me sirvió para cálculos numéricos.
PyTorch lo usé para entrenar modelos de Deep Learning.
ONNX me permitió usar el modelo entrenado dentro del simulador.
HAL API y WebGUI me permitieron comunicarme con el robot y visualizar los resultados.

---

# 📊 Resumen del trabajo realizado

En este repositorio he trabajado con cuatro prácticas principales.

En la primera práctica, hice que el robot siguiera una línea roja usando visión clásica y un controlador PD.

En la segunda práctica, reconstruí una escena en 3D usando dos cámaras y triangulación.

En la tercera práctica, estimé la posición del robot usando AprilTags y odometría.

En la cuarta práctica, entrené una red neuronal para conducir el robot usando imágenes de la cámara.

Cada práctica me ayudó a entender una parte diferente de la visión robótica.

---

# ✅ Conclusión

Este repositorio resume mi trabajo práctico en la asignatura de Visión Robótica.

Durante estas prácticas he aprendido cómo un robot puede usar información visual para moverse, localizarse, reconstruir el entorno y tomar decisiones.

También he visto la diferencia entre métodos clásicos de visión por computador y métodos basados en Deep Learning.

En general, estas prácticas me ayudaron a entender mejor cómo se conectan la percepción visual, la geometría, el control y el aprendizaje automático dentro de la robótica.

---

# 👨‍💻 Autor

**Taref Bilel**
