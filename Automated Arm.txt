#include <Wire.h>
#include <Adafruit_INA219.h>

#define SleepMot1 2
#define SleepMot2 2
#define SleepMot3 3
#define SleepMot4 3
#define SleepMot5 4

#define PinMotA1 5
#define PinMotA2 6
#define PinMotB1 9
#define PinMotB2 10

#define Bout1 7
#define Bout2 8
#define Bout3 11
#define Bout4 12
#define Bout5 13

Adafruit_INA219 ina219_A(0x40);
Adafruit_INA219 ina219_B(0x41);
Adafruit_INA219 ina219_C(0x44);
Adafruit_INA219 ina219_D(0x45);

int N=0;

void setup()
{
  ina219_B.begin();
  ina219_D.begin();
  ina219_A.begin();  // Initialize first board (default address 0x40)
  ina219_C.begin();
  Serial.begin(9600);
  Serial.println("test");
  pinMode(Bout1,INPUT);
  pinMode(Bout2,INPUT);
  pinMode(Bout3,INPUT);
  pinMode(Bout4,INPUT);
  pinMode(Bout5,INPUT);
  pinMode(SleepMot1, OUTPUT);
  pinMode(SleepMot3, OUTPUT);
  pinMode(SleepMot5, OUTPUT);
  pinMode(PinMotA1, OUTPUT);
  pinMode(PinMotA2, OUTPUT);
  pinMode(PinMotB1, OUTPUT);
  pinMode(PinMotB2, OUTPUT);
  digitalWrite(PinMotA1, LOW);
  digitalWrite(PinMotA2, LOW);
  digitalWrite(PinMotB1, LOW);
  digitalWrite(PinMotB2, LOW);
  digitalWrite(SleepMot1, HIGH);
  digitalWrite(SleepMot3, LOW);
  digitalWrite(SleepMot5, LOW);
}

void loop()
{
  if (digitalRead(Bout1)==true)
    {
      Moteur("AVA");
    }
   else if (digitalRead(Bout2)==true)
    {
      Moteur("ARI");
    }
   else if (digitalRead(Bout3)==true)
    {
      Moteur("BVA");
    }
   else if (digitalRead(Bout4)==true)
    {
      Moteur("BRI");
    }
   else if (digitalRead(Bout5)==true)
    {
      while(digitalRead(Bout5)==true)
      {
        delay(1);
      }
      N++;
      if (N>=3)
      {
        N=0;
      }
      if (N==0)
        {
          digitalWrite(SleepMot5,false);
          digitalWrite(SleepMot1,true);
        }
      else if (N==1)
        {
          digitalWrite(SleepMot1,false);
          digitalWrite(SleepMot3,true);
        }      
      else if (N==2)
        {
          digitalWrite(SleepMot3,false);
          digitalWrite(SleepMot5,true);
        }
    }
   else
    {
      Moteur("ARE");
      Moteur("BRE");
    }
}


int Moteur(int A)
{
  Serial.println(Courant());
  if (Courant()==0)
  {
    digitalWrite(PinMotA1, LOW);
    digitalWrite(PinMotA2, LOW); 
    digitalWrite(PinMotB1, LOW);
    digitalWrite(PinMotB2, LOW);
    delay(10);
  }
  if (A == "AVA")
  {
    digitalWrite(PinMotA1, HIGH);
    digitalWrite(PinMotA2, LOW);
  }
  else if (A == "ARI")
  {
    digitalWrite(PinMotA1, LOW);
    digitalWrite(PinMotA2, HIGH);
  }
  else if (A == "ARE")
  {
    digitalWrite(PinMotA1, LOW);
    digitalWrite(PinMotA2, LOW);
  }
  else if (A == "BVA")
  {
    digitalWrite(PinMotB1, HIGH);
    digitalWrite(PinMotB2, LOW);
  }
  else if (A == "BRI")
  {
    digitalWrite(PinMotB1, LOW);
    digitalWrite(PinMotB2, HIGH);
  }
  else if (A == "BRE")
  {
    digitalWrite(PinMotB1, LOW);
    digitalWrite(PinMotB2, LOW); 
  }

}

float Courant()
{
  int current_mAB=0;
    current_mAB = ina219_B.getCurrent_mA();
    int current_mAD = ina219_D.getCurrent_mA();
    int current_mAA = ina219_A.getCurrent_mA();
    int current_mAC = ina219_C.getCurrent_mA();
if (current_mAB>200 or current_mAD>300 or current_mAA>200 or current_mAC>200)
  {
    return false;
  }
else 
  {
    return true;
  }
}
