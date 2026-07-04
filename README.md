#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include "BluetoothSerial.h"

BluetoothSerial SerialBT;
LiquidCrystal_I2C lcd(0x27,16,2);

//============================
// Pin Definitions
//============================
#define MQ3_PIN     34
#define RED_LED     26
#define GREEN_LED   27
#define BUZZER      25

//============================
// Variables
//============================
int alcoholValue = 0;
int threshold = 1800;

bool alertSent = false;

void setup()
{
  Serial.begin(115200);

  SerialBT.begin("SoberDrive");

  pinMode(RED_LED,OUTPUT);
  pinMode(GREEN_LED,OUTPUT);
  pinMode(BUZZER,OUTPUT);

  digitalWrite(RED_LED,LOW);
  digitalWrite(GREEN_LED,HIGH);
  digitalWrite(BUZZER,LOW);

  lcd.init();
  lcd.backlight();

  lcd.setCursor(0,0);
  lcd.print("SoberDrive");

  lcd.setCursor(0,1);
  lcd.print("Initializing");

  delay(3000);

  lcd.clear();
}

void loop()
{

  alcoholValue = analogRead(MQ3_PIN);

  Serial.println(alcoholValue);

  lcd.setCursor(0,0);
  lcd.print("Alcohol:");

  lcd.setCursor(9,0);
  lcd.print("     ");

  lcd.setCursor(9,0);
  lcd.print(alcoholValue);

  if(alcoholValue > threshold)
  {

      digitalWrite(RED_LED,HIGH);
      digitalWrite(GREEN_LED,LOW);

      digitalWrite(BUZZER,HIGH);

      lcd.setCursor(0,1);
      lcd.print("DRUNK DETECTED ");

      if(!alertSent)
      {
          SerialBT.println("ALERT");
          Serial.println("Bluetooth ALERT Sent");
          alertSent=true;
      }

  }

  else
  {

      digitalWrite(RED_LED,LOW);
      digitalWrite(GREEN_LED,HIGH);

      digitalWrite(BUZZER,LOW);

      lcd.setCursor(0,1);
      lcd.print("SAFE DRIVER    ");

      alertSent=false;

      SerialBT.println("SAFE");

  }

  delay(500);

}
