# ImageClassification

Image Classification (Pizza and Ice cream) using a CNN.

Aldo Tena García - A01275222

# Generación o selección del set de datos

El dataset seleccionado para este modelo es "Pizza vs Ice Cream", fue descargado de la plataforma kaggle ([Pizza vs Ice Cream](https://www.kaggle.com/datasets/hemendrasr/pizza-vs-ice-cream)) el 14 de mayo del 2024.La razón prinicipal para la selección de este dataset fue el objetivo de comparar 2 elementos diferentes usando el modelo a desarrollar, en este caso 2 alimentos diferentes, ademas de la estructura ya definida del set (con las carpetas train y test con sus respectivos tags) y la cantidad inicial de imagenes contenidas en el dataset. Después de ver nuevamente la cantidad de imagenes necesarias para entrenar y verificar el correcto funcionamiento del modelo se modifico la cantidad de imágenes (450 en total por categoria despues de elementar imágenes repetidas o incorrectas) presentes dentro de los datasets para dejar 90 imágenes dentro de las carpetas de test y 360 imágenes dentro de la carpeta de train, esto quedo en una proporción de 80-20 entre train y test.

Después de seleccionar el dataset se investigo en Google Académico modelos similares al que se propuso desarrollar, lo mása cercano al modelo propuesto eran multiples articulos en el que usaban modelos de CNN (Convolutional neural network) para clasificar multiples tipos de alimentos, para el desarrollo de este modelo se seleccionaron los siguientes articulos:

* [1] AMIN, Muhammad Zain; ALI, Amir. InceptFood: A CNN Based Classification Approach for Recognizing Food Images. En  *National Conference on Emerging Trends in Computing, Satisics and Mathematical Sciences, SBBWU* . 2017.
* [2] M. T. Islam, B. M. N. Karim Siddique, S. Rahman and T. Jabid, "Food Image Classification with Convolutional Neural Network," 2018 International Conference on Intelligent Informatics and Biomedical Sciences (ICIIBMS), Bangkok, Thailand, 2018, pp. 257-262

# Preprocesado de los datos

Para poder entrenar de la mejor posible el modelo se hara uso de tecnicas de data augmentation para poder suministrar los recursos necesarios para el entrenamiento y verificación del modelo, para esto se usaron los parametros propuestos por AMIN et al. [1]:

* Rotate left -30 degree
* Rotate right +30 degree
* Flip horizontally about Y axis
* Shear by a certain amount
* Rotate left +90 degree

Las imagenes fueron escaladas y posteriormente generadas con las dimensiones 224*224*3 de acuerdon con M. T. Islam et al. [3].

# Implementación de modelo

Al momento de elegir la implementación de un posible modelo se selecciono la implementació propuesta por M. T. Islam et al. [3], los factores que estuvieron involucrados en la selección de este modelo, como la documentación del mismo dentro de su respectivo articulo y el uso de herramientas previamente expuestas en clases.

![1716532176840](image/README/1716532176840.png "Architecture of proposed Convolutional Neural Network Model")

El modelo seleccionado consta de 5 diferentes capa convolucionales con funciones de activación ReLu que terminan en una neurona de la función softmax, en este caso en especifico como se tiene un problema de clasificación binario se opto por cambiar esta por una neurona con una función sigmoid. De acuerdo con M. T. Islam et al. [3] se van a considerar 25 epochs como primer acercamiento y ejecución del modelo.

# Evaluación inicial del modelo

Como se puede ver en las imagenes obtenidas de la primera ejecución del modelo, especificamente en la etapa de train, este obtiene un promedio de accuracy del 95% alrededor de la epoch 20 y se mantiene dentro de un rango entre el 92-95% mientras que el loss llega a su punto más bajo en la epoch 20, despues de la mismas esta empieza a subir nuevamente.

El valor obtenido en test accuracy fue de: 0.7666666507720947, esta marca una diferencia de cerca del 20% entre train y test accuracy por lo que se puede observar un caso de overfitting.

![1716532887381](image/README/1716532887381.png)![1716532898405](image/README/1716532898405.png)

Para evaluar de forma más decuada el modelo se uso de la matriz de confusión, la matriz de confusión es una tabla que funciona para mostrar los resultados obtenidos por el modelo desarrollado haciendo uso de un simple formato en el que se muestran las predicciones esperadas contra las predicciones realizadas. La tabla contiene los siguientes valores:

* True Positives (TP) o Verdaderos Postivos (VP)
* True Negatives (TN) o Verdaderos Negativos (VN)
* False Positives (FP) o Falsos Postivos (FP)
* False Negatives (FN) o Falsos Negativos (FN)

  ![1717565463383](image/README/1717565463383.png)

La matriz de confusión resultante del modelo es la siguiente:

![1717565569700](image/README/1717565569700.png)

Como se puede observar hay una gran cantidad de desaciertos dentro de la matriz de confusión, se puede inferir que hay el modelo tiene dificultades para clasificar correctamente las imagenes de ambas categorias especialmente pertencientes a la categoria "icecream".

Hay multiples metricas de evaluación que surgen de los valores presentes en la matriz de confusión, para evaluar este modelo se opto por usar "Precision". La formula para obtener el valor de precision del modelo es:

Precision = TP/(TP+FP)

El valor de precision en este modelo es de: 0.47101449275362317, este valor es bajo para que un clasificador binario por lo que necesita pasar por una etapa de refinamiento.

# Refinamiento del modelo

Para mejorar el desempeño del modelo se tuvieron que realizar multiples ajustes en el mismo, empezando por la creación de una carpeta de validación, esta contiene 120 imágenes (60 de cada clase), estas imágenes fueron separadas de la carpeta train, por lo tanto la carpeta train tiene 600 imágenes (300 de cada categoria), la carpeta test tiene 180 imágenes (90 de cada categoria) y validation 120 imágenes (60 de categoria), como se menciono anteriormente.

Adicionalmente se modifico el modelo añadiendo 2 capas adicionales de procesamiento, cada una es una capa convolucional bidimensional (Conv2D) con 512 filtros, cada uno de tamaño 3x3, utilizando la función de activación ReLU y con padding 'same' para mantener el tamaño de la salida igual al de la entrada. A continuación, se incorpora una capa de normalización por lotes (Batch Normalization) para estabilizar y acelerar el proceso de entrenamiento, ajustando y escalando las activaciones de la capa anterior. Finalmente, se añade una capa de Dropout con una tasa del 25%, que desactiva aleatoriamente el 25% de las neuronas durante cada iteración de entrenamiento para prevenir el sobreajuste. Para garantizar que el modelo tuviera el tiempo y los elementos necesarios para llegar a tener el mejor desempeño posible se extendiaron las epocas de entranmiento de 25 a 50.

Despues de de ejcutar el modelo se obtuvo la siguiente matriz de confusión:

![1717568920811](image/README/1717568920811.png)

El valor de train accuracy fue de 0.8607 mientras que el test accuracy fue de 0.6888889074325562.

A partir de la matriz de confusión se obtuvo un valor de precision de 0.6842105263157895.

Despues de pasar por la etapa de refinamiento la metrica de evaluación precisión paso de  0.47101449275362317 a 0.6842105263157895, teniendo una mejora de 0.21 aproximadamente.
