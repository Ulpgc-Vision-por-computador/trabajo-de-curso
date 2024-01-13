# Trabajo de curso - HanDraw

## Introducción

La motivación que nos impulsó a realizar este proyecto se encuentra en nuestras ganas de aplicar las técnicas de visión por cumpotador en algo nuevo, en este caso, La principal idea que tuvimos fue la de poder pintar en la pantalla. En un principio queríamos usar algún tipo de lápiz para pintar con el dependiento de su distancia a la cámara. Más adelante explicaremos por qué cambiamos de idea. Nos decantamos por utilizar la librería **MediaPipe Hands.**  La detección y seguimiento de manos, como también el control de gestos son fundamentales en numerosas aplicaciones dentro del emergente mundo del metaverso o de la propia expresión artística, que es a la que más orientada se inclina nuestro proyecto.


## Objetivo de la propuesta

El objetivo principal de este proyecto es desarrollar una aplicación que permita dibujar en tiempo real sobre la imagen de vídeo por webCam utilizando la detección de manos. Para que la gente pueda expresarse artísticamente mediante un gesto de manos.



## Descripción técnica


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
