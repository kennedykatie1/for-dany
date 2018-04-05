/*
   If you use the serial monitor for debugging remember to ster the baud rate to 115200.
   The modules are set to only one-way communication. That means that the controller can only transmit and the tank can only receive.
   If you wish to change this for two-way communication there is plenty of documentation to do so on the internet and in examples.

   This program receives the X and Y values and processes it into movement.
*/
#include <SPI.h>
#include "RF24.h"

RF24 myRadio (0, 8);
struct package
{
  int X = 512;
  int Y = 512;
};

byte addresses[][6] = {"124"};
int E1 = 5;
int M1 = 4;
int E2 = 6;
int M2 = 7;


typedef struct package Package;
Package data;
//const int trigPin = 9;
//const int echoPin = 10;
//// defines variables
//long duration;
//int distance;
void setup()
{
  Serial.begin(115200);
  delay(1000);
//pinMode(trigPin, OUTPUT); // Sets the trigPin as an Output
//pinMode(echoPin, INPUT); // Sets the echoPin as an Input
  myRadio.begin();
  myRadio.setChannel(115);
  myRadio.setPALevel(RF24_PA_MAX);
  myRadio.setDataRate( RF24_250KBPS ) ;
  myRadio.openReadingPipe(1, addresses[0]);
  myRadio.startListening();
  pinMode(M1, OUTPUT);
  pinMode(M2, OUTPUT);
}


void loop()
{
//digitalWrite(trigPin, LOW);
//delayMicroseconds(2);
//// Sets the trigPin on HIGH state for 10 micro seconds
//digitalWrite(trigPin, HIGH);
//delayMicroseconds(10);
//digitalWrite(trigPin, LOW);
//// Reads the echoPin, returns the sound wave travel time in microseconds
//duration = pulseIn(echoPin, HIGH);
//// Calculating the distance
//distance= duration*0.034/2;
//// Prints the distance on the Serial Monitor
//Serial.print("Distance: ");
//Serial.println(distance);

  
  if ( myRadio.available())
  {
    while (myRadio.available())
    {
      myRadio.read( &data, sizeof(data) );
    }
    //    Serial.print("X:");
    //    Serial.print(data.X);
    //    Serial.print("      Y");
    //    Serial.println(data.Y);
    int X = data.X;
    int Y = data.Y;
if(X>520){
    int forward = map(X, 524, 1024, 0, 255);
      digitalWrite(M1, HIGH);
      analogWrite(E1, forward);   //PWM Speed Control
}
if(X<500){
  int backward = map(X, 500, 0, 0, 255);
      digitalWrite(M1, LOW);
      analogWrite(E1, backward);   //PWM Speed Control
}
if(Y>520){
  int forward2 = map(Y, 524, 1024, 0, 255);
      digitalWrite(M2, LOW);
      analogWrite(E2, forward2);  
}
if(Y<500){
  int backward2 = map(X, 500, 0, 0, 255);
      digitalWrite(M2, HIGH);
      analogWrite(E2, backward2);   //PWM Speed Control
}
   

    else if (X < 520 && X > 500 && Y < 520 && Y > 500) {
      digitalWrite(M1, LOW);
      digitalWrite(M2, LOW);
      analogWrite(E1, 0);   //PWM Speed Control
      analogWrite(E2, 0);   //PWM Speed Control
    }
    }
}
