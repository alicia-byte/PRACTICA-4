# Informe Práctica 4
## Ejercicio Practico 1

###  Descripción de la salida por el puerto serie
Cuando se ejecuta el código  se puede observar por el monitor serial una serie de mensajes con la siguiente estructura:
```cpp
this is ESP32 Task
this is another Task
this is ESP32 Task
this is another Task
...
```
Los dos mensajes se imprimen con la misma frecuencia. Esto es porque tanto la función `loop()` como la tarea creada (`anotherTask`) tienen instrucciones para imprimir un mensaje y esperar 1000 milisegundos antes de repetir la función.

Debido a que las dos tareas (la del `loop()` y la de `anotherTask`) se ejecutan al mismo tiempo, cada una imprime su propio mensaje por el monitor serie. Como las dos esperan un segundo antes de repetir, los mensajes van apareciendo uno tras otro, de forma intercalada.

Aunque lo normal es que se vea un mensaje y luego el otro, el orden exacto en que aparecen puede cambiar , porque el sistema operativo decide cuál tarea se ejecuta primero en cada momento.

###  Explicación del funcionamiento
El código está echo para ejecutar  dos tareas concurrentes en la  ESP32-S3.

#### Funcionamiento General del Código
-   **En la función `setup()`:**
    
    -   Se inicia la comunicación serial para enviar mensajes al monitor (a 115200 baudios).
        
    -   Se crea una segunda tarea llamada `anotherTask` con la función `xTaskCreate()`. Esta tarea se ejecutará junto a la principal, sin interrumpirse entre sí.
        
-   **En la función `loop()`:**
    
    -   Se imprime el mensaje `"this is ESP32 Task"` cada segundo usando `delay(1000)`, que pausa la ejecución durante un segundo.
        
-   **En la función `anotherTask()`:**
    
    -   Se imprime el mensaje `"this is another Task"` también cada segundo, pero usando `vTaskDelay()`, una forma más eficiente que `delay()` porque deja que el procesador pueda hacer otras cosas mientras espera.

## Ejercicio Practico 2

###  Descripción de la salida por el puerto serie
Cuando se ejecuta el programa, el ESP32 muestra en el monitor serie dos mensajes que se repiten todo el tiempo:

```cpp
LED ENCENDIDO  
LED APAGADO  
LED ENCENDIDO  
LED APAGADO  
...
```
Cada mensaje aparece cada segundo, uno después del otro. Primero el LED se enciende, se muestra el mensaje, luego se apaga, y se muestra el siguiente mensaje. Este ciclo se repite constantemente.

###  Explicación del funcionamiento

Este código hace que el ESP32 **encienda y apague un LED** cada segundo, usando dos tareas diferentes que se turnan. Para que una tarea no interrumpa a la otra, usamos **semáforos**, que son como permisos para que cada tarea sepa cuándo le toca actuar.

#### Funcionamiento General del Código


En el bloque `setup()`:

- Se configura el puerto serie y el pin del LED para que sea una salida.
- Se crean dos semáforos: uno para encender el LED y otro para apagarlo.
- Se le da permiso a la tarea de encender el LED al principio.
- Después, se crean dos tareas: una para encender el LED y otra para apagarlo.

---

En la tarea que **enciende el LED**:

- La tarea espera su turno para ejecutarse.
- Enciende el LED.
- Muestra el mensaje `"LED ENCENDIDO"`.
- Espera 1 segundo.
- Le da permiso a la tarea que apaga el LED.

---

En la tarea que **apaga el LED**:

- Espera su turno para ejecutarse.
- Apaga el LED.
- Muestra el mensaje `"LED APAGADO"`.
- Espera 1 segundo.
- Le da permiso a la tarea que enciende el LED, para que lo vuelva a encender.

