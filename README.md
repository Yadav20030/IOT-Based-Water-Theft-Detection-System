# IOT-Based-Water-Theft-Detection-System
Technologies: Arduino Uno, Flow Sensors, GSM Module (SIM800L), Relay, Solenoid Valve, Embedded C

• Developed an IoT-based system to detect water theft and leakage in pipelines using flow sensors

• Compared input and output water flow using Arduino Uno to identify abnormal usage

• Integrated GSM module to send real-time SMS alerts during theft detection

• Designed an automatic solenoid valve system to stop water supply when theft is detected

• Built a working hardware prototype for real-time demonstration
PROGRAM

#include <SoftwareSerial.h>


SoftwareSerial gsm(7, 8);  // RX, TX


#define FLOW_IN 2

#define FLOW_OUT 3

#define RELAY 9


volatile int inPulse = 0;

volatile int outPulse = 0;

void inFlow()  { inPulse++; }

void outFlow() { outPulse++; }


void setup() {
  Serial.begin(9600);
  
  gsm.begin(9600);
  

  pinMode(FLOW_IN, INPUT);
  
  pinMode(FLOW_OUT, INPUT);
  
  pinMode(RELAY, OUTPUT);
  

  attachInterrupt(digitalPinToInterrupt(FLOW_IN), inFlow, RISING);
  
  attachInterrupt(digitalPinToInterrupt(FLOW_OUT), outFlow, RISING);
  

  digitalWrite(RELAY, LOW);
  
}

void loop() {
  inPulse = 0;
  outPulse = 0;

  delay(5000);

  int difference = inPulse - outPulse;

  Serial.print("Input: ");
  
  Serial.println(inPulse);
  
  Serial.print("Output: ");
  
  Serial.println(outPulse);

  if (difference > 20) {
    digitalWrite(RELAY, HIGH);

    gsm.println("AT+CMGF=1");
    delay(500);
    gsm.println("AT+CMGS=\"+91XXXXXXXXXX\"");
    delay(500);
    gsm.print("Water theft/leakage detected!");
    gsm.write(26);

    Serial.println("ALERT: Water theft detected!");
  }
  else {
    digitalWrite(RELAY, LOW);    // Open valve
  }
}
