Steven Daniel Vizcaino Cedeño

# Laboratorio de Procesos digitales
·En este README explicare todas las practicas realizadas en el laboratorio durante el curso escolar 2024/2025 explicadas por mi profesor Lopez Palma Manuel.  

·En estas practicas utlizaremos el esp32-s3, el ESP32-S3 es un microcontrolador desarrollado por Espressif Systems, parte de la familia ESP32. Está diseñado para ofrecer un alto rendimiento en aplicaciones que requieren conectividad inalámbrica, procesamiento de señales y computación avanzada, todo en un solo chip. Con este microcontrolador aplicaremos lo aprendido en teoria a ejemplos practicos en el laboratorio. Empezemos!!!

## Practica 1: Blink
En esta practica el objetivo es producir el parpadeo periodico de un led. Se utilizara la salida serie para depurar el programa, esto quiere decir que vas a utilizar la comunicación serie (como el puerto UART en un microcontrolador) para enviar información desde tu dispositivo hacia una computadora o terminal. Esta salida sirve para mostrar mensajes de depuración, errores o información sobre el estado del programa mientras se ejecuta.

## Objetivos
- Configurar un pin del ESP32 como salida.
- Implementar un bucle infinito para el parpadeo del LED.
- Enviar mensajes por el puerto serie para depuración.
- Optimizar el control del LED utilizando acceso directo a los registros del ESP32.
- Determinar la máxima frecuencia de parpadeo sin usar `delay()`.

Para hacer esto tendremos que montar un circuito conectado al esp32-s3 donde necesitaremos de lo siguiente...
### Materiales:
- Protoboard
- cables de conexion
- luz led
- esp32-s3
- cable tipo c

 El montaje deberia quedar tal que asi:
 ![image](https://github.com/user-attachments/assets/9cd7c811-7743-47ef-8e95-d21cb53cf0dd)

  ## Desarrollo
### 1. Configuración inicial
Se configura un pin del ESP32 como salida para controlar el LED.

### 2. Modificación para enviar datos por el puerto serie
Se inicializa el puerto serie y se envían mensajes "ON" y "OFF" cada vez que cambia el estado del LED.

<img width="177" alt="image" src="https://github.com/user-attachments/assets/f254a317-8eac-47ce-89e8-c002ad8956e4" />  

### 3. Control directo de registros
Se accede directamente a los registros del ESP32 para modificar el estado del LED de manera más eficiente.

### 4. Eliminación de `delay()` y medición de frecuencia
Se reemplaza la función `delay()` por una técnica basada en temporizadores y se mide la máxima frecuencia de parpadeo con un osciloscopio en los siguientes escenarios:
- Con envío por puerto serie y usando funciones de Arduino.
- Con envío por puerto serie y acceso directo a registros.
- Sin envío por puerto serie y usando funciones de Arduino.
- Sin envío por puerto serie y acceso directo a registros.
![image](https://github.com/user-attachments/assets/753f7682-ca96-46de-bb9a-96a43484a9e0)
![image](https://github.com/user-attachments/assets/f383d063-db65-48d9-be51-ced3d59a3f16)
![image](https://github.com/user-attachments/assets/745b0ab1-6d6d-4d67-a341-d17b9c77e27f)
![image](https://github.com/user-attachments/assets/ee027b7c-c9ee-43a8-be04-be55a366db48)


## Resultados
- Se logró implementar el parpadeo del LED en diferentes configuraciones.
- Se observó que el acceso directo a registros mejora la eficiencia.
- Se determinó la máxima frecuencia de parpadeo medible con osciloscopio.

## Conclusiones
- El acceso directo a registros permite una mayor optimización en la manipulación del hardware.
- La comunicación serie introduce retardos que afectan el rendimiento.
- La eliminación de `delay()` permite alcanzar frecuencias más altas de parpadeo.
- El tiempo libre del procesador depende de la estrategia utilizada para alternar el estado del LED.

## Referencias
- [Documentación oficial de ESP32](https://docs.espressif.com/)
- [Uso de PlatformIO con ESP32](https://electropeak.com/learn/getting-started-with-platformio-ide-to-program-esp32/)






.  
 

 



# Práctica 2: Interrupciones

## Objetivo
El objetivo de la práctica es comprender el funcionamiento de las interrupciones. Para ello, realizaremos una práctica donde controlaremos dos LEDs de forma periódica y una entrada; el uso de la entrada provocará un cambio de frecuencia de las oscilaciones, pero solo en un LED.

---

## Introducción Teórica

### ¿Qué es una interrupción hardware?
A nivel básico, una interrupción es una señal que interrumpe la actividad normal del microprocesador y salta a atenderla. Hay tres eventos que pueden disparar una interrupción:

- Un evento hardware, previamente definido.
- Un evento programado, o Timer.
- Una llamada por software.

Cuando un evento dispara una interrupción, la ejecución normal del micro se suspende ordenadamente para poder volver, ejecutando una función especial llamada *Interrupt Service Routine (ISR)* o *Interrupt Service Handler (ISH)*. Cuando el ISR/ISH finaliza, el procesador vuelve al punto donde se detuvo y continúa con la ejecución normal.

El concepto de verificar periódicamente el estado de un pin o registro se llama *Polling*, mientras que el uso de interrupciones permite reaccionar a eventos sin necesidad de realizar comprobaciones constantes.

### Tipos de Interrupciones
1. **Evento hardware**
2. **Evento programado (Timer)**
3. **Llamada por software**

En el ESP32, podemos definir una rutina de servicio de interrupción para ejecutar código cuando un pin GPIO cambie de estado.

---

## Interrupciones en ESP32

### Adjuntar una interrupción a un GPIO
En el entorno de Arduino, se usa la función `attachInterrupt()` con la siguiente sintaxis:

```cpp
attachInterrupt(GPIOPin, ISR, Mode);
```

Los parámetros son:
- **GPIOPin**: Pin que generará la interrupción.
- **ISR**: Función que se ejecutará cuando se dispare la interrupción.
- **Mode**: Momento en que se disparará la interrupción, con opciones:
  - `LOW`: Cuando el pin está en bajo.
  - `HIGH`: Cuando el pin está en alto.
  - `CHANGE`: Cambio de estado (alto a bajo o viceversa).
  - `FALLING`: Paso de alto a bajo.
  - `RISING`: Paso de bajo a alto.

Para desactivar la interrupción, se usa `detachInterrupt(GPIOPin);`.

### Temporizadores en ESP32
El ESP32 cuenta con temporizadores internos que generan interrupciones cuando alcanzan un valor específico. Estos temporizadores tienen contadores de 64 bits y preescaladores de 16 bits.

Los temporizadores pueden:
- Contar hacia arriba o abajo.
- Apoyar la recarga automática.
- Generar alarmas cuando alcanzan un valor definido por software.

Para más información, se recomienda leer [este tutorial](https://techtutorialsx.com/2017/10/07/esp32-arduino-timer-interrupts/).

---

## Práctica A: Interrupción por GPIO

### Descripción
En esta práctica, se configurará un botón que generará una interrupción cuando sea presionado, incrementando un contador y mostrando el número de pulsaciones en el puerto serie.
### Materiales:
-protoboard
-esp32-s3
-boton  
El circuito deberia quedar tal que asi:  
![image](https://github.com/user-attachments/assets/ba77938f-dc02-4db4-87c9-d504d80a7943) 

### Informe de Funcionamiento
1. Se configura un botón en un pin GPIO con `INPUT_PULLUP`.
2. Se adjunta una interrupción en modo `FALLING`.
3. En la ISR, se incrementa un contador y se cambia un flag.
4. En `loop()`, se verifica el flag y se imprime la cantidad de veces que el botón fue presionado.
5. Después de un minuto, la interrupción se desactiva.

### Salidas Obtenidas
A través del puerto serie, se imprimen los siguientes mensajes cada vez que se presiona el botón:
```
Button 1 has been pressed X times
Interrupt Detached!
```
---
![image](https://github.com/user-attachments/assets/8044fc35-ebed-41ea-a701-56118b4fe148)  

## Práctica B: Interrupción por Timer

### Descripción
En esta práctica, se usará un temporizador para generar interrupciones periódicas e incrementar un contador de interrupciones.

### Informe de Funcionamiento
1. Se configura un temporizador con `timerBegin()`.
2. Se adjunta una interrupción con `timerAttachInterrupt()`.
3. Se establece una alarma con `timerAlarmWrite()`.
4. Se habilita la alarma con `timerAlarmEnable()`.
5. En cada interrupción, se incrementa un contador.
6. En `loop()`, si hay una interrupción, se muestra el total de interrupciones en el puerto serie.

### Salidas Obtenidas
El puerto serie imprimirá:
```
An interrupt has occurred. Total number: X
```
---

### Ejercicio para subir nota 
-	Teneis que  hacer que chatgpt  os genere un codigo  para esp32  arduino  
sugerencia de  prompt  :   quiero hacer un programa para arduino esp32 en el entorno de platformio ; que controle un led y dos pulsadores utilizando interrupciones de un timer de forma que el led parpadee a una fecurncia inicial y si pulsamos a un pulsador la frecuencia de parpadeo suba y si pulsamos a otro pulsador dicha frecuencia baje ; grrantiza de que el pulsador los pulsadores que se deben de leer en el timer se filtran para evitar rebotes

# **Descripción del funcionamiento del programa**

Este programa para ESP32, desarrollado en PlatformIO, controla un **LED y dos pulsadores** utilizando **interrupciones de un temporizador (timer)**.  
Su objetivo es hacer que el LED parpadee a una frecuencia inicial y que los pulsadores puedan **aumentar o disminuir** esta frecuencia.  
Además, el código incluye un mecanismo de **filtrado de rebotes** para evitar lecturas erróneas de los pulsadores.  

---

## **Funcionamiento detallado**

### **1. Configuración inicial**
- Se define un temporizador de hardware para generar interrupciones periódicas.  
- Se configuran los pines del LED y los pulsadores como salida y entrada, respectivamente.  
- Se activan las interrupciones del timer y de los pulsadores.  

### **2. Interrupciones del temporizador**
- Se ejecuta una función en cada interrupción del timer.  
- Esta función cambia el estado del LED (ON/OFF) según la frecuencia establecida.  

### **3. Interrupciones de los pulsadores**
- Cada pulsador tiene su propia interrupción.  
- Cuando se presiona el pulsador de aumento, la frecuencia de parpadeo del LED se incrementa.  
- Cuando se presiona el pulsador de disminución, la frecuencia se reduce.  
- Se implementa un **filtro de rebotes**, que evita lecturas falsas debido a la naturaleza mecánica de los pulsadores.  

### **4. Bucle principal (loop)**
- En el `loop()`, solo se manejan las variables modificadas por las interrupciones.  
- Se garantiza que los cambios de frecuencia sean efectivos y que el LED parpadee correctamente.  

---

## **Comprobación del funcionamiento**
Para probarlo, puedes:  
- Observar el parpadeo del LED y verificar si responde correctamente a los pulsadores.  
- Usar `Serial.print()` para mostrar en el monitor serie la frecuencia actual del parpadeo.  
- Ajustar la frecuencia mínima y máxima para evitar valores fuera de rango.  

Este enfoque es eficiente porque el ESP32 no se bloquea con `delay()`, ya que el control del tiempo se realiza mediante **interrupciones del temporizador**.  

###Fotos del montaje y proceso: 
![image](https://github.com/user-attachments/assets/39de810b-481b-47fd-bb88-363e952c4c65)
![image](https://github.com/user-attachments/assets/563c21d6-335a-49d0-922f-117e14e6ada8)


## Conclusiones
Las interrupciones en el ESP32 permiten reaccionar de manera eficiente a eventos sin necesidad de realizar comprobaciones constantes (*polling*). Su uso optimiza el rendimiento del microcontrolador y permite desarrollar aplicaciones más eficientes y organizadas.


# **Práctica 3: WiFi y Bluetooth en ESP32**

El objetivo de esta práctica es comprender el funcionamiento de **WiFi y Bluetooth** en el ESP32, mediante dos ejercicios:

1. **Creación de un servidor web en ESP32**  
2. **Comunicación Bluetooth con un móvil a través de puerto serie**  

## **1. Servidor Web con ESP32 (Modo STA)**  

### **Funcionamiento**
- El ESP32 se conecta a una red WiFi utilizando un **SSID** y una **contraseña**.
- Una vez conectado, obtiene una dirección IP dentro de la red.
- Se inicia un **servidor web** en el puerto 80.
- Al acceder a la dirección IP desde un navegador, el ESP32 responde con una **página HTML sencilla**.
- La página se genera directamente en el código, pero se debe modificar para incluir un archivo **HTML externo**.

### **Salida esperada en el monitor serie**
Al ejecutar el código, el monitor serie debería mostrar:  
Try Connecting to [SSID] ...... WiFi connected successfully Got IP: 192.168.X.X HTTP server started
· Cuando nos conectamos al mismo wifi y ponemos la ip en nuestro pc nos deberia de cargar la web echa por el ESP32-S3
![image](https://github.com/user-attachments/assets/021b54a5-4901-4d09-9d5d-46518dace209)
·Aqui la modificamos para que sea dinamica y con colores:
![image](https://github.com/user-attachments/assets/fd71036c-65e2-4d6a-9eb0-ef8ea165c4f2)



## **2. Comunicación Bluetooth con el móvil**  

### **Funcionamiento**
- Se utiliza **Bluetooth clásico** en modo **Serial**.  
- El ESP32 crea un dispositivo Bluetooth llamado `"ESP32test"`.  
- Un móvil puede emparejarse con el ESP32 y enviar datos a través de una aplicación de terminal Bluetooth.  
- Los datos enviados desde el móvil se mostrarán en el monitor serie del ESP32.  
- Si el ESP32 recibe datos a través de la conexión serie, los enviará de vuelta al móvil.  
# **Práctica de Bluetooth Low Energy (BLE) con ESP32-S3**

## **Objetivo**
Implementar una aplicación **Bluetooth Low Energy (BLE)** en un **ESP32-S3** usando **Arduino con PlatformIO** que simule un sensor de temperatura y permita la **lectura y escritura de datos**.

---

## **Requisitos de Hardware**
- **Placa ESP32-S3** (Ejemplo: ESP32-S3-DevKitC-1)
- **Cable USB** para programación
- **Computadora** con **PlatformIO** instalado
- **Dispositivo con capacidad BLE** (smartphone, tablet, etc.)

---

## **Configuración del Proyecto**
### **1. Crear un nuevo proyecto en PlatformIO**
- Selecciona **ESP32-S3** como placa.
- Selecciona **Arduino** como framework.

### **2. Configurar platformio.ini**
- Reemplaza el archivo `platformio.ini` con la configuración proporcionada.
- Incluye:
  - Configuración para **ESP32-S3**.
  - Velocidad del **monitor serial** a **115200**.
  - Inclusión de la biblioteca **NimBLE-Arduino** *(alternativa más ligera que la BLE estándar)*.

### **3. Copiar el código principal**
- Copia el código en el archivo `src/main.cpp`.

---

## **Funcionalidades Implementadas**
### **1. Configuración de BLE**
- Creación de un **servidor BLE**.
- Definición de un **servicio** con **UUID personalizado**.
- Configuración de una **característica** con propiedades de:
  - **Lectura**
  - **Escritura**
  - **Notificación**

### **2. Simulación de Sensor**
- **Generación de valores aleatorios de temperatura**.
- **Actualización periódica** *(cada 2 segundos)*.

### **3. Manejo de Eventos**
- **Detección de conexión** y **desconexión** de dispositivos.
- **Callbacks** para recibir y procesar datos enviados por el cliente.

---

## **Prueba del Proyecto**
### **1. Cargar y ejecutar el código**
- **Compila y carga** el código en el **ESP32-S3**.
- **Abre el monitor serial** para ver los mensajes de depuración.

### **2. Escanear y conectar al ESP32-S3**
- Usa una aplicación **BLE Scanner** en tu smartphone:
  - **nRF Connect** *(Android/iOS)*
  - **LightBlue** *(iOS)*
  - **BLE Scanner** *(Android)*

- En la aplicación:
  1. **Busca el dispositivo** `"ESP32-S3 BLE Device"`.
  2. **Conéctate** al ESP32-S3.
  3. **Encuentra el servicio** y la **característica**.
  4. **Lee los valores de temperatura**.
  5. **Escribe datos** para ver cómo se reciben en el ESP32-S3.
### Imagenes del proceso y resultados: 
![image](https://github.com/user-attachments/assets/35d5be55-604b-430c-aca9-3967b4bce8c9)
![image](https://github.com/user-attachments/assets/d0894669-50be-491d-baf2-296dcb8a7ec4)
![image](https://github.com/user-attachments/assets/f00cea7d-9a81-4e31-aaf2-417f99267f8d)

# Práctica 4: Sistemas Operativos en Tiempo Real

## Objetivo
El objetivo de esta práctica es comprender el funcionamiento de un sistema operativo en tiempo real. En este ejercicio, se generarán varias tareas y se analizará cómo se ejecutan dividiendo el tiempo de uso de la CPU.

---

## Introducción Teórica

### Multitarea en ESP32 con Arduino y FreeRTOS
El ESP32 es un microcontrolador potente y pequeño, ideal para dispositivos IoT. Aunque el entorno Arduino ejecuta todo su código en un solo núcleo, el uso de FreeRTOS en el ESP32 permite programar tareas en ambos núcleos, optimizando el rendimiento. FreeRTOS permite ejecutar múltiples tareas, donde cada una puede tener diferentes prioridades, ayudando a gestionar el tiempo de CPU de manera eficiente.

### Creando Tareas
Para crear una tarea en FreeRTOS, debes:
1. **Definir una función** que contenga el código que se ejecutará en la tarea.
2. **Crear la tarea** con la función `xTaskCreate()`.

**Ejemplo básico:**
- Un LED parpadeando. La tarea se ejecuta en un bucle infinito, y se utiliza `vTaskDelay()` para pausar la tarea entre cada cambio de estado del LED.

### Tareas Únicas
También es posible crear tareas que se ejecutan solo una vez. Al finalizar la tarea, se debe llamar a `vTaskDelete()` para liberar la tarea y evitar que el sistema la ejecute de nuevo.

### Elegir el Núcleo de Ejecución
FreeRTOS permite especificar en qué núcleo (de los dos disponibles en el ESP32) se ejecutará una tarea utilizando `xTaskCreatePinnedToCore()`. Esto es útil para optimizar el rendimiento y distribuir las tareas entre ambos núcleos.

### Detener Tareas
Para detener una tarea, se puede usar `vTaskDelete()` dentro de la misma tarea o desde otra tarea, utilizando un identificador de tarea (TaskHandle).

### Prioridad de las Tareas
Las tareas en FreeRTOS tienen prioridades, y el sistema ejecuta primero las tareas de mayor prioridad. El rango de prioridades va de 0 a 24, donde un número más alto indica mayor prioridad. Si dos tareas tienen la misma prioridad, FreeRTOS las ejecutará de forma compartida.

---

## Ejercicio Práctico 1

Este ejercicio tiene como objetivo demostrar cómo FreeRTOS maneja la ejecución de múltiples tareas en un ESP32. Se crean dos procesos concurrentes que imprimen mensajes en el puerto serie a intervalos de un segundo. A continuación, se desglosa su funcionamiento:  
1. Inicialización:

Serial.begin(112500): Configura la comunicación serial a 112500 baudios.  

xTaskCreate(): Crea una nueva tarea llamada anotherTask. Esta tarea se ejecutará en paralelo con el loop principal de Arduino.  

2. Bucle Principal (loop):
Cada segundo (delay(1000)), imprime "this is ESP32 Task" en el puerto serie.  
3. Tarea Secundaria (anotherTask):

Se ejecuta en un bucle infinito (for(;;)), imprimiendo "this is another Task" cada segundo.

El delay(1000) permite que el planificador de FreeRTOS le ceda tiempo de ejecución a otras tareas.  

La última línea vTaskDelete(NULL); nunca se ejecutará porque el bucle no termina.
Explicación del Funcionamiento
-  Ejecución del Código
El ESP32 ejecuta dos tareas en paralelo:

1. Tarea Principal (loop): Imprime "this is ESP32 Task" cada segundo.
2. Tarea Secundaria (anotherTask): Imprime "this is another Task" cada segundo.  
-¿Por qué ocurre esto?

Planificación de tareas: FreeRTOS permite que ambas tareas compartan el tiempo de CPU.

Prioridad: Ambas tareas tienen la misma prioridad (1), por lo que el planificador distribuye equitativamente el tiempo de ejecución.

Alternancia en la ejecución: Al usar delay(1000), cada tarea se "duerme" por un segundo, permitiendo que la otra tarea se ejecute.

## 📌 **Explicación del Ejercicio Práctico 2**

En este ejercicio se busca implementar dos tareas que trabajen de manera sincronizada para encender y apagar un LED usando **semáforos** en FreeRTOS.

---

### **🔹 Conceptos Claves**

Antes de analizar el código, es importante comprender algunos conceptos:

#### **1️  ¿Qué es un Semáforo en FreeRTOS?**
Un **semáforo** en FreeRTOS es un mecanismo de sincronización que permite coordinar el acceso a un recurso compartido (en este caso, el LED).

#### **2️2 ¿Por qué usar un Semáforo?**
Si ambas tareas intentan encender y apagar el LED sin coordinación, podrían ejecutarse simultáneamente, causando un comportamiento errático. El semáforo asegura que solo una tarea controle el LED a la vez.

---

### **🔹 Explicación del Funcionamiento**

1  **Creación de las tareas:**  
   Se crean dos tareas:
   - **Tarea 1:** Enciende el LED.
   - **Tarea 2:** Apaga el LED.

2️ **Uso del semáforo:**  
   Para sincronizar las tareas, se utiliza un semáforo binario. Este semáforo tiene dos estados posibles: `xSemaphoreTake` (espera hasta que el semáforo esté disponible) y `xSemaphoreGive` (libera el semáforo para que otra tarea lo pueda usar).

3️  **Funcionamiento de las tareas:**  
   - **Tarea 1 (Encender LED):** Esta tarea se ejecuta y espera hasta que el semáforo esté disponible. Luego enciende el LED y libera el semáforo, permitiendo que la otra tarea lo apague.
   - **Tarea 2 (Apagar LED):** Después de recibir el semáforo, apaga el LED y libera el semáforo para que la tarea 1 pueda volver a encender el LED.

4️  **Sincronización:**  
   Las tareas están sincronizadas gracias al semáforo, garantizando que solo una de ellas acceda al LED a la vez.

---
### Fotos del proceso: 
![image](https://github.com/user-attachments/assets/27a13428-cf4c-4f09-a84c-bb2ae5bb4748)
![image](https://github.com/user-attachments/assets/34529a09-c2f6-46bd-9de7-b13dd5c1fd51)


### Conclusiones
- Este ejercicio es una buena introducción a cómo trabajar con semáforos en FreeRTOS para sincronizar tareas.
- El semáforo asegura que las tareas no interfieran entre sí, lo que es fundamental en aplicaciones multitarea.


# Práctica 6: Buses de Comunicación II (SPI)

## Introducción
En esta práctica se estudia el funcionamiento del bus SPI (Serial Peripheral Interface), un protocolo de comunicación síncrono ampliamente utilizado en sistemas embebidos. Se realizarán ejercicios prácticos con una tarjeta SD y un lector RFID para comprender mejor su aplicación.

## Objetivos
- Comprender el protocolo SPI y su funcionamiento.
- Aprender a utilizar SPI en el ESP32-S3.
- Realizar lectura y escritura en una memoria SD mediante SPI.
- Leer etiquetas RFID utilizando SPI.
- Implementar un sistema de almacenamiento de lecturas RFID en una tarjeta SD.
- (Ejercicio de subida de nota) Mostrar la información en una página web.

## Materiales
- ESP32-S3
- Módulo lector de tarjetas SD
- Módulo lector RFID RC522
- Cables de conexión
- Tarjeta SD
- Etiquetas RFID

## Desarrollo
### 1. Configuración del Bus SPI en ESP32-S3
ESP32-S3 dispone de múltiples buses SPI, utilizaremos SPI2 para la conexión de los periféricos. La conexión de los dispositivos se realizó de la siguiente manera:

| Función | Pin SD | Pin RFID |
|---------|-------|---------|
| MOSI    | GPIO35 | GPIO11  |
| MISO    | GPIO37 | GPIO13  |
| SCK     | GPIO36 | GPIO12  |
| CS      | GPIO39 | GPIO10  |

### 2. Ejercicio 1: Lectura y escritura en memoria SD
Se desarrolló un programa que inicializa la tarjeta SD y permite la lectura de archivos almacenados en ella. Se verificó su correcto funcionamiento mediante la lectura de un archivo `archivo.txt`.  
### Montaje de los componentes:
![image](https://github.com/user-attachments/assets/4d85af68-683a-46d4-aef2-abb3cca2deab)
### Lo que imprime la consola:  
![image](https://github.com/user-attachments/assets/66866dc2-be0c-4612-981e-a905c18b1099)


### 3. Ejercicio 2: Lectura de etiquetas RFID
Se implementó un programa que permite la detección de tarjetas RFID y la lectura de su UID. La información obtenida se muestra en el monitor serie.

### Montaje  y uso de los componentes:  
![image](https://github.com/user-attachments/assets/34d458cf-16fb-4bdb-8d30-09b0ce60c1df)
![image](https://github.com/user-attachments/assets/82c64dac-4e29-48e5-8e7a-a195150cc54f)
### Lo que imprime la consola: 
![image](https://github.com/user-attachments/assets/9982ed93-30ab-4cb1-a3e7-1b4a3dd2a684)



## Resultados
- Se logró la comunicación con la tarjeta SD y el lector RFID.
- Se almacenaron correctamente las lecturas en la memoria SD.
- Se implementó una página web para visualizar los datos.

## Conclusiones
- SPI es un protocolo eficiente y fácil de implementar en ESP32.
- La gestión de múltiples dispositivos SPI requiere control adecuado de la señal CS.
- La integración con almacenamiento y redes permite aplicaciones avanzadas.

## Referencias
- [ESP32 SPI Documentation](https://docs.espressif.com/)
- [Arduino SPI Library](https://www.arduino.cc/en/reference/SPI)
- [Tutorial RFID](https://naylampmechatronics.com/blog/22_tutorial-modulo-lector-rfid-rc522.html)






 
 
 






