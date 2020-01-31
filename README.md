# http-request-ultra
In this project ultrasonic data send to Http server.
#include <SoftwareSerial.h>
#include <String.h>

SoftwareSerial mySerial(10, 11);
#define trigpin A0
#define echopin A1
long distance,duration;

void setup()
{
mySerial.begin(9600);               // the GPRS baud rate  
Serial.begin(9600);    // the GPRS baud rate
pinMode(trigpin,OUTPUT);
pinMode(echopin,INPUT);
delay(100);
}

void loop()
{
digitalWrite(trigpin,LOW);
delay(100);
digitalWrite(trigpin,HIGH);
delay(100);
digitalWrite(trigpin, LOW);
duration = pulseIn(echopin, HIGH);
distance = (duration/2) / 29.1;
Send2Pachube();
Serial.println(distance);

if (mySerial.available())
Serial.write(mySerial.read());
}
void Send2Pachube()
{
mySerial.println("AT");
delay(1000);

mySerial.println("AT+CREG?");
delay(1000);
ShowSerialData();
mySerial.println("AT+CPIN?");
delay(1000);
 ShowSerialData();

mySerial.println("AT+HTTPINIT");
delay(1000);
ShowSerialData();

 mySerial.println("AT+SAPBR=3,1,Contype,GPRS");
delay(1000);
ShowSerialData();

mySerial.println("AT+SAPBR=3,1,APN,portalnmms");
delay(1000);
ShowSerialData();


mySerial.println("AT+SAPBR=2,1");
delay(1000);
ShowSerialData();

mySerial.println("AT+SAPBR=1,1");
delay(1000);
ShowSerialData();


mySerial.println("AT+HTTPPARA=CID,1");
delay(1000);
ShowSerialData();

mySerial.println("AT+HTTPPARA=\"URL\",\"http://5d4a1561.ngrok.io/requestpost?page="+String(distance)+"&mobile=9918365421\"");
delay(2000);
ShowSerialData();

mySerial.println("AT+HTTPACTION=1");
delay(2000);
ShowSerialData();

//mySerial.println("AT+HTTPPARA=CONTENT, application/json");
// delay(2000);
//ShowSerialData();

mySerial.println("AT+HTTPREAD=1");
delay(2000);
ShowSerialData();




mySerial.println("AT+HTTPTERM");
delay(2000);
ShowSerialData();

mySerial.println("AT+SAPBR=0,1");
delay(1000);
ShowSerialData();

mySerial.println((char)26);//sending
delay(1000);//waitting for reply, important! the time is base on the condition of internet
mySerial.println();

ShowSerialData();


}
void ShowSerialData()
{
while(mySerial.available()!=0)
  Serial.write(mySerial.read());
}
