\#include \<ESP8266WiFi.h\> \#include \<BlynkSimpleEsp8266.h\> \#include
\<Servo.h\>

char auth\[\] = "Su4MJy-PB602LV1S4lLr3X1ep20xTNIC"; // Blynk auth token
char ssid\[\] = "wifi"; // Wi-Fi name char pass\[\] = "abcdefgh"; //
Wi-Fi password

Servo myservo; // create servo object

\#define trigPin D6 \#define echoPin D5

int motorPos = 0; // motor position variable

void setup() { Serial.begin(9600); Blynk.begin(auth, ssid, pass);
myservo.attach(D7); // attach servo to pin D7 pinMode(trigPin, OUTPUT);
pinMode(echoPin, INPUT); }

int ultradistcal(){ long duration, cm;

digitalWrite(trigPin, LOW); delayMicroseconds(2); digitalWrite(trigPin,
HIGH); delayMicroseconds(10); digitalWrite(trigPin, LOW);

duration = pulseIn(echoPin, HIGH); cm = (duration / 2) / 29.1; //
convert to centimeters return cm; }

void loop() {

int pres_dist = ultradistcal(); Serial.print("Distance: ");
Serial.print(pres_dist); Serial.println(" cm");

// if (pres_dist \< 20) { // motorPos = 0; // if distance is less than
20, set motor position to 0 // myservo.write(motorPos); // move motor to
zero position // }

if (Blynk.connected()) { Blynk.run(); } delay(2500); }

BLYNK_WRITE(V3) { int motorSwitch = param.asInt(); if (motorSwitch == 1
) { // if virtual switch is turned on

motorPos = 0; // set motor position to 90 degrees (middle position)
myservo.write(motorPos); // move motor to middle position

int new_dist = ultradistcal();

while(new_dist \>= 20 && motorSwitch == 1 ){ delay(1000); new_dist =
ultradistcal(); } motorPos = 90; // set motor position to 0 degrees
(zero position) myservo.write(motorPos); // move motor to zero position

} else { // if virtual switch is turned off motorPos = 90; // set motor
position to 0 degrees myservo.write(motorPos); // move motor to zero
position } }
