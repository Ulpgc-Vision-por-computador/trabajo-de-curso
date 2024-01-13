# Trabajo de curso - HanDraw

## Introducción

La motivación que nos impulsó a realizar este proyecto se encuentra en nuestras ganas de aplicar las técnicas de visión por cumpotador en algo nuevo, en este caso, La principal idea que tuvimos fue la de poder pintar en la pantalla. En un principio queríamos usar algún tipo de lápiz para pintar con el dependiento de su distancia a la cámara. Más adelante explicaremos por qué cambiamos de idea. Nos decantamos por utilizar la librería **MediaPipe Hands.**  La detección y seguimiento de manos, como también el control de gestos son fundamentales en numerosas aplicaciones dentro del emergente mundo del metaverso o de la propia expresión artística, que es a la que más orientada se inclina nuestro proyecto.


## Objetivo de la propuesta

El objetivo principal de este proyecto es desarrollar una aplicación que permita dibujar en tiempo real sobre la imagen de vídeo por webCam utilizando la detección de manos. Para que la gente pueda expresarse artísticamente mediante un gesto de manos.



## Descripción técnica

El programa cuenta con dos funciones que se emplean en el código principal:
- `add_points_to_list`: Recibe la lista donde se almacenan los puntos a dibujar, los datos de la mano detectada, y el color que se esta usando en ese frame. La función toma las posiciones de los dedos pulgar e índice para calcular la distancia a la que se encuentran, en caso de que esta distancia sea menor a cierto umbral quiere decir que se esta haciendo el gesto de dibujar, por lo tanto se calcula la posición media entre ambos dedos y se añade a la lista junto con el color. Cuando no se esta haciendo el gesto de dibujar se añade una posición no útil a lista (-1, -1, -1), esto permite saber cuando la persona ha querido parar de dibujar y luego continuar en otra parte de la imagen.
```py
if distance < threshold:
      midpoint = ((thumb_x + index_x) / 2, (thumb_y + index_y) / 2, (thumb_z + index_z) / 2, point_color)
      list_of_draws.append(midpoint)

  # Añadir una posicion sin uso (-1, -1, -1) para cuando no se este dibujando
  elif list_of_draws and list_of_draws[-1][0] != -1: 
    list_of_draws.append((-1, -1, -1, (255, 255, 255)))
```

- `draw_lines`: Recibe la lista donde se almacenan los puntos a dibujar y el frame actual. La función

## Fuentes y tecnologías utilizadas

Las tecnologías de las que nos hemos apoyado a la hora de la realización de este proyecto se pueden resumir en las aiguientes:
- **Mediapipe Hands**: Biblioteca utilizada para la deteción y seguimiento de las manos y de sus puntos clave.
- **OpenCV**: Se emplea para el procesamiento de imágenes, la visualización en tiempo real y el dibujo en la imagen.
- **Python**: Lenguaje de programación utilizado para implementar la lógica del proyecto.


## Conclusiones y propuestas de ampliación

El proyecto ha logrado su objetivo principal de desarrollar una aplicación de dibujo interactivo con varias funcionalidades, se observa un rendimiento altamente satisfactorio en buenas condiciones lumínicas, y difícilmente se presenta un rendimiento no deseado. Una propuesta de ampliación interesante sería añadir la posibilidad de hacer dibujos en 3d sobre el espacio, lamentablemente la librería OpenCV implementa una funcionalidad que nos permita realizar esa tarea.

## Herramientas con las que nos hubiera gustado contar

Durante el desarrollo, nos hubiera gustado contar con varias cámras calibradas  para poder estimar de forma adecuada la profndidad de las manos en el espacio y conseguir una mayorprecisión a la hora de la detección de gestos y también para la propuesta de apmliación de dibujo 3d.

## Créditos materiales no originales del grupo

Agradecemos a MediaPipe por proporcionar una biblioteca robusta y eficiente para la detección de manos, y una buena guía, de la que nos hemos apoyado significativamente para la reallización de este proyecto. 
