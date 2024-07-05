# WALL-E Robot: An Interactive Companion

Have you ever wanted a personalized robot companion? Look no further! WALL-E is here, an innovative automative robot that responds to your presence. By simply moving your hand closer or farther away, you can change WALL-E's emotions and movements. This project combines Arduino programming, sensor technology, and expressive visual elements to make a robot come to life. 

| **Engineer** | **School** | **Major Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Anish N | Dougherty Valley High School | Computer Engineering | Incoming Senior |

![WALL-E Logo](logo.svg)

## Second Milestone: Integrating the components

<iframe width="560" height="315" src="https://www.youtube.com/embed/WgoiZ1K0GwM?si=B_8jLmUFtSOCsSyv" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

Using the Arduino IDE, I've successfully implemented multiple functions for each component of the WALL-E robot, as highlighted in the schematic below. Key achievements include:

- Wiring and implementing the ultrasonic sensor and multiple servos
- Utilizing the ultrasonic sensor to calculate distance by measuring reflected sound waves
- Converting sensor data into tangible distances to trigger various functions

### Next Steps
Before the final draft, I plan to:
1. Create and assemble the 3D printed WALL-E bot
2. Install components correctly
3. Fine-tune the code to display emotions corresponding to the correct distances

![Schematic](secondmilestone.png)
*Figure 3: Wiring for all components*

[Ultra-sonic sensor guide](https://projecthub.arduino.cc/Isaac100/getting-started-with-the-hc-sr04-ultrasonic-sensor-7cabe1):

### My code and explanations 
```c++
//packages installed from the internet
#include <LiquidCrystal_I2C.h>
#include <LedControl.h>
#include <Servo.h>

// Pins for LED matrix connected to arduino
int DIN = 11;
int CS = 9;
int CLK = 10;
int DIN_2 = 6;
int CS_2 = 8;
int CLK_2 = 7;

 // Create instances for LCD and LED matrix
LiquidCrystal_I2C lcd(0x27, 16, 2);
LedControl ledmatrix = LedControl(DIN, CLK, CS);
LedControl ledmatrix_2 = LedControl(DIN_2, CLK_2, CS_2);

//create instances for the Servos
Servo pan;  
Servo tilt;
Servo leftBrow;
Servo rightBrow;

//pins for the ultrasonic sensor
const int trigPin = 13;  
const int echoPin = 12;

//variable to measure distance
float duration, distance;  

// Define byte arrays with LED matrix patterns for each emotion
//0 represents no light while 1 represents on 
byte happyL_bmp[8] = {
  B00000000, 
  B00011100, 
  B00100100, 
  B01011100,
  B01011100,
  B00100100, 
  B00011100, 
  B00000000
};
byte happyR_bmp[8] = {
  B00000000, 
  B00011100, 
  B00100100, 
  B01011100,
  B01011100, 
  B00100100, 
  B00011100, 
  B00000000
};
byte neutralL_bmp[8] = {
  B00000000, 
  B00111100, 
  B01000010, 
  B01011010,
  B01011010, 
  B01000010, 
  B00111100, 
  B00000000
};
byte neutralR_bmp[8] = {
  B00000000, 
  B00111100, 
  B01000010, 
  B01011010,
  B01011010,
   B01000010, 
   B00111100, 
   B00000000
};
byte sadL_bmp[8] = {
  B00000000, 
  B00111100, 
  B01000010, 
  B01011010,
  B00111010,
  B00010010, 
  B00001100, 
  B00000000
};
byte sadR_bmp[8] = {
  B00000000, 
  B00001100, 
  B00010010, 
  B00111010,
  B01011010,
  B01000010, 
  B00111100, 
  B00000000
};
byte surprisedL_bmp[8] = {
  B01111110, 
  B10000001, 
  B10000001, 
  B10011001,
  B10011001, 
  B10000001, 
  B10000001, 
  B01111110
};
byte surprisedR_bmp[8] = {
  B01111110, 
  B10000001, 
  B10000001, 
  B10011001,
  B10011001, 
  B10000001, 
  B10000001, 
  B01111110
};

// Function to display pattern on LED matrix
//uses for loop to iterate through each led on the matrix 
void displayPattern(byte patternL[], byte patternR[]) {
  for (int i = 0; i < 8; i++) {
    ledmatrix.setColumn(0, i, patternL[i]);
    ledmatrix_2.setColumn(0, i, patternR[i]);
  }
  //all serial prints are for debugging
  Serial.println("Pattern displayed");
}

// Function to write emotion on LCD
void displayEmotion(const char* emotion) {
  lcd.clear();
  lcd.print(emotion);
  Serial.print("Emotion displayed: ");
  Serial.println(emotion);
}

// Function to move servos
void moveServos(int left, int right, int panIn, int tiltIn) {
  leftBrow.write(left);
  rightBrow.write(right);
  pan.write(panIn);
  tilt.write(tiltIn);
  Serial.println("Servos moved");
}

// Function to measure distance using ultrasonic sensor
float measureDistance() {
  digitalWrite(trigPin, LOW);  
  delayMicroseconds(2);  
  digitalWrite(trigPin, HIGH);  
  delayMicroseconds(10);  
  digitalWrite(trigPin, LOW);
  //how it calculates distance  
  duration = pulseIn(echoPin, HIGH);  
  return (duration * 0.0343) / 2;
}

void setup() {
  //debugging
  Serial.begin(9600);
  
  // Set up LCD
  lcd.init();
  lcd.backlight();
  
  // Set up LED matrices
  ledmatrix.shutdown(0, false);
  ledmatrix.setIntensity(0, 1);
  ledmatrix.clearDisplay(0);
  
  ledmatrix_2.shutdown(0, false);
  ledmatrix_2.setIntensity(0, 1);
  ledmatrix_2.clearDisplay(0);
  
  // Initialize columns with letters
  displayPattern(neutralL_bmp, neutralR_bmp);  // Initial neutral pattern
  displayEmotion("Neutral");  // Initial neutral emotion
  
  //define input/outputs
  pinMode(trigPin, OUTPUT);  
  pinMode(echoPin, INPUT);  
  
  // Attach servos
  pan.attach(5); 
  tilt.attach(4);
  leftBrow.attach(2);
  rightBrow.attach(3);
  
  // Set initial positions
  moveServos(0, 0, 0, 0);
}

void loop() {
  distance = measureDistance();
  Serial.print("Distance: ");  
  Serial.println(distance);  
  

  // logic to display emotions based on distance
  if (distance <= 10) {
    displayPattern(happyL_bmp, happyR_bmp);
    displayEmotion("Happy");
    moveServos(100, 0, 0, 0);
  } else if (distance <= 20) {
    displayPattern(surprisedL_bmp, surprisedR_bmp);
    displayEmotion("Surprised");
    moveServos(100, 0, 100, 0);
  } else if (distance <= 30) {
    displayPattern(neutralL_bmp, neutralR_bmp);
    displayEmotion("Neutral");
    moveServos(100, 0, 0, 0);
  } else {
    displayPattern(sadL_bmp, sadR_bmp);
    displayEmotion("Sad");
    moveServos(100, 0, 0, 0);
  }

  // Move servos in loop
  moveServos(100, 0, 100, 0);
  delay(1000);
  moveServos(0, 100, 0, 100);
  delay(1000);
}
```


# First Milestone: Lighting up screens

<iframe width="560" height="315" src="https://www.youtube.com/embed/YkfFGZ1TbZA?si=SSonITQkPW6j-yTY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

In this initial phase, I focused on:

- Getting the LED lights and LCD screen to function properly
- Displaying text and my initials on the screens
- Learning to code the LED lights and LCD screen using the Arduino IDE

Moving forward, I aim to:

- Integrate all components, including servos
- Implement proximity-based reactions using the ultrasonic sensor
- Begin construction of the physical robot using 3D printed parts

![starter project](firstmilestone.png)
*Figure 2: Wiring for LED and LCD Screens*



<!--- 
/*
# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c++
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  Serial.println("Hello World!");
}

void loop() {
  // put your main code here, to run repeatedly:

}
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.

-->
# Arduino Starter Project

<iframe width="560" height="315" src="https://www.youtube.com/embed/IUZZ2V6nGmU?si=mzgKdNSG2Q01wio3" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

I chose this starter project because it would give me a good introduction into how to code and use the Arduino. Additionally, it gave me great experience with soldering, and electrical components. I now have a basic idea of a circuit, and understand the layout of the breadboard. This knowledge has given me the confidence to continue on my main project, which will use the Arduino extensively, as well as plenty of electrical components. 

![starter project](starterproject.png)
*Figure 1: Arduino and breadboard wiring*

- **Yellow**: provides 5V of power
- **Orange**: connects to ground 
- **Brown**: gives power to button 
- **Black**: gives ground to button 
- **Blue**: provides digital cue 
- **Black**: provides ground to LED with resistor to stop voltage 

When the button is pressed, the circuit is allowed to connect, causing the LED to light up. It is also possible to reverse this effect with the code. 

[Project Guide](https://docs.arduino.cc/built-in-examples/digital/Button/):

```cpp
const int buttonPin = 2;  // the number of the pushbutton pin
const int ledPin = 13;    // the number of the LED pin

// variables will change:
int buttonState = 0;  // variable for reading the pushbutton status

void setup() {
  // initialize the LED pin as an output:
  pinMode(ledPin, OUTPUT);
  // initialize the pushbutton pin as an input:
  pinMode(buttonPin, INPUT);
}

void loop() {
  // read the state of the pushbutton value:
  buttonState = digitalRead(buttonPin);

  // check if the pushbutton is pressed. If it is, the buttonState is HIGH:
  if (buttonState == HIGH) {
    // turn LED on:
    digitalWrite(ledPin, HIGH);
  } else {
    // turn LED off:
    digitalWrite(ledPin, LOW);
  }
}

```

switching HIGH and LOW will reverse the effect of the LED light. Instead of turning on when pressed, it will turn off when pressed. 
