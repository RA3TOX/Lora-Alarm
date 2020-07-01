# Lora-Alarm
In this article, we will expand the capabilities of these wonderful modules and add some useful features to them:
Temperature and humidity sensor
Tamper sensor (button)
LED in the transmitter to indicate the sending of a packet
Sound sensor in the receiver to notify the signal about the reception of the packet
           With this set of functions, it is already possible to assemble an existing system for controlling temperature and humidity in a greenhouse or a security system, for example, for a garage. Data on temperature and humidity, as well as the state of the tamper button are transmitted every 5 seconds (can be changed in the sketch).

Transmission part
           I previously said that the transmitter does not require large computational resources and therefore it can be performed directly on the Atmega8 microcontroller. In this case, you can exclude the quartz resonator and apply the internal synchronization of the microcontroller.

           LoRa module transmitter circuit for Atmega8.

LoRa_Atm8_cx2a.jpg

           In the diagram, we see the newly introduced elements. This is a DHT11 temperature and humidity sensor, a Kn button and an LED. The Kn button is an intrusion detector for the security system. When it is closed, an object opening signal is transmitted, which is fixed by the receiver. The LED flashes during the transmission of the packet, which indicates the operation of the security system (similar to car alarms). When installing a blue LED, a 100 ohm limiting resistor can not be set, since its operating voltage is 3 volts.
           Hex file for 868 MHz transmitter: LoRaSender_868_Alarm_Temp.hex
           433 MHz transmitter hex file: LoRaSender_433_Alarm_Temp.hex

          Fuses for Atmega8 with internal 8 MHz clock in the AVRDUDE_PROG program:

fuze_atm8_int_osc-8mhz.jpg

           For those wishing to execute the transmitter on the Arduino (Nano) module, the circuit has the following form:

arduino-nano_lora-per.jpg

           When using Arduino, it is necessary to install a stabilizer at 3.3V to power the LoRa module. There is a 3.3V contact on the Arduino Nano module and there are circuits on the Internet where power is taken from this pin. But when I measured the voltage on it, it turned out to be 4 V (apparently, just 5 V is quenched by a diode).
           For Arduino Nano, the outputs of the AMS1117-3.3 stabilizer just coincide in the contact pitch (5V / GND), so we solder it directly to the module contacts:

Arduino-nano_lora-3v.jpg

           The transmitter software is written in such a way that when the tamper sensor is in normal condition, the word "OK" is transmitted at the beginning of the transmission packet. If the alarm contacts are closed, the word "Alarm" is transmitted at the beginning of the packet. The receiver recognizes these words and when the alarm is triggered, displays the message “Alarm” on the screen, and the SOS signal is sent by telegraph to the “beep”.

           Sketch for a transmitter made on the Arduino platform: LoRaSender_Alarm_Temp.ino

           In addition to the LoRa module library, the sketch also contains an external library for the DHT11 temperature sensor, so do not forget to install it before compiling the program.

Receiving part
zummer.jpg In the receiver, I added a squeaker that emits a sound when receiving a packet. This came in handy when determining the coverage area of ​​the transmitter while away from it. As soon as the receiver stopped emitting a sound signal, it means that you have left the transmitter coverage area. You do not need to constantly look at the monitor screen to monitor the signal loss. A small trifle - but nice.

           An active buzzer that sounds when a constant voltage is applied to it is used as a “tweeter”. It is better to connect it through the toggle switch so that you can turn it off if necessary. Instead of a buzzer, you can put an LED that will light up briefly when a packet is received.

           Arduino Nano LCD Receiver Schematic:

arduino-nano_lora-lcd.jpg

           Here such information is displayed on the screen when working with the above transmitter:

lora-lcd.jpg

          Instead of an LCD display, a more compact OLED display can be used. The receiver circuit is somewhat simplified, since the number of display connection pins is reduced.

           Arduino receiver circuit with OLED display differs only in fewer connections to the display:



          The received information on the OLED display looks like this:



Download receiver code:

For circuit with LCD display - LoRaReceiver_868_lcd.ino
For a circuit with an OLED display - Lora_rx_433_oled.ino

           As I said above, the receiver program recognizes the word “Alarm” when an alarm is triggered and displays the message “Alarm”, and the SOS signal is sent by telegraph to the “beep”. If you have experience programming Arduino modules, you can change the parameters of the displayed alarm. For example, you can use another port and connect an executive relay to it (of course, through a switching transistor) with output to a more powerful siren or light projector. It all depends on your imagination.

           Let me remind you that receiver sketches except the LoR module library
