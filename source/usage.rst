==============
Using TinyCore
==============

GPIO
----

Follow the Arduino syntax, GPIO can be controlled as output or input.

Configured as output::

    pinMode(pin, OUTPUT);           
    digitalWrite(pin, HIGH);       

You can check the pinout for reference:

    .. image:: _static/TinyCore16_Pinout.png

The Arduino Pin is marked as purple. To use a specific pin, for example::

    const DAC_PIN = 15;
    
    void setup()
    {
        pinMode(DAC_PIN, OUTPUT);
    }
    
    void loop() {
        digitalWrite(pin, HIGH);
        delay(1000);
        digitalWrite(pin, LOW);
        delay(1000);
    }
    
Reference: https://www.arduino.cc/en/Tutorial/DigitalPins

PWM
---
Same as Arduino, use analogWrite to generate PWM signals.

Reference: https://www.arduino.cc/en/Tutorial/PWM

ADC
---
There are 11 pins on TinyCore that support AnalogRead. 

Available pins are marked in green color from A0 to A11. Some of the pins are in the extended pin area.

Use analogRead(A0) to read values from pins. 

Reference: https://www.arduino.cc/reference/en/language/functions/analog-io/analogread/

DAC
---
TinyCore has 1 pin support real DAC output, which is pin 15.

(To do: Internal DAC reference)

Servo
-----
(To do)

I2C
---
Fully Implement Wire library. SCL/SDA pins are marked on the board which is pin 3 and pin 2.

Simple Reader::

    #include <Wire.h>

    void setup() {
        Wire.begin();        // join i2c bus (address optional for master)
        Serial.begin(9600);  // start serial for output
    }

    void loop() {
        Wire.requestFrom(8, 6);    // request 6 bytes from slave device #8

        while (Wire.available()) { // slave may send less than requested
            char c = Wire.read(); // receive a byte as character
            Serial.print(c);         // print the character
        }

        delay(500);
    }

Simple Writer::

    #include <Wire.h>

    void setup() {
        Wire.begin(); // join i2c bus (address optional for master)
    }

    byte x = 0;

    void loop() {
        Wire.beginTransmission(8); // transmit to device #8
        Wire.write("x is ");        // sends five bytes
        Wire.write(x);              // sends one byte
        Wire.endTransmission();    // stop transmitting

        x++;
        delay(500);
    }


receiveEvent and requestEvent are also supported.


UART
----
uart support up to 115200 baud rate.

To use uart, simply initialize it as::

    Serial.begin(9600);

Then you can send or receive data::

    Serial.println("test");
    Serial.write(0x0d)


SPI
---

EEPROM
------


