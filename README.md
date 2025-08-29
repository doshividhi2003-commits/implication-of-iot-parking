# Implication of IoT in Car Parking System 🚗📶  

An IoT-based car parking system that uses sensors and real-time data to detect available slots, reduce traffic congestion, save fuel, and lower emissions. It enhances user convenience with mobile access, automated entry/exit, and supports smart city sustainability.  

---

## 🔹 Project Overview  
This project demonstrates how IoT can be applied in smart city infrastructure to improve parking management. Using Arduino, ultrasonic sensors, and a servo motor, the system detects free parking slots and displays the status on an LCD screen. It can be extended with mobile/web apps for real-time monitoring.  

---

## ⚙️ Components Used  
- Arduino Uno R3  
- Ultrasonic Sensors (HC-SR04)  
- Servo Motor  
- LCD Display (16x2)  
- Breadboard and Jumper Wires  

---

## 🚀 Features  
- Real-time detection of parking slot availability  
- Automated barrier control with servo motor  
- LCD display showing parking status  
- Eco-friendly solution reducing fuel wastage  
- Scalable for larger parking lots  

---

## 🛠️ How It Works  
1. Ultrasonic sensors detect whether a slot is occupied.  
2. Data is processed by the Arduino Uno.  
3. If slots are available, the servo motor opens the barrier.  
4. LCD displays the number of free slots.  

---

## 📸 Circuit Design (Tinkercad)  
<img width="1456" height="777" alt="image" src="https://github.com/user-attachments/assets/97d8b5eb-c2e2-45e1-9d2d-42c0b9b5d1b1" />
 

---

## 📂 Code   
#include <Servo.h>
#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

#define PIN_S1 10
#define PIN_S2 9
#define PIN_S3 8
#define PIN_ENTRANCE 7
#define PIN_EXIT 13
#define PIN_SERVO 6

const int DIST_THRESHOLD = 100;

Servo gate;

int slotsAvailable = 3;
int gateState = 0;   // 0=closed, -1=open-enter, 1=open-exit, -2=closing-after-enter, 2=closing-after-exit

void setup() {
  lcd.begin(16, 2);
  lcd.setCursor(0, 0);
  Serial.begin(9600);
  gate.attach(PIN_SERVO);
  gate.write(0);
}

long readDistance(int triggerPin, int echoPin) {
  pinMode(triggerPin, OUTPUT);
  digitalWrite(triggerPin, LOW);
  delayMicroseconds(2);
  digitalWrite(triggerPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(triggerPin, LOW);
  pinMode(echoPin, INPUT);
  return pulseIn(echoPin, HIGH);
}

void loop() {
  float d1 = 0.01723 * readDistance(PIN_S1, PIN_S1);
  float d2 = 0.01723 * readDistance(PIN_S2, PIN_S2);
  float d3 = 0.01723 * readDistance(PIN_S3, PIN_S3);
  float dEntrance = 0.01723 * readDistance(PIN_ENTRANCE, PIN_ENTRANCE);
  float dExit = 0.01723 * readDistance(PIN_EXIT, PIN_EXIT);

  Serial.println("d1 = " + String(d1) + "cm");
  Serial.println("d2 = " + String(d2) + "cm");
  Serial.println("d3 = " + String(d3) + "cm");
  Serial.println("entrance = " + String(dEntrance) + "cm");
  Serial.println("exit = " + String(dExit) + "cm");

  if (gateState == 0) {
    if (dEntrance < DIST_THRESHOLD && dExit >= DIST_THRESHOLD && slotsAvailable > 0) {
      slotsAvailable -= 1;
      gateState = -1;
      gate.write(90);
    }
    
    if (dEntrance >= DIST_THRESHOLD && dExit < DIST_THRESHOLD && slotsAvailable < 3) {
      slotsAvailable += 1;
      gateState = 1;
      gate.write(90);
    }
    
  } else if (gateState == -1) {
    if (dEntrance >= DIST_THRESHOLD && dExit < DIST_THRESHOLD) {
      gateState = -2;
      gate.write(0);
    }
    
  } else if (gateState == 1) {
    if (dExit >= DIST_THRESHOLD && dEntrance < DIST_THRESHOLD) {
      gateState = 2;
      gate.write(0);
    }
    
  } else if (gateState == -2) {
    if (dExit >= DIST_THRESHOLD) {
      gateState = 0;
    }
    
  } else if (gateState == 2) {
    if (dEntrance >= DIST_THRESHOLD) {
      gateState = 0;
    }
  }

  lcd.setCursor(0, 0);
  if (slotsAvailable == 0) {
    lcd.print("Parking Full  ");
  } else {
    lcd.print("Parking left ");
    lcd.print(slotsAvailable);
  }
  

  if (d1 > DIST_THRESHOLD && d2 > DIST_THRESHOLD && d3 > DIST_THRESHOLD) 
  {
    lcd.setCursor(0, 1);
    lcd.print("Slot 1 2 3 Free");
    delay(500);
  } 
  
  else if ((d1 > DIST_THRESHOLD && d2 > DIST_THRESHOLD) || (d2 > DIST_THRESHOLD && d3 > DIST_THRESHOLD) || (d3 > DIST_THRESHOLD && d1 > DIST_THRESHOLD)) 
  {
    lcd.setCursor(0, 1);
    if (d1 > DIST_THRESHOLD && d2 > DIST_THRESHOLD)     
    lcd.print("Slot 1 & 2 Free");
    
    else if (d1 > DIST_THRESHOLD && d3 > DIST_THRESHOLD) 
    lcd.print("Slot 1 & 3 Free");
    
    else                                                
    lcd.print("Slot 2 & 3 Free");
    
  } else if (d1 < DIST_THRESHOLD && d2 < DIST_THRESHOLD && d3 < DIST_THRESHOLD) {
    lcd.setCursor(0, 1);
    lcd.print("Parking Full   ");
    
  } else if ((d1 < DIST_THRESHOLD && d2 < DIST_THRESHOLD) ||
             (d2 < DIST_THRESHOLD && d3 < DIST_THRESHOLD) ||
             (d3 < DIST_THRESHOLD && d1 < DIST_THRESHOLD)) 
             {
    lcd.setCursor(0, 1);
    if (d1 > DIST_THRESHOLD)
    lcd.print("Slot 1 is Free ");
    
    else if (d2 > DIST_THRESHOLD) 
    lcd.print("Slot 2 is Free ");
    
    else 
    lcd.print("Slot 3 is Free ");
  }

  delay(100);
}

## 🧰 Technologies Used
1. Arduino IDE (C/C++)
2. Tinkercad Simulator
3. IoT Sensors (HC-SR04)
4. Embedded Systems Concepts

## 🔮 Future Enhancements

1. Mobile app integration for real-time slot booking
2. Online payment system for parking fees
3. Cloud integration for storing and analyzing parking data
4. AI-based prediction of parking demand


