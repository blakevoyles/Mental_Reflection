  #include <Arduino.h>
  #include <U8x8lib.h>

  U8X8_SSD1306_128X64_ALT0_HW_I2C u8x8(/* reset=*/ U8X8_PIN_NONE);

  int mood = 0; // the number displayed used to track the mood input

  const int rotaryPin = A0;           // the number of the potentiometer pin
  const int buttonPin = 6;      // the number of the pushbutton pin
  const int ledPin =  4;        // the number of the LED pin
  const int tonepin = 5;              // the number of the buzzer pin

  const int tones[] = {221, 248, 278, 294, 330, 350, 393, 441, 495, 556, 589}; // tone array to change the sound when potentiometer is turned
  const char *num[] = {"0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "10"}; // Array to display the numbers
  const char message[11][100] = {     // Array for the comment after input
    "                Keep going, tomorrow is a new day!\0", 
    "                Sorry things aren't great, but you totally are!\0", 
    "                Remember you are loved!\0", 
    "                Keep chugging along, everyday is an opportunity to grow!\0", 
    "                If you're feeling down, do something that you love!\0", 
    "                I'm sorry your day was average, but glad it wasn't worse!\0", 
    "                Excited that the day is above average, keep up all your hard work!\0", 
    "                Pretty good day, let's do it again tomorrow!\0",
    "                So happy that your day was good, we hope tomorrow treats you the same!\0", 
    "                Almost a perfect day, no need to chase perfection!\0", 
    "                BEST DAY EVER, keep making those good choices!!!\0"
    };

  int buttonState = 0;         // variable for reading the pushbutton status
  boolean textMode = false;    // indicates when the text should scroll across the OLED
  int currentIndex = 0;        // variable used to scroll the message
  int avgArray[] = {-1,-1,-1,-1,-1,-1,-1}; // used to store the mood inputs for the last 7 days
  int avgCount = 0; // variable used to set the inputs in the avgArray

  void setup() {
    // put your setup code here, to run once:
    // setup all the pins and modules
    pinMode(rotaryPin, INPUT);
    pinMode(ledPin, OUTPUT);
    pinMode(tonepin,OUTPUT);
    pinMode(buttonPin, INPUT);
    u8x8.begin();
    u8x8.setFlipMode(1);
  }
   void loop() {
    // put your main code here, to run repeatedly:
    // checks to see if the loop should displaying the mood values for input
    if (textMode) {
      if (message[mood][currentIndex] == '\0') {    // used  to start the scrolling when input is selected
        textMode = false;
        currentIndex = -1;
        digitalWrite(ledPin, LOW);
      }
        u8x8.setFont(u8x8_font_amstrad_cpc_extended_r);
        u8x8.setCursor(0, 1);
        u8x8.print(slidingFrame(currentIndex));
        delay(100);
        currentIndex++;
    } else { 
      int pastMood = mood;
      mood = analogRead(rotaryPin) / 100;  // read the input from the potentiometer when the button is pressed
      if (mood != pastMood) {
        tone(tonepin, tones[mood], 100); // plays the tone when the potentiometer is turned
        u8x8.clear();
      }
      // Displays the "What's your mood?"
      u8x8.setFont(u8x8_font_amstrad_cpc_extended_r);
      u8x8.draw2x2String(9, 1, num[mood]);
      u8x8.setCursor(0, 0);
      u8x8.print("What's");
      u8x8.setCursor(0, 1);
      u8x8.print("your");
      u8x8.setCursor(0, 2);
      u8x8.print("mood?");
    }

    buttonState = digitalRead(buttonPin);
    if (buttonState == HIGH) {
      // turn LED on:
      digitalWrite(ledPin, HIGH);
      tone(tonepin, tones[mood], 100); // plays a tone to confirm the button is pressed
      avgCount = avgCount % 7; //used to change which value of the average array is changed
      avgArray[avgCount] = mood; // adds the mood to the avgArray
      avgCount++;
      textMode = true; // used to go back to the numbers ready to recieve next input
      u8x8.clear();
      // Displays the average for the past 7 days 
      u8x8.setCursor(0, 1);
      u8x8.print(" 7-Day Average: ");
      u8x8.setCursor(6, 2);
      u8x8.print(calcAverage());
      delay(3000);
      u8x8.clear();
    }
  }
  String slidingFrame(int index) {  // used to offset each message and make the scroll happen, and stop when /0 is reached
    String frame = "                ";
    for (int i = 0; i < 16; i++) {
      if (index >= 0) {
        if (message[mood][index] == '\0') {
          break;
        }
        frame[i] = message[mood][index];
        index++;
      }
    }
    return(frame);
  }

  float calcAverage() { // method to calculate the average of the inputs
    float sum = 0;
    for(int i = 0; i < 7; i++) {
      if (avgArray[i] != -1) {
        sum += avgArray[i];
      } // if
    } // for
    if (avgArray[6] == -1) {
      return sum / avgCount;
    }
    return sum / 7.0;
  }
