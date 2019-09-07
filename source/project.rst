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

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="100%" height="315" src="https://www.youtube.com/embed/t7WcHrdrQLk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>


Controlling NeoPixel String
---------------------------

You need to first install Adafruit NeoPixel Library.

.. code-block:: c

    #include <Adafruit_NeoPixel.h>
    #ifdef __AVR__
    #include <avr/power.h>
    #endif

    #define PIN 12

    #define NUM_LEDS 30

    #define BRIGHTNESS 50

    Adafruit_NeoPixel strip = Adafruit_NeoPixel(NUM_LEDS, PIN, NEO_GRBW + NEO_KHZ800);

    byte neopix_gamma[] = {
        0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  0,
        0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  0,  1,  1,  1,  1,
        1,  1,  1,  1,  1,  1,  1,  1,  1,  2,  2,  2,  2,  2,  2,  2,
        2,  3,  3,  3,  3,  3,  3,  3,  4,  4,  4,  4,  4,  5,  5,  5,
        5,  6,  6,  6,  6,  7,  7,  7,  7,  8,  8,  8,  9,  9,  9, 10,
    10, 10, 11, 11, 11, 12, 12, 13, 13, 13, 14, 14, 15, 15, 16, 16,
    17, 17, 18, 18, 19, 19, 20, 20, 21, 21, 22, 22, 23, 24, 24, 25,
    25, 26, 27, 27, 28, 29, 29, 30, 31, 32, 32, 33, 34, 35, 35, 36,
    37, 38, 39, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 50,
    51, 52, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 66, 67, 68,
    69, 70, 72, 73, 74, 75, 77, 78, 79, 81, 82, 83, 85, 86, 87, 89,
    90, 92, 93, 95, 96, 98, 99,101,102,104,105,107,109,110,112,114,
    115,117,119,120,122,124,126,127,129,131,133,135,137,138,140,142,
    144,146,148,150,152,154,156,158,160,162,164,167,169,171,173,175,
    177,180,182,184,186,189,191,193,196,198,200,203,205,208,210,213,
    215,218,220,223,225,228,231,233,236,239,241,244,247,249,252,255 };


    void setup() {
    // This is for Trinket 5V 16MHz, you can remove these three lines if you are not using a Trinket
    #if defined (__AVR_ATtiny85__)
        if (F_CPU == 16000000) clock_prescale_set(clock_div_1);
    #endif
    // End of trinket special code
    strip.setBrightness(BRIGHTNESS);
    strip.begin();
    strip.show(); // Initialize all pixels to 'off'
    }

    void loop() {
    // Some example procedures showing how to display to the pixels:
    colorWipe(strip.Color(255, 0, 0), 50); // Red
    colorWipe(strip.Color(0, 255, 0), 50); // Green
    colorWipe(strip.Color(0, 0, 255), 50); // Blue
    colorWipe(strip.Color(0, 0, 0, 255), 50); // White

    whiteOverRainbow(20,75,5);  

    pulseWhite(5); 

    // fullWhite();
    // delay(2000);

    rainbowFade2White(3,3,1);


    }

    // Fill the dots one after the other with a color
    void colorWipe(uint32_t c, uint8_t wait) {
    for(uint16_t i=0; i<strip.numPixels(); i++) {
        strip.setPixelColor(i, c);
        strip.show();
        delay(wait);
    }
    }

    void pulseWhite(uint8_t wait) {
    for(int j = 0; j < 256 ; j++){
        for(uint16_t i=0; i<strip.numPixels(); i++) {
            strip.setPixelColor(i, strip.Color(0,0,0, neopix_gamma[j] ) );
            }
            delay(wait);
            strip.show();
        }

    for(int j = 255; j >= 0 ; j--){
        for(uint16_t i=0; i<strip.numPixels(); i++) {
            strip.setPixelColor(i, strip.Color(0,0,0, neopix_gamma[j] ) );
            }
            delay(wait);
            strip.show();
        }
    }


    void rainbowFade2White(uint8_t wait, int rainbowLoops, int whiteLoops) {
    float fadeMax = 100.0;
    int fadeVal = 0;
    uint32_t wheelVal;
    int redVal, greenVal, blueVal;

    for(int k = 0 ; k < rainbowLoops ; k ++){
        
        for(int j=0; j<256; j++) { // 5 cycles of all colors on wheel

        for(int i=0; i< strip.numPixels(); i++) {

            wheelVal = Wheel(((i * 256 / strip.numPixels()) + j) & 255);

            redVal = red(wheelVal) * float(fadeVal/fadeMax);
            greenVal = green(wheelVal) * float(fadeVal/fadeMax);
            blueVal = blue(wheelVal) * float(fadeVal/fadeMax);

            strip.setPixelColor( i, strip.Color( redVal, greenVal, blueVal ) );

        }

        //First loop, fade in!
        if(k == 0 && fadeVal < fadeMax-1) {
            fadeVal++;
        }

        //Last loop, fade out!
        else if(k == rainbowLoops - 1 && j > 255 - fadeMax ){
            fadeVal--;
        }

            strip.show();
            delay(wait);
        }
    
    }



    delay(500);


    for(int k = 0 ; k < whiteLoops ; k ++){

        for(int j = 0; j < 256 ; j++){

            for(uint16_t i=0; i < strip.numPixels(); i++) {
                strip.setPixelColor(i, strip.Color(0,0,0, neopix_gamma[j] ) );
            }
            strip.show();
            }

            delay(2000);
        for(int j = 255; j >= 0 ; j--){

            for(uint16_t i=0; i < strip.numPixels(); i++) {
                strip.setPixelColor(i, strip.Color(0,0,0, neopix_gamma[j] ) );
            }
            strip.show();
            }
    }

    delay(500);


    }

    void whiteOverRainbow(uint8_t wait, uint8_t whiteSpeed, uint8_t whiteLength ) {
    
    if(whiteLength >= strip.numPixels()) whiteLength = strip.numPixels() - 1;

    int head = whiteLength - 1;
    int tail = 0;

    int loops = 3;
    int loopNum = 0;

    static unsigned long lastTime = 0;


    while(true){
        for(int j=0; j<256; j++) {
        for(uint16_t i=0; i<strip.numPixels(); i++) {
            if((i >= tail && i <= head) || (tail > head && i >= tail) || (tail > head && i <= head) ){
            strip.setPixelColor(i, strip.Color(0,0,0, 255 ) );
            }
            else{
            strip.setPixelColor(i, Wheel(((i * 256 / strip.numPixels()) + j) & 255));
            }
            
        }

        if(millis() - lastTime > whiteSpeed) {
            head++;
            tail++;
            if(head == strip.numPixels()){
            loopNum++;
            }
            lastTime = millis();
        }

        if(loopNum == loops) return;
        
        head%=strip.numPixels();
        tail%=strip.numPixels();
            strip.show();
            delay(wait);
        }
    }
    
    }
    void fullWhite() {
    
        for(uint16_t i=0; i<strip.numPixels(); i++) {
            strip.setPixelColor(i, strip.Color(0,0,0, 255 ) );
        }
        strip.show();
    }


    // Slightly different, this makes the rainbow equally distributed throughout
    void rainbowCycle(uint8_t wait) {
    uint16_t i, j;

    for(j=0; j<256 * 5; j++) { // 5 cycles of all colors on wheel
        for(i=0; i< strip.numPixels(); i++) {
        strip.setPixelColor(i, Wheel(((i * 256 / strip.numPixels()) + j) & 255));
        }
        strip.show();
        delay(wait);
    }
    }

    void rainbow(uint8_t wait) {
    uint16_t i, j;

    for(j=0; j<256; j++) {
        for(i=0; i<strip.numPixels(); i++) {
        strip.setPixelColor(i, Wheel((i+j) & 255));
        }
        strip.show();
        delay(wait);
    }
    }

    // Input a value 0 to 255 to get a color value.
    // The colours are a transition r - g - b - back to r.
    uint32_t Wheel(byte WheelPos) {
    WheelPos = 255 - WheelPos;
    if(WheelPos < 85) {
        return strip.Color(255 - WheelPos * 3, 0, WheelPos * 3,0);
    }
    if(WheelPos < 170) {
        WheelPos -= 85;
        return strip.Color(0, WheelPos * 3, 255 - WheelPos * 3,0);
    }
    WheelPos -= 170;
    return strip.Color(WheelPos * 3, 255 - WheelPos * 3, 0,0);
    }

    uint8_t red(uint32_t c) {
    return (c >> 16);
    }
    uint8_t green(uint32_t c) {
    return (c >> 8);
    }
    uint8_t blue(uint32_t c) {
    return (c);
    }

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="100%" height="315" src="https://www.youtube.com/embed/jilxpWxtArI" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

    
Servo Control
-----------------

.. code-block:: c

    #include <Servo_megaTinyCore.h>

    Servo myservo;  // create servo object to control a servo
    // twelve servo objects can be created on most boards

    int pos = 0;    // variable to store the servo position

    void setup() {
        myservo.attach(9);  // attaches the servo on pin 9 to the servo object
    }

    void loop() {
        for (pos = 0; pos <= 180; pos += 1) { // goes from 0 degrees to 180 degrees
            // in steps of 1 degree
            myservo.write(pos);              // tell servo to go to position in variable 'pos'
            delay(15);                       // waits 15ms for the servo to reach the position
        }
        for (pos = 180; pos >= 0; pos -= 1) { // goes from 180 degrees to 0 degrees
            myservo.write(pos);              // tell servo to go to position in variable 'pos'
            delay(15);                       // waits 15ms for the servo to reach the position
        }
    }


EEPROM
------

.. code-block:: c

    #include <EEPROM.h>

    void setup() {

    float f = 0.00f;   //Variable to store data read from EEPROM.
    int eeAddress = 0; //EEPROM address to start reading from

    Serial.begin(9600);
    while (!Serial) {
        ; // wait for serial port to connect. Needed for native USB port only
    }
    Serial.print("Read float from EEPROM: ");

    //Get the float data from the EEPROM at position 'eeAddress'
    EEPROM.get(eeAddress, f);
    Serial.println(f, 3);    //This may print 'ovf, nan' if the data inside the EEPROM is not a valid float.

    /***
        As get also returns a reference to 'f', you can use it inline.
        E.g: Serial.print( EEPROM.get( eeAddress, f ) );
    ***/

    /***
        Get can be used with custom structures too.
        I have separated this into an extra function.
    ***/

    secondTest(); //Run the next test.
    }

    struct MyObject {
    float field1;
    byte field2;
    char name[10];
    };

    void secondTest() {
    int eeAddress = sizeof(float); //Move address to the next byte after float 'f'.

    MyObject customVar; //Variable to store custom object read from EEPROM.
    EEPROM.get(eeAddress, customVar);

    Serial.println("Read custom object from EEPROM: ");
    Serial.println(customVar.field1);
    Serial.println(customVar.field2);
    Serial.println(customVar.name);
    }

    void loop() {
    /* Empty loop */
    }

UART Communication with ESP8266
-------------------------------

.. code-block:: c

    #include "Adafruit_EPD.h"

    char rec;
    char rec_buf[10];
    volatile int index = 0;
    volatile int flag = 0;

    float temp_data = 0;

    #define EPD_CS     13
    #define EPD_DC      3
    #define SRAM_CS     2
    #define EPD_RESET   1 // can set to -1 and share with microcontroller Reset!
    #define EPD_BUSY    0 // can set to -1 to not use a pin (will wait a fixed delay)

    /* Uncomment the following line if you are using 1.54" tricolor EPD */
    Adafruit_IL0373 display(152, 152 ,EPD_DC, EPD_RESET, EPD_CS, SRAM_CS, EPD_BUSY);

    /* Uncomment the following line if you are using 2.15" tricolor EPD */
    //Adafruit_IL0373 display(212, 104 ,EPD_DC, EPD_RESET, EPD_CS, SRAM_CS, EPD_BUSY);

    /* Uncomment the following line if you are using 2.7" tricolor EPD */
    //Adafruit_IL91874 display(264, 176 ,EPD_DC, EPD_RESET, EPD_CS, SRAM_CS);
    char *buf = "123.45";

    void get_weather_data() {
    Serial.println("AT+CIPSTART=\"TCP\",\"api.openweathermap.org\",80");
    delay(1000);
    Serial.println("AT+CIPSEND=113");
    delay(500);

    while(Serial.available()){
        Serial.read();
    }  
    
    index = 0;
    flag = 0;

    memset(rec_buf, 0, sizeof(rec_buf));
    
    Serial.print("GET /data/2.5/weather?id=4885955&appid=a9077ed0f95a1800cd2e7752adfdc137 HTTP/1.1");
    Serial.write(0x0d);
    Serial.write(0x0a);
    Serial.println("Host: api.openweathermap.org");
    Serial.write(0x0a);

    for(int i = 0; i < 1000; i++){
        delay(1);
        while(Serial.available()){
        rec = Serial.read();
    
        if (rec == 't' && flag == 0) {
            flag++;
        } else if (rec == 'e' && flag == 1) {
            flag++;
        } else if (rec == 'm' && flag == 2) {
            flag++;
        } else if (rec == 'p' && flag == 3) {
            flag++;
        } else if (rec == 0x22 && flag == 4) {
            flag++;
        } else if (rec == 0x3a && flag == 5) {
            flag++;
        } else if (flag == 6) {
            if (index < 6) {
            rec_buf[index++] = rec;
            } else {
            flag = 0;
            }
        } else {
            flag = 0;
        }
        }  

        if (index == 6) {
        break;
        }
    }
    
    
    
    delay(1000);
    
    Serial.println("AT+CIPCLOSE");

    }

    void display_temp(float temp) {
        display.clearBuffer();
        display.setCursor(2, 0);
        display.fillScreen(EPD_WHITE);
        display.setTextColor(EPD_BLACK);
        display.setTextSize(2);
        display.println(" ");
        display.println("Current");
        display.println("Temperature:");
        display.println(" ");
        display.setTextSize(4);
        display.setTextColor(EPD_RED);
        display.print(temp, 1);
        display.println(" F");
        //refresh the display
        display.display();
    }

    void setup() {
        Serial.begin(115200);
        
        display.begin();
        
        delay(10000);
    }

    void loop() {

        get_weather_data();

        temp_data = atof(rec_buf);

        temp_data = (temp_data - 273.15) * 1.8 + 32;

        Serial.println(temp_data,2);

        if (temp_data > -100)
        {
            display_temp(temp_data);
        }
        
        
        delay(600000);
        //don't do anything!
    }

.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="100%" height="315" src="https://www.youtube.com/embed/GgDZX_R0gxE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>


Interfacing with LCD Display
-----------------------------

.. code-block:: c

    #include <Wire.h>
    #include "rgb_lcd.h"
    #include "Adafruit_HTU21DF.h"

    rgb_lcd lcd;

    const int colorR = 255;
    const int colorG = 255;
    const int colorB = 255;

    Adafruit_HTU21DF htu = Adafruit_HTU21DF();

    void setup() 
    {
    
        Serial.begin(9600);
        Serial.println("HTU21D-F test");
        
        // set up the LCD's number of columns and rows:
        lcd.begin(16, 2);
        
        lcd.setRGB(colorR, colorG, colorB);
        
        // Print a message to the LCD.
        //lcd.print("hello, world!");

    
        if (!htu.begin()) {
        Serial.println("Couldn't find sensor!");
        while (1);
        }
    
        delay(1000);
    }

    void loop() 
    {
        // set the cursor to column 0, line 1
        // (note: line 1 is the second row, since counting begins with 0):
        float temp = htu.readTemperature();
        float rel_hum = htu.readHumidity();
        lcd.setCursor(0, 0);
        lcd.print("Temp=");
        lcd.print(temp, 2);
        lcd.print(" C");
        
        lcd.setCursor(0, 1);
        // print the number of seconds since reset:
        lcd.print("Hum=");
        lcd.print(rel_hum, 2);
        lcd.print(" \%");
        
        Serial.print("Temp: "); Serial.print(temp); Serial.print(" C");
        Serial.print("   ");
        Serial.print("Humidity: "); Serial.print(rel_hum); Serial.println(" \%");
        delay(1000);
    }


.. raw:: html

    <div style="text-align: center; margin-bottom: 2em;">
    <iframe width="100%" height="315" src="https://www.youtube.com/embed/pp4p_UtTKt4" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>


Interfacing with E-ink Display
------------------------------

.. code-block:: c

    #include "Adafruit_EPD.h"
    
        
    #define EPD_CS     13
    #define EPD_DC      3
    #define SRAM_CS     2
    #define EPD_RESET   -1 // can set to -1 and share with microcontroller Reset!
    #define EPD_BUSY    -1 // can set to -1 to not use a pin (will wait a fixed delay)

    /* Uncomment the following line if you are using 1.54" tricolor EPD */
    Adafruit_IL0373 display(152, 152 ,EPD_DC, EPD_RESET, EPD_CS, SRAM_CS, EPD_BUSY);

    /* Uncomment the following line if you are using 2.15" tricolor EPD */
    //Adafruit_IL0373 display(212, 104 ,EPD_DC, EPD_RESET, EPD_CS, SRAM_CS, EPD_BUSY);

    /* Uncomment the following line if you are using 2.7" tricolor EPD */
    //Adafruit_IL91874 display(264, 176 ,EPD_DC, EPD_RESET, EPD_CS, SRAM_CS);

    void setup() {

        Serial.begin(9600);
        
        display.begin();
        display.clearBuffer();

        //draw some pretty lines
        for (int16_t i=0; i<display.width(); i+=4) {
            display.drawLine(0, 0, i, display.height()-1, EPD_BLACK);
        }

        for (int16_t i=0; i<display.height(); i+=4) {
            display.drawLine(display.width()-1, 0, 0, i, EPD_RED);
        }
        
        //refresh the display
        display.display();
    }

    void loop() {
    //don't do anything!
    }