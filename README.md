#include <Keypad.h>
#include <AccelStepper.h>
#include <LiquidCrystal_I2C.h>
#include <Keypad.h>

#define PUMP1_DIRECTION   8
#define PUMP1_STEP        9
#define PUMP1_ENABLE      10
 
#define PUMP2_DIRECTION   11
#define PUMP2_STEP        12
#define PUMP2_ENABLE      13

#define PUMP3_DIRECTION   52
#define PUMP3_STEP        50
#define PUMP3_ENABLE      48

#define PUMP4_DIRECTION   4
#define PUMP4_STEP        3
#define PUMP4_ENABLE      2

#define STEPS_PER_REVOLUTION 200


// Define the keypad pins and keys
const byte ROWS = 4;
const byte COLS = 3;
char keys[ROWS][COLS] = {
  {'1', '2', '3'},
  {'4', '5', '6'},
  {'7', '8', '9'},
  {'*', '0', '#'}
};
byte rowPins[ROWS] = {22, 23, 24, 25};
byte colPins[COLS] = {26, 27, 28};
Keypad keypad = Keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);
int volumn;
float pump1_steps,pump2_steps,pump3_steps,pump4_steps;

// Define the LCD pins
LiquidCrystal_I2C lcd(0x27,16,2);


// Define the pump variables

int primeDelay = 6000;
char userSelect;

AccelStepper pump1(AccelStepper::DRIVER, PUMP1_STEP, PUMP1_DIRECTION);
AccelStepper pump2(AccelStepper::DRIVER, PUMP2_STEP, PUMP2_DIRECTION);
AccelStepper pump3(AccelStepper::DRIVER, PUMP3_STEP, PUMP3_DIRECTION);
AccelStepper pump4(AccelStepper::DRIVER, PUMP4_STEP, PUMP4_DIRECTION);

String currentNumber = ""; // Declare currentNumber outside of loop()

int motorCount = 0;

void printVolumn(int volumn){
  Serial.print("Volume:");
  Serial.println(volumn);
  // Display the current volume on the LCD
  lcd.clear();
  lcd.print("Dispensing");
  delay(1200);
  lcd.clear();
  lcd.print("Volume (ml):");
  lcd.setCursor(0, 1);
  lcd.print(volumn);
  delay(1500);
  lcd.clear();
}

void setup() {
  Serial.begin(9600);
  
  // Initialize the LCD
  lcd.init();
  lcd.backlight();
  lcd.print("Hello");
  delay(5000);
  lcd.clear();
  // Set the maximum speed and acceleration for each pump
  pump1.setMaxSpeed(6000);
  pump1.setAcceleration(10000);
  
  pump2.setMaxSpeed(1800);
  pump2.setAcceleration(5000);
  
  pump3.setMaxSpeed(3000);
  pump3.setAcceleration(5000);

  pump4.setMaxSpeed(1800);
  pump4.setAcceleration(9000);

  // Enable all pumps
  digitalWrite(PUMP1_ENABLE, LOW);
  digitalWrite(PUMP2_ENABLE, LOW);
  digitalWrite(PUMP3_ENABLE, LOW);
  digitalWrite(PUMP4_ENABLE, LOW);

  // Move pumps to initial position
  pump1.move(0);
  pump1.runToPosition();
  pump2.move(0);
  pump2.runToPosition();
  pump3.move(0);
  pump3.runToPosition();
  pump4.move(0);
  pump4.runToPosition();

  
 
  
  
  // Set the initial volume to 0
  volumn = 0;
}

void loop() {
  char key = keypad.getKey();
  
  if (key != NO_KEY) {
    if (isdigit(key)){
        Serial.println(key);
        userSelect = key;
        
    }
    else if (key == '#'){
      Serial.println("# is pressed");
      switch (userSelect){
          
          case '0':
            volumn = 50;
            pump1_steps = 159646;
            pump2_steps = 2057.9;
            pump3_steps = 46776.6;
            pump4_steps = 4292.5;
            printVolumn(volumn);
            break;
    
          case '1':
            volumn = 100;
            pump1_steps = 319292;
            pump2_steps = 4115.8;
            pump3_steps = 93553.2;
            pump4_steps = 8584.5;
            printVolumn(volumn);
            break;
    
          case '2':
            volumn = 150;
            pump1_steps = 478938;
            pump2_steps = 6173.7;
            pump3_steps = 140329.8;
            pump4_steps = 12876.8;
            printVolumn(volumn);
            break;
    
          case '3':
            volumn = 200;  
            pump1_steps = 638584;
            pump2_steps = 8231.6;
            pump3_steps = 187106.4;
            pump4_steps = 17169;
            printVolumn(volumn);
            break;
    
          case '4':
            volumn = 250;
            pump1_steps = 798229;
            pump2_steps = 10289.5;
            pump3_steps = 233883;
            pump4_steps = 21461.5;
            printVolumn(volumn);
            break;
    
          case '5':
            volumn = 300;
            pump1_steps = 957874.5;
            pump2_steps = 12347.5;
            pump3_steps = 280659;
            pump4_steps = 25753.5;
            printVolumn(volumn);
            break;
    
          case '6':
            volumn = 350;
            pump1_steps = 1117520.08;
            pump2_steps = 14405;
            pump3_steps = 327435.5;
            pump4_steps = 30045.5;
            printVolumn(volumn);
            break;
    
          case '7':
            volumn = 400;
            pump1_steps = 1277165.8;
            pump2_steps = 16462.2;
            pump3_steps = 374212;
            pump4_steps = 34337.8;
            printVolumn(volumn);
            break;
    
          case '8':
            volumn = 450;
            pump1_steps = 1436811.61;
            pump2_steps = 18520;
            pump3_steps = 420989;
            pump4_steps = 38630;
            printVolumn(volumn);
            break;
    
          case '9':
            volumn = 500;
            pump1_steps = 1598542;
            pump2_steps = 20577.8;
            pump3_steps = 467765.5;
            pump4_steps = 42922;
            printVolumn(volumn);
            break;  
          }
    }
    else if (key == '*'){
      Serial.println("* is clicked");
      Serial.print("Motor ");
      Serial.print(motorCount);
      Serial.print(" will be moving");

      if (motorCount == 0){
        lcd.clear();
        pump1.move(pump1_steps);
        lcd.print("Motor 1 is running");
        while(pump1.distanceToGo() != 0) {
          pump1.run();
          
        }
        lcd.clear();
        motorCount++;
        lcd.print("Waiting for pump 2");
        delay(2000);
        lcd.clear();
      }
      else if (motorCount == 1){
        lcd.clear();
        pump2.move(pump2_steps);
        lcd.print("Motor 2 is running");
        while(pump2.distanceToGo() != 0) {
          pump2.run();
          
        }
        lcd.clear();
        motorCount++;
        lcd.print("Waiting for pump 3");
        delay(2000);
        lcd.clear();
      }
      else if (motorCount == 2){
        lcd.clear();
        pump3.move(pump1_steps);
        lcd.print("Motor 3 is running");
        while(pump3.distanceToGo() != 0) {
          pump3.run();
          
          }
          lcd.clear();
        motorCount++;
        lcd.print("Waiting for pump 4");
        delay(2000);
        lcd.clear();
      }
      else if (motorCount == 3){
        lcd.clear();
        pump4.move(pump4_steps);
        lcd.print("Motor 4 is running");
        while(pump4.distanceToGo() != 0) {
          pump4.run();
          
        }
        lcd.clear();
        motorCount = 0;
      }
      
    }
  }
}
