# WALL-E Robot: An Interactive Companion

Have you ever wanted a personalized robot companion? Look no further! WALL-E is here, an innovative automative robot that responds to your presence. By simply moving your hand closer or farther away, you can change WALL-E's emotions and movements. This project combines Arduino programming, sensor technology, and expressive visual elements to make a robot come to life. 

### Modification: Android App!

In this milestone, I worked on controlling Robo from a remote controlled app. Key achievements include: 

- Installed OHM resistors and bluetooth module
- Coded and designed an Android app on MIT appinventor
- Adjusted arduino code to accomodate app


![Bluetooth Schematic](schematicwithbluetoothj_bb.png)
*Figure 7: Installation of new HC-05 bluetooth module*

![Schematic](appcode.png)
*Figure 6: *Design of app and code on MITappinventor*

### Third Milestone: Working Robo!

<iframe width="560" height="315" src="https://www.youtube.com/embed/WgoiZ1K0GwM?si=B_8jLmUFtSOCsSyv" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

In this milestone, I assembled a working automative robot. Key achievements include: 

- Assembling each 3D printed part
- Finding suitable screws for each part
- Coding new patterns and logic for servo movement

### Challenges

- Miscalibrating servos
- Breaking servos due to non-grounded connections
- Coding for blinking LED lights

![Real project depiction](realproject.png)
*Figure 5: My project*

![Schematic](fixedschematic.png)
*Figure 4: Wiring for all components*

The purple wire connecting the servos to the ground is crucial for proper functioning:

1. It completes the circuit, allowing current to return to the power source for servo operation.
2. It ensures consistent voltage, preventing short circuits.

During testing for milestone #3, one of my servos short-circuited, leading to this discovery.

### Components Overview

#### Arduino Uno

- **Microcontroller**: Acts as the project's brain, processing inputs and controlling outputs.
- **Connections**:
  - **Digital Pins**: Used for controlling servos and the LCD screen.
  - **Analog Pins**: Not utilized in this schematic.
  - **Power Pins**: Provide power to the breadboard and other components.

#### Breadboard

- **Prototyping Board**: Used for creating temporary circuits without soldering.
- **Connections**:
  - **Power Rails**: Distribute power and ground from the Arduino and battery pack.
  - **Signal Lines**: Connect various components to the Arduino's I/O pins.

#### Servos

- **Actuators**: Convert electrical signals into mechanical movement.
- **Connections**:
  - **Signal Wires (Yellow)**: Connect to Arduino digital pins for PWM control.
  - **Power Wires (Red)**: Connected to the positive terminal of the battery pack.
  - **Ground Wires (Black)**: Connected to the breadboard ground, linked to Arduino ground via the purple wire.

#### Ultrasonic Sensor (HC-SR04)

- **Distance Measurement**: Uses ultrasonic waves to measure distance to an object.
- **Connections**:
  - **VCC**: Connected to Arduino's 5V pin for power.
  - **GND**: Connected to the breadboard ground.
  - **Trig and Echo Pins**: Connected to specific digital pins on the Arduino for sending and receiving signals.

#### 16x2 LCD Screen

- **Display**: Shows information such as sensor readings or system status.
- **Connections**:
  - **VCC**: Connected to Arduino's 5V pin for power.
  - **GND**: Connected to the breadboard ground.
  - **Data Pins**: Connected to specific digital pins on the Arduino for data input.
  - **Contrast Pin**: Connected to a potentiometer (not shown) for adjusting display contrast.
  - **Backlight**: Powered from the same source as the LCD.

#### 8x8 LED Matrix

- **Visual Indicator**: Used for displaying patterns or simple graphics.
- **Connections**:
  - **VCC**: Connected to Arduino's 5V pin for power.
  - **GND**: Connected to the breadboard ground.
  - **Control Pins**: Connected to specific digital pins on the Arduino for data and control signals.

#### 4xAAA Battery Pack

- **Power Supply**: Provides additional power to servos to ensure sufficient current for operation.
- **Connections**:
  - **Positive Terminal**: Connected to the breadboard power rail for supplying voltage to servos.
  - **Negative Terminal**: Connected to the breadboard ground, establishing a common ground with the Arduino.

#### Wires and Connections

- **Power Wires (Red)**: Carry power from Arduino and battery pack to components.
- **Ground Wires (Black/Purple)**: Create a unified ground for all components, essential for consistent operation.
- **Signal Wires (Yellow/Other Colors)**: Transmit control signals from Arduino to servos, LCD screen, ultrasonic sensor, and LED matrix.

### Code and explanations 
```c++ 

// Include libraries for LCD, LED matrix, and Servo motors
#include <LiquidCrystal_I2C.h>  // Library for I2C LCD
#include <LedControl.h>         // Library for LED matrix
#include <Servo.h>              // Library for Servo motors

// Pins for LED matrix connected to Arduino
int DIN = 11;
int CS = 9;
int CLK = 10;
int DIN_2 = 6;
int CS_2 = 8;
int CLK_2 = 7;

// Create instances for LCD and LED matrix
LiquidCrystal_I2C lcd(0x27, 16, 2);  // LCD instance
LedControl ledmatrix = LedControl(DIN, CLK, CS);  // LED matrix instance (left)
LedControl ledmatrix_2 = LedControl(DIN_2, CLK_2, CS_2);  // LED matrix instance (right)

// Create instances for the Servos
Servo pan;         // Pan servo instance
Servo tilt;        // Tilt servo instance
Servo leftBrow;    // Left eyebrow servo instance
Servo rightBrow;   // Right eyebrow servo instance

// Pins for the ultrasonic sensor
const int trigPin = 13;   // Trigger pin for ultrasonic sensor
const int echoPin = 12;   // Echo pin for ultrasonic sensor

// Variables to measure distance
float duration, distance;

// Bitmaps for different emotional expressions on LED matrix
byte neutral_bmp[8] = {
  B00000000,
  B00111100,
  B01000010,
  B01011010,
  B01011010,
  B01000010,
  B00111100,
  B00000000
};
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
  B00111000,
  B00100100,
  B00111010,
  B00111010,
  B00100100,
  B00111000,
  B00000000
};
byte loveL_bmp[8] = {
  B00011000,
  B00100100,
  B01000010,
  B10000001,
  B10000001,
  B10011001,
  B01100110,
  B00000000
};
byte loveR_bmp[8] = {
  B00011000,
  B00100100,
  B01000010,
  B10000001,
  B10000001,
  B10011001,
  B01100110,
  B00000000
};
byte sad_bmp[8] = {
  B10000001,
  B01000010,
  B00100100,
  B00011000,
  B00011000,
  B00100100,
  B01000010,
  B10000001
};
byte surprisedL_bmp[8] = {
  B00000000,
  B01111110,
  B01000010,
  B01000010,
  B01000010,
  B01000010,
  B01111110,
  B00000000
};
byte surprisedR_bmp[8] = {
  B00000000,
  B01111110,
  B01000010,
  B01000010,
  B01000010,
  B01000010,
  B01111110,
  B00000000
};

// Function to display pattern on LED matrix
void displayPattern(byte patternL[], byte patternR[]) {
  for (int i = 0; i < 8; i++) {
    ledmatrix.setColumn(0, i, patternL[i]);
    ledmatrix_2.setColumn(0, i, patternR[i]);
  }
}

// Function to write emotion on LCD
void displayEmotion(const char* emotion) {
  lcd.clear();
  lcd.print(emotion);
}

// Function to move servos
void moveServos(int left, int right, int panIn, int tiltIn) {
  leftBrow.write(left);
  rightBrow.write(right);
  pan.write(panIn);
  tilt.write(tiltIn);
}

// Function to measure distance using ultrasonic sensor
float measureDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  return (duration * 0.0343) / 2;
}

void setup() {
  // Debugging
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
  displayPattern(neutral_bmp, neutral_bmp);  // Initial neutral pattern
  displayEmotion("Neutral");  // Initial neutral emotion

  // Define input/outputs
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // Attach servos to respective pins
  pan.attach(5);
  tilt.attach(4);
  leftBrow.attach(2);
  rightBrow.attach(3);

  // Set initial positions of servos
  moveServos(60, 90, 130, 90);  // Adjust as needed
}

void loop() {
  // Measure distance using ultrasonic sensor
  distance = measureDistance();
  Serial.println(distance);

  // Logic to display emotions based on distance
  if (distance <= 10 && distance > 0) {
    // Display sad emotion and prompt to back up
    moveServos(60, 120, 130, 90);  // Adjust as needed
    displayPattern(sad_bmp, sad_bmp);
    displayEmotion("Please back up!");

    // Move tilt servo up and down for effect
    for (int i = 90; i > 45; i--) {
      delay(10);
      tilt.write(i);
    }
    for (int j = 45; j < 135; j++) {
      delay(10);
      tilt.write(j);
    }
  } else if (distance <= 30 && distance > 10) {
    // Display happy emotion and perform a movement
    tilt.write(90);  // Reset tilt position
    displayPattern(happyL_bmp, happyR_bmp);
    displayEmotion("I am so happy");

    // Move pan servo left and right for effect
    for (int i = 150; i > 110; i--) {
      delay(10);
      pan.write(i);
    }
    for (int j = 110; j < 150; j++) {
      delay(10);
      pan.write(j);
    }
  } else if (distance <= 50 && distance > 30) {
    // Display love emotion and light up LED matrix
    displayEmotion("I love you");
    moveServos(90, 90, 130, 90);  

    // Display heart pattern on LED matrix(blinking)
    displayPattern(loveL_bmp, loveR_bmp);
    delay(500);  // On for 500ms
    ledmatrix.clearDisplay(0);  // Turn off left matrix
    ledmatrix_2.clearDisplay(0);  // Turn off right matrix
    delay(500);  // Off for 500ms
  } else {
    // Default to neutral emotion and initial servo position
    displayPattern(neutral_bmp, neutral_bmp);
    displayEmotion("Hello!");
    moveServos(120, 60, 130, 90);  
  }
}

```

This code is mostly the same as milestone 2, but with minor tweaks. 

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
