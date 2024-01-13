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

- `draw_lines`: Recibe la lista donde se almacenan los puntos a dibujar y el frame actual. La función recorre la lista de puntos mientras guarda el punto actual y el anterior. Se realiza la linea entre puntos siempre y cuando no se este pintando sobre la paleta de colores o la posicion anterior a la actual sea (-1, -1, -1), que esto quiere decir que por un momento se ha parado de dibujar por lo que los puntos no se deben unir. 
```py
for position in list_of_draws:
      prev_x, prev_y, prev_z = x, y, z
      x, y, z, color_point = position

      # No unir puntos si se paro de dibujar
      if (x, y, z) == (-1, -1, -1) or (prev_x == -1 and prev_y == -1 and prev_z == -1):
        continue

      height, width, deep = image.shape
      midpoint_x, midpoint_y, midpoint_z= int(x * width), int(y * height), int(z * deep)
      prev_midpoint_x, prev_midpoint_y, prev_midpoint_z= int(prev_x * width), int(prev_y * height), int(prev_z * deep)
      
      # Evitar dibujar sobre la paleta
      if 190 < prev_midpoint_x < 460 and 190 < midpoint_x < 460 and 0 < prev_midpoint_y < 90 and 0 < midpoint_y < 90:
        continue 
      
      cv2.line(image, (midpoint_x, midpoint_y), (prev_midpoint_x, prev_midpoint_y), color_point, 5)
```

En el código principal se hace lo siguiente:
- Inicializar las variables para la captura de la webcam, para el control del tiempo y poder añadir delay a las teclas y para añadir la imagen de la paleta de colores.
- Inicializar un diccionario cuyas claves son los nombres de los colores en la paleta, y sus valores son el color en el sistema BGR junto con la posicion en la que se encuentra el recuadro en la paleta. Tambien se inicializan variables para manejar el color actual seleccionado y la lista de puntos a dibujar.
- Se inicializa la clase Hands para realizar el tracking, en cada frame de la webcam se realiza el reconocimiento y se almacenan los datos. Para mejorar el rendimiento, durante el trackeo se prohibe la escritura sobre el frame.
```py
# Inicializar clase Hands
with mp_hands.Hands(
    model_complexity=0,
    min_detection_confidence=0.5,
    #max_num_hands=2,
    min_tracking_confidence=0.5) as hands:
  

  # Lista para almacenar las posiciones en donde se dibuja con su color  
  list_of_draws = []

  while cap.isOpened():
    success, image = cap.read()
    if not success:
      print("Ignoring empty camera frame.")
      continue

    # Almacenar los datos del detector de manos sobre el frame
    image.flags.writeable = False
    image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)   
    results = hands.process(image)
    image.flags.writeable = True
    image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
```
- Si se han obtenido resultados de una mano encontrada se llama a `add_points_to_list` la cual decide si se debe añadir un punto en la lista de dibujos o no.
- Despues de procesar el frame se llama a `draw_lines` para que lea la lista y muestre el dibujo.
- En la parte final del código se encuentran las acciones por teclas. Si se pulsa la 'z' se borra un elemento de la lista por lo que se estaria deshaciendo el dibujo. Si se pulsa 'd' se avanza al siguiente color dentro de la paleta de colores, se ha añadido un delay para que no avance más de una vez seguida accidentalmente. Si se pulsa 'q' se termina el programa.
```py
# Si se pulsa 'z' deshacer lo que se ha dibujado    
    if (cv2.waitKey(1) & 0xFF  == ord('z')):
      while list_of_draws and list_of_draws[-1][0] == (-1, -1, -1):
        list_of_draws.pop()
      
      if list_of_draws:
        list_of_draws.pop() 

    # Si se pulsa 'd' cambiar de color
    elif (cv2.waitKey(1) & 0xFF  == ord('d')):
      current_time = time.time()
      if current_time - last_d_key_press_time >= 1.1:
          current_color_index = (current_color_index +1) % len(color_names)
          last_d_key_press_time = current_time

    # Terminar si se pulsa 'q'
    elif cv2.waitKey(1) & 0xFF == ord('q'):
      break
```

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


## Demo

[!Video Demo(https://alumnosulpgc-my.sharepoint.com/:v:/g/personal/eduardo_etopa101_alu_ulpgc_es/EeKF5WvF-AdKmHbXfGxETRsBeieqbzq1_-vm-MMYwY4oiQ?e=RB7ztp&nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJTdHJlYW1XZWJBcHAiLCJyZWZlcnJhbFZpZXciOiJTaGFyZURpYWxvZy1MaW5rIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXcifX0%3D)](https://alumnosulpgc-my.sharepoint.com/:v:/g/personal/eduardo_etopa101_alu_ulpgc_es/EeKF5WvF-AdKmHbXfGxETRsBeieqbzq1_-vm-MMYwY4oiQ?e=RB7ztp&nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJTdHJlYW1XZWJBcHAiLCJyZWZlcnJhbFZpZXciOiJTaGFyZURpYWxvZy1MaW5rIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXcifX0%3D)
