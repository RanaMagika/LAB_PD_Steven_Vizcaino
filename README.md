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





## Practica 2
El objetivo de la practica es comprender el funcionamiento de las interrupciones. Para lo cual realizaremos una practica donde controlaremos 2 leds de una forma periódica y una entrada ; de forma que el uso de la entrada provoque un cambio de frecuencia de las oscilaciones pero solo en un led
·¿Qué es una interrupción hardware?  

 A un nivel básico, una interrupción es una señal que interrumpe la actividad normal de nuestro microprocesador y salta a atenderla. Hay tres eventos que pueden disparar una interrupción:

-Un evento hardware, previamente definido  
-Un evento programado, o Timer  
-Una llamada por software  

 En el ESP32, podemos definir una función de rutina de servicio de interrupción que se llamará cuando un pin GPIO cambie el valor de su señal. Con una placa ESP32, todos los pines GPIO pueden ser configurados para funcionar como entradas de solicitud de interrupción.  
  
 En el IDE de Arduino, usamos una función llamada attachInterrupt() para establecer una interrupción en base a un pin por pin. La sintaxis recomendada es la siguiente.  
   attachInterrupt(GPIOPin, ISR, Mode);
### Practica A
Para este apartado se nos pide que hagamos un programa para manejar un boton y contar cuantas veces  es presionado, se generara una interrupción que incrementa un contador y registra el evento en la variable pressed. Además, después de un minuto, la interrupción se desactiva. Para armar el circuito necesitaremos lo siguiente...  
### Materiales:
-protoboard
-esp32-s3
-boton  
El circuito deberia quedar tal que asi:  
![image](https://github.com/user-attachments/assets/ba77938f-dc02-4db4-87c9-d504d80a7943)  

En el programa deberiamos de ver esta salida cuando le damos al boton: 
![image](https://github.com/user-attachments/assets/8044fc35-ebed-41ea-a701-56118b4fe148)  

### Conclusiones

- La interrupción responde inmediatamente a la presión del botón.

- El uso de attachInterrupt() permite definir fácilmente la función de respuesta.

- El programa finaliza la interrupción tras 60 segundos para evitar ejecuciones innecesarias.



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






 
 
 






