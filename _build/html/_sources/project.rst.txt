=============
Projects Demo
=============

Breathing LED
-------------
LED on the TinyProgrammer board will breath in a constant pace.

Here we use DAC pin to output various voltages.

**breathing_led.c**

.. code-block:: c

    int i = 0;
    int dir = 1;
    void setup() {
        pinMode(LED_BUILTIN, OUTPUT);
    }

    void loop() {
        if (dir)
        {
            i++;
        } else {
            i--;
        }
        if (i >= 255) {
            dir = 0;
        } else if (i <= 150){
            dir = 1;
        }
        analogWrite(LED_BUILTIN, i);
        delay(10);
    }


Controlling NeoPixel String
---------------------------

.. image:: _static/neopixel.gif
    :width: 100%
    
Servo Control
-----------------

Motor Control
-------------

RTC Clock
---------


EEPROM
------

UART Communication with ESP8266
-------------------------------


Interfacing with OLED Display
-----------------------------

Interfacing with E-ink Display
------------------------------