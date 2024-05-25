### Clasificación de imágenes de retina con redes convolucionales

## CONTENIDO
Introducción

Armado de Base de datos 

Aumentación de datos

CNN

Resultados

Conclusiones

## INTRODUCCION

Para introducirnos al tema, trabajamos sobre datos obtenidos por la Facultad de ciencias químicas, los cuales los que usaremos para nuestro proyecto integrador de la carrera. Los datos se basan en una experiencia realizada en ratas albinas que fueron expuestas a luz led de baja intensidad, y se obtuvieron cortes histológicos de de sus retinas a los distintos días de exposición, con el objetivo de evaluar su daño retinal.
A la derecha ven una imagen para recordar las muchas capas de la retina, de las cuales solo nos enfocamos en estas dos que se remarcaron
Las van a encontrar a lo largo de la presentación como CAPA GRUESA a la capa nuclear externa Y CAPA FINA a la capa nuclear interna.
El objetivo fue entrenar una red neuronal convolucional con nuestras imagenes, y que la misma detecte como 0 en caso de capa gruesa y 1 como datos de capa fina.

![444018](https://github.com/mlzulatto/Clasificaci-n-de-im-genes-de-retina-con-redes-convolucionales/assets/138075122/be9220dd-f018-4fa9-8362-0f727e49af63)

## BASE DE DATOS

Como primer paso seleccionamos rois manualmente  en cada capa gruesa de cada imagen, y luego lo hicimos con las capas finas.
Usando las imagenes *ImagenRetina1.tif*,*ImagenRetina2.tif*,*ImagenRetina3.tif*,*ImagenRetina4.tif*

**IMAGENES DE RETINA CNN-Solo selección de Rois Presentación**

Una vez que saliamos de ese for se generaban los recortes que ibamos a meter en la red, fueron de 15x15, y recorrieron cada roi con un corrimiento superpuesto que como se ve ahi dice tamaño medio de recorte, es decir un corrimiento de 7 pixeles hacia el costado.
Elegimos 15x15 porque nos pareció un buen tamaño que abarcaba al menos una célula completa de la capa gruesa.
A medida que estos recortes se generaban se guardaban en dos carpetas denominadas G y F

Hasta este punto lo hicimos en una ntb para que pudiésemos generar los datos una vez y quedaran guardados.

El último paso fue el stack vertical de todos estos recortes para usarlos en la cnn, se estableció un eje anexo, que nos preserva la posición del recorte.
Una vez finalizado el apilado quedaron 18478 recortes con una dimensión de 15x15

## AUMENTACIÓN DE DATOS

Como la cantidad de imágenes que usamos fueron pocas, y los recortes sumaban cerca de 13 mil datos de entrenamiento y 5500 de testeo,y en base a eso se presentaron inconvenientes,  se realizó una aumentación de datos, con 4 rotaciones a + y - 5° A +Y - 10% ,También con filtro de ruido sal y pimienta, y  con una función de variación de brillo aumentando los datos 5veces más .

![Captura de pantalla 2024-05-25 203241](https://github.com/mlzulatto/Clasificaci-n-de-im-genes-de-retina-con-redes-convolucionales/assets/138075122/c8430545-43b7-402f-8d39-b8a0267d504c)


## CNN

Ahora veremos cómo fue evolucionando la red al variar los parametros. En la primera se decidio trabajamos sin la aumentación de datos, luego una capa convolucional con 32 filtros y un tamaño de kernel de 5x5, a continuación una capa densa de 32 neuronas y por ultimo una capa de salida que clasifica los datos en capa gruesa o capa fina. En cuanto a la configuración del entrenamiento de la red, se eligio 20 epocas, batch size de 32 y un learning rate predeterminado de 0,001. Como resultado se observó en las curvas que la red era muy inestable y crecia muy abruptamente el entrenamiento.
Para lograr estabilidad proearning rate y las mejores fueron notorias en ambas curvas de la prueba 2, sin embargo se observa overfitting.

Para solucionar el problema de overfitting se probó disminuir el tamaño del kernel en la capa convolucional, aplicar dos capas convolucionales y disminuir el número de neuronas en la capa densa. Se obtuvieron curvas suaves y a su vez aumento de la precision y disminución en el valor de la funcion de perdida.

Como ultimas pruebas, con el objetivo de aumentar la precision y disminuir la funcion de perdida, se aumentaron los datos de la capa de entrada. La prueba 5 se realizó con dos convolucionales y una capa densa de 32 neuronas, obteniendo mejoras importantes pero una curva con overfitting, mientras que en la prueba 6 se lograron los mejores valores de precision y funcion de perdida y ambas curvas suaves al disminuir la cantidad de neuronas en la capa densa. 

**IMAGENES DE RETINA CNN-SIN SELECCIÓN DE ROIS-presentación final Prueba 5**

**IMAGENES DE RETINA CNN-SIN SELECCIÓN DE ROIS-presentación final Prueba 6**


![Captura de pantalla 2024-05-25 203350](https://github.com/mlzulatto/Clasificaci-n-de-im-genes-de-retina-con-redes-convolucionales/assets/138075122/364164a2-4240-41d5-bde1-df8283ed9c7f)
![Captura de pantalla 2024-05-25 203416](https://github.com/mlzulatto/Clasificaci-n-de-im-genes-de-retina-con-redes-convolucionales/assets/138075122/a6772580-f991-4edb-a24c-0bba22b04dd5)
![Captura de pantalla 2024-05-25 203431](https://github.com/mlzulatto/Clasificaci-n-de-im-genes-de-retina-con-redes-convolucionales/assets/138075122/0a484328-b1c3-446b-9e32-c2e497a9a4e1)


## RESULTADOS

En el primer gráfico tuvimos inconvenientes de sobreajuste, fue necesario implementar algunas medidas para disminuirlo y poder hacer un modelo más generalizado:
1- Primeramente aumentamos los datos en gran cantidad ya que el lote de imágenes de entrada que usamos era relativamente bajo
2- Se decidió bajar  la cantidad ya que inicialmente la red era muy compleja para nuestros datos, y se observaron mejoras instantáneamente.
3- Al aplicar el dropout no se obtuvo mejoras 

-También se aplicó el parámetro de regularización L2 que permitió disminuir la complejidad del modelo y disminuir la función de coste

-En los primeros gráficos también se observó que la curva de entrenamiento  aumenta la curva muy rápido , y para lograr una subida más suave modificamos el valor del learning rate( se disminuyó), con lo cual fue necesario aumentar la cantidad de épocas para poder ver la estabilización del modelo. 

![Captura de pantalla 2024-05-25 203446](https://github.com/mlzulatto/Clasificaci-n-de-im-genes-de-retina-con-redes-convolucionales/assets/138075122/9c96716a-03f7-4011-8203-5204b43b20b3)


## CONCLUSIONES

Como pudimos reducir el overfitting que teníamos en un inicio, se pudo obtener una curva suave de loss que llega a un valor del 45% al final de la prueba.

-La precisión (Accuracy) y el desempeño de la red  podría mejorar  en el caso de usar un lote de imágenes mayor.

-La curva de Loss tiene un buen comportamiento descendente gracias a la reducción de overfitting

-Se estima que el entrenamiento puede estar afectado al tamaño de recorte usado, y los tamaños de rois obtenidos especialmente en la Capa fina.



