Steven Daniel Vizcaino Cedeño

# Laboratorio de Procesos digitales
·En este README explicare todas las practicas realizadas en el laboratorio durante el curso escolar 2024/2025 explicadas por mi profesor Lopez Palma Manuel.  

·En estas practicas utlizaremos el esp32-s3, el ESP32-S3 es un microcontrolador desarrollado por Espressif Systems, parte de la familia ESP32. Está diseñado para ofrecer un alto rendimiento en aplicaciones que requieren conectividad inalámbrica, procesamiento de señales y computación avanzada, todo en un solo chip. Con este microcontrolador aplicaremos lo aprendido en teoria a ejemplos practicos en el laboratorio. Empezemos!!!

## Practica 1
En esta practica el objetivo es producir el parpadeo periodico de un led. Se utilizara la salida serie para depurar el programa, esto quiere decir que vas a utilizar la comunicación serie (como el puerto UART en un microcontrolador) para enviar información desde tu dispositivo hacia una computadora o terminal. Esta salida sirve para mostrar mensajes de depuración, errores o información sobre el estado del programa mientras se ejecuta.
Para hacer esto tendremos que montar un circuito conectado al esp32-s3 donde necesitaremos de lo siguiente...
### Materiales:
- Protoboard
- cables de conexion
- luz led
- esp32-s3
- cable tipo c

 El montaje deberia quedar tal que asi:
 ![image](https://github.com/user-attachments/assets/9cd7c811-7743-47ef-8e95-d21cb53cf0dd)




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





 
 
 






