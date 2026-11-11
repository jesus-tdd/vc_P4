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
> Por hacer


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
> Por hacer

## Entrega B
> Por hacer