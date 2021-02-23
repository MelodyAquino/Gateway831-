# Gateway831-

1) Obtenga la placa raspberry pi 3 B+ y obtenga una tarjeta micro sd de 8 gb lista con el software raspbian.Sobre cómo actualizar el sistema operativo en la tarjeta SD, siga las instrucciones aquí: https://www.raspberrypi.org/learning/hardware-guide/
NOTA: Para activar el protocolo ssh debe crear en la SD un archivo sin extension con el nombre : ssh ( puede hacerlo con cualquier herramienta de texto y borrar la extension)
En este paso puede tambien configurar su conexion wifi en Rpi (vea el paso 6). 

2) Conecte la raspberry pi 3 B+ a la fuente de alimentación de 5[V] 2 [A]. ESTO ES MUY IMPORTANTE. El módulo lora puede generar un pico de 700 mA durante las transacciones inalámbricas activas y, por lo tanto, tiene un buen bloque de alimentación para alimentar la raspberry pi 3 B+.

3) Detalles de conexión: 

-RAK 831: 
 Antes incluso de encender la placa, obtenga las antenas del  kit y conéctelas al terminal de tornillo de la antena. ESTO ES ESENCIAL.
Diseño de los pines de RAK 831, diríjase a: https://media.digikey.com/pdf/Data%20Sheets/Pi%20Supply%20PDFs/PIS-0995_Web.pdf

![image](https://user-images.githubusercontent.com/72763026/108891115-154d0480-75ed-11eb-9809-ea56c1b8425e.png)


-Raspberry PI 3 B+ :

Diseño de los pines de Raspberry Pi, diríjase a: https://www.raspberrypi.org/documentation/usage/gpio/

![image](https://user-images.githubusercontent.com/72763026/108891467-82f93080-75ed-11eb-965b-7c67c57a16a0.png)


Tabla de conexión de pines módulo RAK831 con la raspberry pi 3 B+:
--------------------------------------------------------------------
 
Descripción                  Pin RAK 831                Pin RPI 
 
5V                             1                          2
GND                            3                          6
RST( reset pin)                19                         11/GPIO17
SCK( spi clock)                18                         23
MISO                           17                         21
MOSI                           16                         19
CSN( chip select)              15                         24

--------------------------------------------------------------------

4) Habilitar SPI:
Correr  sudo raspi-config.
Utilice la flecha hacia abajo para seleccionar 7 Interfacig  Options
Flecha hacia abajo hasta P4 SPI.
Seleccione yes cuando le pida que habilite SPI ,
También seleccione 'yes' cuando pregunte acerca de la carga automática del módulo del kernel.
Utilice la flecha derecha para seleccionar el <Finish> botón.
Seleccione 'yes' cuando le pida reiniciar


5)  Instalar el Git: 
Correr: 
Sudo apt-get update
Sudo apt-get upgrade
Sudo apt-get install git


6) Administrar la conexión wifi en la raspberry pi

Opcion 1:
-

Configure las credenciales de wifi : https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md

Correr : 
 $ sudo nano /etc/wpa_supplicant/wpa_supplicant.conf 
 
Y agregue el siguiente bloque al final del archivo, reemplazando el SSID y la contraseña para que coincidan con su red:

network={
ssid="The_SSID_of_your_wifi"
psk="Your_wifi_password"
}
$ sudo nano /etc/wpa_supplicant/wpa_supplicant.conf 
 
Opcion 2:
-
Cree un archivo de texto con el nombre  wpa_supplicant.conf  en la SD con el siguiente bloque: 

network={
ssid="The_SSID_of_your_wifi"
psk="Your_wifi_password"
}
 
7) Clona el instalador e inicia la instalación 

$ git clone https://github.com/RAKWireless/RAK2245-LoRaGateway-RPi-Raspbian-OS.git ~/RAK2245-LoRaGatewa$
$ cd ~/RAK2245-LoRaGateway-RPi-Raspbian-OS
$ sudo ./install.sh



La configuración de la selección de red para la tarjeta SIM, se refiere a /RAK2245-LoRaGateway-RPi-Raspbian-OS/rak_ppp/at.txt

 A continuación, verá algunos mensajes de la siguiente manera. Simplemente presione la tecla Enter para mantener el valor predeterminado o ingrese su información
 
      Nombre de host [rak-gateway]:
      Latitud [0]:
      Longitud [0]:
      Altitud [0]:
 

Nota:
-
 El paso de instalación le preguntará si desea habilitar la configuración remota. Escriba 'n' o 'no' y continúe con la instalación. Al comienzo de la instalación de la línea de comandos, el script le mostrará la puerta de enlace EUI, que es importante para los siguientes pasos.

Guardar el EUI: Ejemplo eui: B827EBFFFE7367F6 para cuando lo registre el gateway en TTN
-
Si olvida este paso puede probar con la direccion MAC de su modulo RAK

9)  ¡Ahora tiene una puerta de enlace en ejecución después de reiniciar el RAK!

Reinicio del RAK: 
crear en un archivo .c    (RAK_reset.c)

#include <unistd.h>
#include <wiringPi.h>
#define GPIO_RESET_PIN 0 // see wiringPi mapping !
int main() {
wiringPiSetup();
pinMode(GPIO_RESET_PIN, OUTPUT);
digitalWrite(GPIO_RESET_PIN, HIGH);
sleep(5);
digitalWrite(GPIO_RESET_PIN, LOW);
}

 -Instalar WiringPi si no se hizo aún.
 Para obtener WiringPi usando GIT:
 $ sudo apt-get install wiringpi
 -Compilar y ejecutar el archivo .c
Corra :
gcc -Wall -o RAK_reset RAK_reset.c -lwiringPi./RAK_reset
 
10)  Puede utilizar el comando "gateway-config" para configurar otras bandas.


11) Hay que modificar el local_conf.json y global_config.json; con los adecuados planes de
frecuencia, el EUI del gateway, IP del server, entre otros. 


Registro en EN TTN 
------------------
REGISTRARSE EN TTN NORMALMENTE PERO NO UTILIZAR LA CONSOLA DE https://www.thethingsnetwork.org/ 
El  router que se utiliza es el  mallado  de Australia para una frecuencia  915 MHz 
Para rellenar  el formulario de configuraciones  colocar la siguiente URL :  https://console.thethings.meshed.com.au que es la consola que se comunica directamente con el meshed-router. 

Nota: cuando iniciamos sesión nuevamente en ambas consolas aparecerán los gateways y aplicaciones que hayamos realizado; se recomienda trabajar con la consola https://console.thethings.meshed.com.au

Registro de puerta de enlace
-

1) Utilizamos el EUI de la instalación como ID del Gateway 
2) Seleccionar 
              I'm using the legacy packet forwarder
PARAMETROS ESTABLECIDOS PARA ARGENTINA https://www.thethingsnetwork.org/country/argentina (VERIFICAR SIEMPRE LA ACTUALIZACION)

------------------------------------------------------------------------------------- 
PARÁMETROS                                                DETALLE/RANGOS  


Región                                                   Argentina 
Denominación                                             AU915
Regulación                                               ENACOM
Restricción  TX                                          400 ms (opcional) 
Tamaño de carga útil (Payload)                           11 a 242 bytes 
Spreadings Factors                                       SF7 a SF10
Velocidad de datos                                       0.976 kbps a 12,5 kbps 
Potencia Máxima TX                                       +30 dBm

---------------------------------------------------------------------------------------

Nota : Pruebas : Antenna placement indoor, después cambiar a outdoor para pruebas en exterior  





      
 
                 












