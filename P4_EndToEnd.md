
# 🚗 Práctica: Control visual End-to-End usando Deep Learning

---

## 👨‍💻 Autor

**Taref Bilel**
**Máster en Visión Artificial**
**Asignatura:** Visión Robótica

---

## 📘 Introducción

En esta práctica he trabajado con un sistema de conducción autónoma usando Deep Learning dentro de la plataforma RoboticsAcademy / Unibotics.

La idea principal de la práctica fue hacer que el robot pudiera conducir usando solamente la imagen de su cámara frontal.

En otras prácticas, normalmente el robot sigue una línea usando visión clásica. Por ejemplo, se detecta un color, se calcula el centro de la línea y después se usa un controlador para girar. Pero en esta práctica hice algo diferente.

En vez de decirle al robot paso por paso qué tiene que buscar, yo entrené una red neuronal para que aprendiera a conducir a partir de ejemplos.

Es decir, yo le enseñé al modelo muchas imágenes del circuito. Para cada imagen, el modelo también tenía la respuesta correcta, que eran dos valores:

* La velocidad lineal `v`, que indica cuánto avanza el robot.
* La velocidad angular `w`, que indica cuánto gira el robot.

Entonces, el objetivo fue que la red aprendiera esta idea:

**cuando ve una imagen parecida, debe saber qué velocidad y qué giro usar.**

De forma sencilla, el robot hace esto todo el tiempo:

1. Mira con la cámara.
2. La imagen entra en el modelo.
3. El modelo predice la velocidad y el giro.
4. El robot se mueve con esos valores.
5. El proceso se repite muchas veces por segundo.

Esto se llama control visual End-to-End porque el sistema va directamente desde la imagen hasta el control del robot.

---

## 🎯 Objetivo

El objetivo principal de esta práctica fue crear un sistema de conducción autónoma usando Deep Learning.

Yo quería conseguir que el robot pudiera seguir un circuito usando solamente la cámara frontal, sin usar durante la inferencia un sistema clásico basado en detectar colores, calcular centroides o aplicar un controlador PID.

Para conseguirlo, yo hice varias partes:

* Primero, cargué un dataset con imágenes del circuito.
* Después, leí las etiquetas del dataset, donde aparecen los valores reales de velocidad `v` y giro `w`.
* Luego, preparé las imágenes para que fueran más fáciles de usar por la red neuronal.
* Después, creé el sistema de datos en PyTorch.
* Luego, entrené una red neuronal para que aprendiera a predecir `v` y `w`.
* Después, comprobé si las predicciones eran razonables.
* Luego, guardé el modelo entrenado.
* Después, convertí el modelo a formato ONNX.
* Finalmente, usé ese modelo dentro de Unibotics para controlar el robot en tiempo real.

Mi objetivo no era solamente entrenar una red, sino conseguir que esa red funcionara realmente dentro del simulador.

---

## 🧠 Idea principal de la práctica

La idea principal es que el robot aprende mirando ejemplos.

Para entenderlo de forma sencilla, es como cuando una persona aprende a conducir viendo muchas situaciones diferentes.

Por ejemplo:

* Si la carretera está recta, la persona aprende que debe seguir recto.
* Si hay una curva a la derecha, aprende que debe girar a la derecha.
* Si hay una curva fuerte, aprende que debe girar más.
* Si la carretera está clara, puede avanzar con más velocidad.
* Si la situación es complicada, debe tener más cuidado.

En esta práctica hice algo parecido, pero con una red neuronal.

Yo le di al modelo muchas imágenes. Cada imagen tenía asociada una acción correcta. Esa acción era la velocidad lineal y la velocidad angular.

Entonces, durante el entrenamiento, el modelo intentaba aprender la relación entre lo que se ve en la imagen y lo que debe hacer el robot.

La diferencia con una solución clásica es importante.

En una solución clásica, yo tendría que escribir reglas manualmente. Por ejemplo:

* Si la línea está a la izquierda, gira a la izquierda.
* Si la línea está a la derecha, gira a la derecha.
* Si la línea está en el centro, sigue recto.

Pero en esta práctica, yo no escribí esas reglas directamente. En vez de eso, entrené una red para que aprendiera esas decisiones usando datos.

---

# 📂 Dataset utilizado

Para entrenar el modelo, usé un dataset con imágenes del circuito y comandos del robot.

Cada ejemplo del dataset tiene dos partes:

1. Una imagen de la cámara frontal.
2. Los valores correctos de control para esa imagen.

Los valores de control son:

* `v`: velocidad lineal.
* `w`: velocidad angular.

La velocidad lineal indica si el robot avanza más rápido o más lento.

La velocidad angular indica si el robot gira, y hacia dónde gira.

Por ejemplo, si el valor de `w` es pequeño, el robot casi sigue recto.
Si el valor de `w` es grande, el robot gira más fuerte.

---

## Dataset del circuito simple

En la práctica había un dataset del circuito simple.

Este dataset estaba dividido en varias carpetas con imágenes de entrenamiento y también tenía archivos CSV.

Los archivos CSV son muy importantes porque conectan cada imagen con sus valores reales de `v` y `w`.

Es decir, el CSV le dice al programa:

**para esta imagen, la respuesta correcta es esta velocidad y este giro.**

Yo usé esta información para entrenar el modelo.

---

## Dataset combinado

También trabajé con el dataset combinado.

Este dataset tiene más imágenes y más variedad. Esto es importante porque, cuando una red neuronal ve más ejemplos, normalmente puede aprender mejor.

En mi caso, usé aproximadamente:

**50.000 imágenes**

Esto es bastante útil porque el modelo necesita ver muchas situaciones diferentes del circuito.

Por ejemplo, el dataset puede contener:

* Imágenes en rectas.
* Imágenes en curvas suaves.
* Imágenes en curvas más cerradas.
* Imágenes con diferentes posiciones del coche dentro del circuito.
* Imágenes donde el robot debe avanzar rápido.
* Imágenes donde el robot debe girar más.

Cuanta más variedad tenga el dataset, más posibilidades tiene el modelo de aprender un comportamiento estable.

---

# 🔁 Flujo general del proyecto

El trabajo que hice sigue este orden:

1. Primero preparé el dataset.
2. Después preprocesé las imágenes.
3. Luego apliqué Data Augmentation.
4. Después preparé los datos para PyTorch.
5. Luego entrené el modelo.
6. Después validé el modelo.
7. Luego guardé el mejor modelo.
8. Después exporté el modelo a ONNX.
9. Finalmente integré el modelo en Unibotics.

De forma sencilla, el flujo completo fue:

**datos → preparación → entrenamiento → modelo ONNX → robot conduciendo en Unibotics**

---

# ⚙️ Metodología

En esta sección explico con mucho detalle todo lo que hice.

---

## 1. Carga de datos

Lo primero que hice fue cargar las etiquetas del dataset.

Las etiquetas estaban guardadas en un archivo CSV.

Ese archivo tenía la información de cada imagen y los valores de control asociados.

Cada fila del CSV representa un ejemplo.

Una fila contiene:

* El nombre o la ruta de la imagen.
* La velocidad lineal `v`.
* La velocidad angular `w`.

Yo cargué este archivo para poder saber qué imagen correspondía a qué comando.

Esto es importante porque la red neuronal no aprende sola sin ejemplos. Necesita ver una entrada y una salida correcta.

En esta práctica:

* La entrada es la imagen.
* La salida correcta es `[v, w]`.

Entonces, yo preparé los datos para que el modelo pudiera aprender esta relación.

---

## 2. Por qué fue necesario preprocesar las imágenes

Antes de entrenar, no usé las imágenes directamente tal como venían.

Primero las preparé.

Esto se llama preprocesamiento.

El preprocesamiento es muy importante porque ayuda a que la red neuronal aprenda mejor.

Una imagen original puede tener mucha información que no sirve para conducir. Por ejemplo, puede aparecer el cielo, el horizonte o partes del entorno que no son útiles.

Si dejo toda esa información, el modelo puede confundirse o perder tiempo aprendiendo cosas que no son importantes.

Por eso, yo limpié y preparé las imágenes antes de pasarlas al modelo.

El preprocesamiento que hice tuvo tres pasos principales:

1. Recortar la imagen.
2. Cambiar el tamaño de la imagen.
3. Normalizar los valores de los píxeles.

---

## 3. Recorte de la imagen

El primer paso que hice fue recortar la imagen.

Yo eliminé la parte superior de la imagen porque no era la parte más importante para conducir.

La parte superior puede contener información como:

* El cielo.
* El fondo.
* El horizonte.
* Zonas lejanas.
* Elementos que no ayudan mucho al control inmediato.

Para conducir, la parte más útil es la parte inferior de la imagen, porque ahí está la carretera más cercana al robot.

Es como cuando una persona conduce. No mira solamente el cielo; mira la carretera que tiene delante.

Por eso, yo hice que el modelo se concentrara en la zona importante.

Con este recorte, conseguí varias cosas:

* Reducir información innecesaria.
* Ayudar al modelo a centrarse en la carretera.
* Hacer que el entrenamiento fuera más eficiente.
* Evitar que la red aprendiera detalles que no son importantes.

En resumen, recorté la imagen para que el modelo mirara principalmente la zona por donde tiene que conducir.

---

## 4. Cambio de tamaño de la imagen

Después de recortar la imagen, cambié su tamaño.

Todas las imágenes deben tener el mismo tamaño para poder entrar correctamente en la red neuronal.

Yo redimensioné las imágenes a:

**200 x 66 píxeles**

Este tamaño es pequeño, pero suficiente para mantener la información importante de la carretera.

Hice esto por varias razones:

* Para que todas las imágenes tengan el mismo formato.
* Para que el entrenamiento sea más rápido.
* Para reducir el uso de memoria.
* Para que el modelo no sea demasiado pesado.
* Para que la inferencia en tiempo real sea más fácil.

Si las imágenes fueran muy grandes, el modelo necesitaría más tiempo para procesarlas. Eso no es bueno para un robot, porque el robot necesita reaccionar rápido.

Por eso, usar una imagen más pequeña ayuda a que el sistema pueda funcionar en tiempo real.

---

## 5. Normalización de la imagen

Después de cambiar el tamaño, normalicé los valores de los píxeles.

Normalmente, una imagen tiene valores entre 0 y 255.

Pero para una red neuronal, es mejor trabajar con valores entre 0 y 1.

Por eso dividí los valores de los píxeles entre 255.

Esto hace que la red trabaje con números más pequeños y más estables.

La normalización ayuda a:

* Mejorar la estabilidad del entrenamiento.
* Hacer que la red aprenda de forma más suave.
* Evitar valores muy grandes.
* Mejorar el comportamiento de los gradientes.

De forma simple, normalizar es como poner todos los valores en una escala más cómoda para la red.

---

## 6. Conversión de formato de imagen

También tuve que adaptar el formato de la imagen para PyTorch.

Las imágenes normalmente se guardan con este orden:

**alto, ancho, canales**

Pero PyTorch trabaja con este orden:

**canales, alto, ancho**

Entonces, yo cambié el orden de las dimensiones.

Esto es una parte técnica, pero importante. Si no hago este cambio, la red no interpreta la imagen correctamente.

Por ejemplo, una imagen final queda con esta forma:

**3 x 66 x 200**

Esto significa:

* 3 canales de color: rojo, verde y azul.
* 66 píxeles de alto.
* 200 píxeles de ancho.

Así la imagen ya está preparada para entrar en el modelo.

---

# 🌞 Data Augmentation

Después hice Data Augmentation.

Data Augmentation significa crear pequeñas variaciones de las imágenes para que el modelo aprenda mejor.

En mi caso, usé cambios de brillo.

La idea es que el modelo no dependa siempre de una sola iluminación.

Por ejemplo, en una imagen puede haber:

* Más luz.
* Menos luz.
* Sombras.
* Reflejos.
* Zonas más oscuras.
* Zonas más claras.

Si el modelo solo aprende con imágenes siempre iguales, puede fallar cuando la iluminación cambia un poco.

Por eso, yo cambié el brillo de algunas imágenes durante el entrenamiento.

Esto ayuda a que el modelo sea más robusto.

De forma sencilla, es como entrenar al robot para que no diga:

**“solo sé conducir cuando la imagen tiene exactamente esta luz.”**

Yo quería que el modelo pudiera conducir aunque la imagen fuera un poco más clara o un poco más oscura.

---

# 🧩 Preparación de los datos en PyTorch

Después de preparar las imágenes, tuve que organizar los datos para entrenar el modelo en PyTorch.

Para eso, creé una clase de dataset.

Esta clase tenía una función muy importante: cada vez que el entrenamiento necesita un ejemplo, la clase hace esto:

1. Busca la fila correspondiente en el CSV.
2. Lee la ruta de la imagen.
3. Carga la imagen.
4. Preprocesa la imagen.
5. Convierte la imagen en tensor.
6. Lee los valores `v` y `w`.
7. Devuelve la imagen y sus etiquetas.

Esto permite que PyTorch pueda entrenar el modelo de forma ordenada.

---

## ¿Qué es un tensor?

Un tensor es una forma de representar datos numéricos.

Para explicarlo de forma simple, una imagen para el ordenador no es una foto como la vemos nosotros. Para el ordenador, una imagen es una tabla de números.

Cada píxel tiene valores numéricos.

Entonces, yo convertí la imagen a tensor para que la red neuronal pudiera trabajar con ella.

La imagen entra como números, no como una foto normal.

---

## Etiquetas del modelo

Las etiquetas son los valores que el modelo tiene que aprender a predecir.

En esta práctica, cada etiqueta tiene dos números:

* `v`
* `w`

Entonces, para cada imagen, el modelo intenta predecir estos dos valores.

Durante el entrenamiento, compara su predicción con los valores reales.

Si se equivoca mucho, la pérdida es grande.
Si se equivoca poco, la pérdida es pequeña.

Poco a poco, el modelo ajusta sus pesos para equivocarse menos.

---

# 📦 DataLoader

Después usé DataLoader para cargar los datos por lotes.

Un lote significa que el modelo no entrena con una sola imagen cada vez, sino con varias imágenes juntas.

En mi caso, usé lotes de 64 imágenes.

Esto significa que en cada paso de entrenamiento, el modelo ve 64 imágenes al mismo tiempo.

Esto ayuda a que el entrenamiento sea más rápido y más estable.

También mezclé los datos durante el entrenamiento. Esto es importante porque no quiero que el modelo vea siempre las imágenes en el mismo orden.

Si siempre ve los datos en el mismo orden, puede aprender de forma menos general.

Por eso, usar DataLoader con mezcla ayuda a entrenar mejor.

---

# ✂️ División entre entrenamiento y validación

También dividí el dataset en dos partes:

* Una parte para entrenamiento.
* Una parte para validación.

Usé aproximadamente:

* 40.000 imágenes para entrenamiento.
* 10.000 imágenes para validación.

La parte de entrenamiento sirve para que el modelo aprenda.

La parte de validación sirve para comprobar si el modelo funciona con imágenes que no está usando directamente para aprender.

Esto es muy importante.

Si solo miro el error de entrenamiento, no sé si el modelo está aprendiendo de verdad o si solo está memorizando.

Pero si también baja el error de validación, significa que el modelo está aprendiendo patrones más generales.

---

# 🧠 Modelo usado

Después preparé el modelo de Deep Learning.

Usé una red neuronal convolucional inspirada en PilotNet.

No voy a explicar aquí el código completo de la arquitectura porque lo importante es entender la idea.

La red tiene dos partes principales:

1. Una parte que mira la imagen y extrae información visual.
2. Una parte que usa esa información para predecir `v` y `w`.

---

## Cómo entiende la imagen el modelo

La red neuronal no entiende la imagen como una persona.

Una persona mira la imagen y puede decir:

“aquí hay una curva”
“aquí la carretera sigue recta”
“aquí tengo que girar”

Pero la red trabaja con números.

Lo que hace la red es aprender patrones visuales.

Por ejemplo, puede aprender cosas como:

* Bordes de la carretera.
* Forma de la curva.
* Posición del camino.
* Dirección del circuito.
* Cambios visuales que indican que hay que girar.

Después, con esa información, el modelo predice los dos valores de salida.

---

## Salida del modelo

La salida del modelo son dos números:

* `v`
* `w`

La velocidad lineal `v` indica cuánto debe avanzar el robot.

La velocidad angular `w` indica cuánto debe girar el robot.

Por ejemplo:

* Si `w` está cerca de cero, el robot sigue más o menos recto.
* Si `w` es positivo o negativo, el robot gira.
* Si `v` es mayor, el robot avanza más rápido.
* Si `v` es menor, el robot avanza más despacio.

Entonces, el modelo aprende a mirar una imagen y decir:

**“con esta imagen, creo que el robot debe avanzar así y girar así.”**

---

# 🏋️ Entrenamiento del modelo

Después entrené el modelo usando PyTorch.

Entrenar significa repetir muchas veces este proceso:

1. Le doy al modelo una imagen.
2. El modelo predice `v` y `w`.
3. Comparo la predicción con los valores reales.
4. Calculo cuánto se ha equivocado.
5. Ajusto el modelo para que la próxima vez se equivoque menos.

Este proceso se repite muchas veces con muchas imágenes.

Al principio, el modelo no sabe conducir. Sus predicciones no son muy buenas.

Pero poco a poco, después de ver muchos ejemplos, empieza a aprender.

---

## Función de pérdida

Para medir el error usé una función de pérdida.

La función de pérdida mide la diferencia entre:

* Lo que el modelo predice.
* Lo que debería haber predicho.

En esta práctica, la pérdida compara la velocidad real y la velocidad predicha, y también compara el giro real y el giro predicho.

Si la diferencia es grande, la pérdida es grande.

Si la diferencia es pequeña, la pérdida es pequeña.

Entonces, el objetivo del entrenamiento es hacer que la pérdida baje.

De forma simple:

**la pérdida es como una nota que dice cuánto se ha equivocado el modelo.**

---

## Optimizador

También usé un optimizador.

El optimizador es la parte que cambia los pesos de la red para mejorar el resultado.

Podemos imaginarlo como una persona que corrige al modelo después de cada error.

El modelo predice algo, se calcula el error, y el optimizador ajusta un poco el modelo para que mejore.

Yo usé Adam porque es un optimizador muy común en Deep Learning y suele funcionar bien.

---

# 📉 Resultados del entrenamiento

Entrené el modelo durante 3 épocas.

Una época significa que el modelo ve todo el dataset de entrenamiento una vez.

Los resultados fueron:

* En la época 1, la pérdida de entrenamiento fue 0.0553 y la pérdida de validación fue 0.0560.
* En la época 2, la pérdida de entrenamiento bajó a 0.0507 y la pérdida de validación bajó a 0.0492.
* En la época 3, la pérdida de entrenamiento bajó a 0.0471 y la pérdida de validación bajó a 0.0453.

Esto fue una buena señal.

La pérdida fue bajando poco a poco.

Eso significa que el modelo aprendió mejor con cada época.

También fue importante que la pérdida de validación bajara, porque eso significa que el modelo no solo estaba aprendiendo los ejemplos de memoria, sino que también mejoraba con datos de validación.

---

# 💾 Guardado del modelo

Después de entrenar, guardé el mejor modelo.

Esto es importante porque el entrenamiento puede generar varios estados del modelo.

Yo quería quedarme con el modelo que funcionaba mejor en validación.

El modelo guardado contenía los pesos aprendidos por la red.

Los pesos son como la memoria del modelo. Son los números internos que la red usa para hacer sus predicciones.

Una vez guardado, pude usar ese modelo más tarde sin tener que entrenarlo otra vez desde cero.

---

# 🔍 Evaluación del modelo

Después de entrenar, comprobé algunas predicciones del modelo.

La idea fue comparar:

* El valor real.
* El valor predicho por la red.

Por ejemplo, tenía un caso donde el valor real era aproximadamente:

* `v = 6.80`
* `w = 0.002`

Y el modelo predijo aproximadamente:

* `v = 6.74`
* `w = -0.013`

La predicción no era exactamente igual, pero era bastante cercana.

Esto me indicó que el modelo había aprendido una relación razonable entre la imagen y los comandos.

En conducción autónoma, no siempre hace falta que el valor sea exactamente igual, pero sí tiene que ser suficientemente bueno para que el robot se mueva de forma estable.

---

# 🖼️ Visualización del preprocesamiento

También añadí imágenes para mostrar el proceso de preparación de datos.

Esto ayuda a entender qué hice antes de entrenar el modelo.

---

## Distribución de los datos

![Distribution](img1_P4.png)

En esta imagen mostré cómo estaban distribuidos los valores del dataset.

Esto es útil porque permite ver si hay muchos ejemplos de un tipo y pocos de otro.

Por ejemplo, si hay muchas imágenes de rectas y pocas imágenes de curvas fuertes, el modelo puede aprender mejor las rectas que las curvas.

Por eso, mirar la distribución ayuda a entender la calidad del dataset.

---

## Imagen original

![Original](img2_P4.png)

Aquí mostré una imagen original del dataset.

Esta es la imagen antes de hacer cambios.

La imagen original contiene toda la información capturada por la cámara.

Pero no toda esa información es útil para conducir.

---

## Imagen recortada

![Cropped](img3_P4.png)

Aquí mostré la imagen después de recortar la parte superior.

Con este paso eliminé información menos importante y dejé más visible la zona de conducción.

Esto ayuda al modelo a concentrarse en la carretera.

---

## Imagen redimensionada

![Resized](img4_P4.png)

Aquí mostré la imagen después de cambiar su tamaño.

La imagen ahora es más pequeña, pero todavía mantiene la información importante.

Esto ayuda a entrenar más rápido y a usar menos memoria.

---

## Imagen procesada

![Processed](img5_P4.png)

Esta imagen representa el resultado después del preprocesamiento.

Es decir, esta es la imagen que el modelo recibe durante el entrenamiento.

---

## Imagen con cambio de brillo

![Brightness](img6_P4.png)

Aquí mostré un ejemplo de Data Augmentation con brillo.

Esto significa que cambié un poco la luz de la imagen para hacer el entrenamiento más robusto.

Con esto, el modelo aprende mejor a trabajar con imágenes que no tienen siempre la misma iluminación.

---

# 📦 Exportación a ONNX

Después de entrenar el modelo en PyTorch, lo convertí a formato ONNX.

Hice esto porque necesitaba usar el modelo dentro de RoboticsAcademy / Unibotics.

El modelo entrenado en PyTorch no siempre se usa directamente dentro del simulador. Por eso, ONNX sirve como un formato intermedio.

La idea fue:

**modelo entrenado en PyTorch → modelo exportado en ONNX → modelo usado en Unibotics**

El archivo final exportado fue:

**model_single.onnx**

El tamaño del modelo era pequeño, aproximadamente 1 MB.

Esto es bueno porque un modelo pequeño es más fácil de ejecutar en tiempo real.

---

## Por qué ONNX fue importante

ONNX fue importante porque me permitió llevar el modelo entrenado a la parte de inferencia.

Entrenar el modelo es una cosa.
Usar el modelo para controlar el robot es otra cosa.

ONNX me permitió pasar de la parte de entrenamiento a la parte de ejecución dentro del simulador.

Es como guardar el cerebro entrenado del robot en un formato que Unibotics puede usar.

---

# ⚡ Inferencia en tiempo real

Después de exportar el modelo, lo usé para hacer inferencia.

Inferencia significa usar el modelo ya entrenado para hacer predicciones.

Durante la inferencia, el modelo ya no aprende. Solo usa lo que ya aprendió.

El proceso fue este:

1. El robot captura una imagen con la cámara.
2. Yo preproceso esa imagen igual que en el entrenamiento.
3. La imagen entra en el modelo ONNX.
4. El modelo devuelve `v` y `w`.
5. Yo mando esos valores al robot.
6. El robot se mueve.
7. El proceso se repite continuamente.

Esto ocurre en tiempo real.

Eso significa que el robot no espera a tener muchas imágenes. Va tomando decisiones mientras se mueve.

---

# 🤖 Integración en Unibotics

La última parte fue integrar el modelo dentro de Unibotics.

Esto fue muy importante porque ahí comprobé si el modelo funcionaba realmente para controlar el robot.

El robot hacía este ciclo:

**capturar imagen → preprocesar → predecir → mover**

Primero, el robot capturaba la imagen de la cámara.

Después, yo aplicaba el mismo preprocesamiento que usé durante el entrenamiento.

Esto es muy importante. Si durante el entrenamiento la imagen tenía un tamaño y una normalización, durante la inferencia tenía que hacer lo mismo.

Si no, el modelo podría confundirse.

Luego, el modelo ONNX calculaba la predicción.

La predicción daba dos valores:

* `v`
* `w`

Después, yo usaba esos valores para mover el robot con las funciones de control del simulador.

Así el robot podía conducir usando solamente la cámara y el modelo entrenado.

---

# 🖼️ Resultados en Unibotics

Después de integrar el modelo, probé el robot en el circuito.

El modelo fue capaz de controlar el robot y seguir el camino.

Los resultados mostraron que el robot podía:

* Avanzar por el circuito.
* Girar según la imagen.
* Mantener un movimiento estable.
* Usar predicciones suaves.
* Funcionar en tiempo real.

---

## Resultado 1

![Result1](imgR1_P4.png)

En este primer resultado, el robot empieza a conducir usando el modelo entrenado.

Aquí el robot ya no usa reglas manuales. Usa la red neuronal para decidir su velocidad y su giro.

---

## Resultado 2

![Result2](imgR2_P4.png)

En este resultado, el robot sigue avanzando por el circuito.

El modelo mira la imagen y predice los comandos necesarios para mantenerse dentro del camino.

---

## Resultado 3

![Result3](imgR3_P4.png)

Aquí se puede ver que el robot sigue adaptando su dirección.

Cuando la imagen cambia, la predicción del modelo también cambia.

Esto permite que el robot responda al circuito.

---

## Resultado 4

![Result4](imgR4_P4.png)

En este resultado final, el robot mantiene una conducción estable.

El comportamiento fue suficientemente bueno para seguir el circuito usando solamente visión y Deep Learning.

---

# 🛠️ Tecnologías utilizadas

En esta práctica utilicé varias tecnologías.

Usé **Python** como lenguaje principal.

Usé **PyTorch** para crear, entrenar y guardar la red neuronal.

Usé **OpenCV** para leer imágenes y hacer el preprocesamiento.

Usé **NumPy** para trabajar con matrices y valores numéricos.

Usé **Pandas** para leer los archivos CSV del dataset.

Usé **ONNX** para exportar el modelo entrenado.

Usé **ONNX Runtime** para ejecutar el modelo exportado.

Usé **RoboticsAcademy / Unibotics** para probar el robot en el simulador.

Cada herramienta tuvo una función concreta dentro del proyecto.

---

# 📊 Resumen de lo que hice

En resumen, en esta práctica hice todo este proceso:

1. Primero entendí que el objetivo era controlar el robot usando imágenes.
2. Después cargué el dataset con imágenes y comandos.
3. Luego leí los archivos CSV con las velocidades `v` y `w`.
4. Después preparé las imágenes para la red neuronal.
5. Recorté la parte superior de las imágenes.
6. Cambié el tamaño de las imágenes.
7. Normalicé los valores de los píxeles.
8. Apliqué cambios de brillo para mejorar la robustez.
9. Preparé los datos en formato compatible con PyTorch.
10. Organicé los datos en lotes para entrenar.
11. Dividí los datos en entrenamiento y validación.
12. Preparé una red neuronal para predecir velocidad y giro.
13. Entrené el modelo con muchas imágenes.
14. Observé que la pérdida bajaba durante el entrenamiento.
15. Guardé el mejor modelo.
16. Comparé algunas predicciones con los valores reales.
17. Exporté el modelo a ONNX.
18. Cargué el modelo ONNX para inferencia.
19. Integré el modelo dentro de Unibotics.
20. Probé el robot conduciendo en tiempo real.

---

# ✅ Conclusiones

En esta práctica conseguí implementar un sistema de control visual End-to-End usando Deep Learning.

Lo más importante es que el robot aprendió a conducir usando imágenes de la cámara frontal.

Primero preparé los datos. Después preprocesé las imágenes para que el modelo recibiera información más limpia y más útil.

Luego entrené una red neuronal para predecir directamente la velocidad lineal y la velocidad angular.

Durante el entrenamiento, observé que la pérdida bajaba, lo que significa que el modelo estaba aprendiendo.

Después exporté el modelo a ONNX para poder usarlo en Unibotics.

Finalmente, integré el modelo en el simulador y el robot pudo conducir en tiempo real usando solamente la imagen de la cámara.

Esta práctica me ayudó a entender que Deep Learning puede reemplazar una parte del sistema clásico de visión y control.

En vez de programar reglas manuales, entrené un modelo con ejemplos para que aprendiera el comportamiento de conducción.

---

# 🚀 Posibles mejoras futuras

Aunque el resultado fue bueno, el sistema se podría mejorar.

Una mejora sería entrenar durante más épocas. Con más entrenamiento, el modelo podría aprender mejor, aunque también habría que vigilar que no memorice demasiado.

Otra mejora sería usar más datos, especialmente más ejemplos de curvas difíciles. Si el dataset tiene más variedad, el modelo puede aprender situaciones más diferentes.

También se podría mejorar el balance del dataset. Si hay muchas rectas y pocas curvas, el modelo puede aprender muy bien a ir recto pero peor a girar.

Otra mejora sería usar modelos con memoria temporal. En esta práctica, el modelo toma una decisión usando una imagen. Pero en conducción real, también puede ser útil recordar imágenes anteriores.

Por ejemplo, una arquitectura con CNN y LSTM podría ayudar porque tendría en cuenta la evolución del movimiento.

También se podría probar el sistema en otros circuitos o incluso adaptarlo a un robot real.

---

# 🧠 Explicación simple de todo el trabajo

De forma muy sencilla, lo que hice fue enseñar al robot a conducir mirando imágenes.

Primero tenía muchas imágenes del circuito. Cada imagen tenía una respuesta correcta: la velocidad y el giro que el robot debía hacer.

Después preparé esas imágenes. Les quité la parte que no era importante, las hice más pequeñas y puse sus valores en una escala más cómoda.

Luego usé esas imágenes para entrenar una red neuronal.

Al principio, la red no sabía conducir. Pero después de ver muchos ejemplos, empezó a aprender.

La red aprendió algo parecido a esto:

* Si ve que el camino sigue recto, predice poco giro.
* Si ve que el camino gira, predice más giro.
* Si necesita avanzar, predice una velocidad lineal.
* Si la situación cambia, cambia también la predicción.

Después guardé el modelo entrenado.

Luego convertí ese modelo a ONNX para poder usarlo dentro de Unibotics.

Finalmente, puse el modelo dentro del simulador. El robot empezó a capturar imágenes con la cámara, el modelo predijo `v` y `w`, y el robot se movió usando esos valores.

En resumen, hice este ciclo:

**mirar con la cámara → preparar la imagen → usar el modelo → predecir velocidad y giro → mover el robot**

Y este ciclo se repite todo el tiempo mientras el robot conduce.

Lo importante de esta práctica es que yo no escribí reglas manuales para cada curva. En vez de eso, entrené un modelo para que aprendiera a conducir usando ejemplos.

## 🎥 Video de demostración

https://drive.google.com/file/d/1u5KwVlA786fDR3145DbC-y8QguiN53BB/view?usp=sharing
