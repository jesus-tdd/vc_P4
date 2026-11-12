# Práctica 4. Detección de vehículos y matrículas


## Entrega A
El código presentado no está separado en las distintas partes de la entrega,
sino que se ha desarrollado cada una sobre la anterior de forma incremental. 


### Detectar vehículos y personas
Se usa el modelo `yolo11n.pt`, de YOLO.

En el bucle principal, se le indica al modelo las clases que debe detectar.
Se pueden modificar las clases a detectar simplemente
añadiéndolas o eliminándolas de la lista `CLASS_NAMES`.

Con el fin de facilitar la lectura del código,
se ha creado la función `getCocoIdx()`
para calcular el índice de las clases a detectar en el dataset Coco.


### Detectar matrículas
Para la detección de matrículas se ha usado un dataset de un curso anterior,
pero etiquetando de nuevo cada imagen.
El dataset etiquetado y dividido puede encontrarse 
[aquí](https://drive.google.com/drive/folders/1puQBX3HBJref2brm8q5jQ1OaYJQgEGHX?usp=sharing).

Por limitaciones técnicas, el modelo se ha entrenado en *collab notebooks*.
A continuación, se adjunta el código usado para el entrenamiento.

```py
import torch
from ultralytics import YOLO

model = YOLO("yolo11n.pt")

model.train(
    data="/content/drive/MyDrive/UNI/26-27/vc/license_plates/dataset/data.yaml",
    device=0,
    imgsz=416, batch=4,
    epochs=40
)
```

Los resultados del entrenamiento se adjuntan en la carpeta `weights`.
Para la detección de las matrículas se usará el modelo `best.pt`.

La detección de matrículas también se ha separado en una función auxiliar: `detect_plates()`.
Esta actúa de forma similar 
a la detección de objetos del grupo principal,
pero únicamente al detectarse un coche y únicamente en el área que ocupa dicho coche.


### Contar total de cada clase
El total de cada clase se cuenta en el bucle principal y se almacena en un diccionario
en el que la clave es el nombre de la clase y el valor la cantidad detectada de la misma.

Para no contar varias veces el mismo objeto,
se ha implementado seguimiento de objetos (`model.track()`).
De este modo, se puede guardar el identificador de cada objeto en el momento de su detección, y realizar un seguimiento de los identificadores detectados para no contarlos varias veces.

La lógica para llevar la cuenta de cada clase se encuentra en la función `countClass()`.
De nuevo, se ha creado esta función para facilitar la lectura del bucle principal.


### Vídeo para visualizar resultados
Los resultados se vuelcan en un vídeo 
usando la propia función de seguimiento `model.track()` 
con el parámetro `save=True`.

Tanto el vídeo original como el de los resultados
son demasiado pesados para su subida a GitHub.
Se puede ver el vídeo de los resultados en el siguiente enlace:
[vídeo pendiente]().

### CSV de los resultados del seguimiento
Para la creación del CSV,
para cada objeto detectado en cada frame del vídeo analizado
se escribe un campo del archivo.

Se ha separado la creación del campo a escribir
en la función `build_rows()` para facilitar la legibilidad del bucle principal. 


## Entrega B


### Leer matrículas
Esta tarea se ha implementado de forma incremental en el código de la entrega anterior.

El OCR elegido es `easyocr`.

Las matrículas se leen en el bucle principal, justo después de su detección.
Para esto, se usan las siguientes funciones auxiliares:

- `read_plate()`: se encarga de leer la matrícula.
Primero, extrae la *bounding box* de la matrícula
y luego la escala para aumentar su tamaño, de modo que la detección sea más precisa.
Finalmente, lee la matrícula y calcula la puntuación obtenida (media de la confianza por carácter leído).
Antes de retornar, llama a la función `fix_plate()` para corregir posibles errores con caracteres que no existan en matrículas en España.

- `fix_plate()`: corrige caracteres que no puedan existir en matrículas españolas.

La lectura de la matrícula se valida con la siguiente expresión regular:

```regex
^\d{4}[BCDFGHJKLMNPRSTVWXYZ]{3}$
```

Si la matrícula es válida,
esta solo se guarda en caso de no existir otra matrícula para el coche detectado
o siesta tiene una mayor puntuación que la ya almacenada.

Finalmente, el OCR se ejecutará únicamente cuando se detecte una matrícula nueva
o cuando pasen la cantidad de frames indicados en `OCR_EVERY`.
De este modo, se reduce la carga de trabajo y el tiempo de ejecución. 


### Comparación OCR vs VLM
> Por hacer