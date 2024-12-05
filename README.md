#include <Servo.h>

// Pin definitions
const int sensorPin = A0;  // Gas sensor connected to analog pin A0
const int relayPin = 9;    // Relay module connected to digital pin 9
const int servoPin = 10;   // Servo motor connected to PWM pin 10
const int ledPin = 13;     // LED connected to digital pin 13

// Threshold for gas detection
const int gasThreshold = 400;

// Servo object
Servo valveServo;

void setup() {
  pinMode(relayPin, OUTPUT); // Set relay pin as output
  pinMode(ledPin, OUTPUT);   // Set LED pin as output
  digitalWrite(relayPin, LOW); // Turn relay ON initially (power ON)
  digitalWrite(ledPin, HIGH);  // Turn LED ON initially (normal state)

  valveServo.attach(servoPin); // Attach the servo motor
  valveServo.write(0);         // Set valve to closed position (0 degrees)

  Serial.begin(9600);          // Initialize serial communication for debugging
}

void loop() {
  int gasValue = analogRead(sensorPin); // Read the gas sensor value
  Serial.println(gasValue);            // Print the value to Serial Monitor for debugging

  if (gasValue > gasThreshold) {
    // Gas leakage detected
    digitalWrite(relayPin, HIGH); // Turn OFF relay (simulate power cut)
    digitalWrite(ledPin, LOW);    // Turn LED OFF (warning state)
    valveServo.write(90);         // Rotate servo to 90 degrees (close gas valve)
    delay(2000);                  // Allow time for valve to close
  } else {
    // No gas leakage
    digitalWrite(relayPin, LOW);  // Turn ON relay (power ON)
    digitalWrite(ledPin, HIGH);   // Turn LED ON (normal state)
    valveServo.write(0);          // Rotate servo to 0 degrees (open gas valve)
  }

  delay(500); // Stabilize sensor readings
}
