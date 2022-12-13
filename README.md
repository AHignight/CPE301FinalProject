# CPE301FinalProject
#include <dht_nonblocking.h>
#include <Adafruit_Sensor.h>
#include <DHT.h>
#include <DHT_U.h>

#include <LiquidCrystal.h>
LiquidCrystal lcd(12,11,5,4,3,2); 
#define DHT_SENSOR_TYPE DHT_TYPE_11

int DHT_SENSOR_PIN = 9;
DHT_nonblocking dht_sensor(DHT_SENSOR_PIN,DHT_SENSOR_TYPE);

float temp;
float humd;
  
void setup() { 
  Serial.begin(9600);
  lcd.begin(16, 2);  
} 
  
void loop(){ 
  if(measure_environment(&temp, &humd) == true)
  {
    float op1 = temp*1.8;
    float op2 = op1 + 32;
    lcd.setCursor(0,0);
    lcd.print("Temperature=");
    lcd.print(op2,1);
    lcd.println("F");
    lcd.setCursor(0,1);
    lcd.print("Humidity = ");
    lcd.print(humd,1);
    lcd.println("% ");
    //6000 is 1 min, change around to test
    delay(500); 
  }
}


static bool measure_environment(float *temp, float *humd)
{
  static unsigned long measurement_timestamp=millis();
  if(millis()-measurement_timestamp>3000ul)
  {
    if(dht_sensor.measure(temp,humd) == true)
    {
      measurement_timestamp = millis();
      return(true);
    }
  }
  return(false);
}
