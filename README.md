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






 
 
 






