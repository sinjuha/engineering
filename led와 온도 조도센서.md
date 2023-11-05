앱 인벤터: 

![제목 없음](https://github.com/sinjuha/engineering/assets/127822620/894e5b11-24b9-45d6-8789-4886960cc22c)

아두이노 코드: 

int ledPin = 13;
int photoPin = A1;
int tempPin = A0;

void setup() {
  Serial.begin(9600);
  pinMode(ledPin, OUTPUT);
}

double th(int v) {
  double t;
  t = log(((10240000.0/v) - 10000.0));
  t = 1 /(0.001129148 + (0.000234125*t) + (0.0000000876741*t*t*t));
  t = t - 273.15;
  return t;
}

void loop() {
  if (Serial.available() > 0) {
    char command = Serial.read();
    if (command == '1') {
      digitalWrite(ledPin, HIGH);
    } else if (command == '0') {
      digitalWrite(ledPin, LOW);
    }
  }
  
  int photoValue = analogRead(photoPin);
  double tempValue = th(analogRead(tempPin));
  
  Serial.print("P:");
  Serial.print(photoValue);
  Serial.print(", T:");
  Serial.println(tempValue);
  
  delay(1000);
}

프로세싱 코드:

import processing.net.*;
import processing.serial.*;
Server s;
Client c;
Serial p;
void setup() {
  s = new Server(this, 12345);
  p = new Serial(this, "COM3", 9600);
}
String msg="hi";
void draw() {
  c = s.available();
  if (c!=null) {
    String m = c.readString(); 
    if (m.indexOf("GET /")==0) {
      c.write("HTTP/1.1 200 OK\r\n\r\n");
      c.write(msg);
    }
    if (m.indexOf("PUT /")==0) {
      int n = m.indexOf("\r\n\r\n")+4;
      m = m.substring(n); 
      m += '\n';           
      p.write(m);
      print(m);
      c.write("HTTP/1.1 200 OK\r\n\r\n");
    }
    c.write(msg);
    c.stop();
  }
  if (p.available()>0) {
    String m = p.readStringUntil('\n');
    if (m!=null)  msg = m;
    print(msg);
   }
}
