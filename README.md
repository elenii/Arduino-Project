#include <LiquidCrystal.h>

const int ledRedPin = 9;//Red Colour Pin with ~
const int ledBluePin = 10;//Blue Colour Pin with ~
const int ledGreenPin = 11;//Green Colour Pin with ~

const int rs = 7, en = 6, d4 = 5, d5 = 4, d6 = 3, d7 = 2;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

const int buttonOk = 8;//pushbutton Pin
const int buttonLess = 12;//pushbutton Pin
const int buttonMore = 13;//pushbutton Pin

int buttonStateOk;
int buttonStateMore;
int buttonStateLess;

const int photoSensorPinFirst = 0;//Analog input Pin
const int photoSensorPinSecond = 1;//Analog input Pin

int lightLevelFirst;
int lightLevelSecond;

unsigned long timeStart;
unsigned long timePaused = 0;
unsigned long timePassed;
unsigned long timeTempOne;
unsigned long timeTempTwo;
bool isPaused = false;

bool isRunning = false;
bool firstRun = true;
int numberOfPeople = 1;
const char blank[17]={"                "};

void setup()
{
  //lcd setup colums/rows
  lcd.begin(16, 2);
  lcd.print("Welcome");
  //Pushbuttons pin inputs
  pinMode(buttonOk, INPUT);
  pinMode(buttonMore, INPUT);
  pinMode(buttonLess, INPUT);

  //LED Pin outputs
  pinMode(ledRedPin, OUTPUT);
  pinMode(ledBluePin, OUTPUT);
  pinMode(ledGreenPin, OUTPUT);
    
}

void loop()
{
  sensorReadings();

 if(buttonStateOk == HIGH && firstRun)
 {
  isRunning = true;
 }
  if(isRunning)
  {
    modeSelection();    
  }
	
  displaySwitch(1);
  if(buttonStateOk == HIGH && !isRunning)
 {
    theEnd();
 }
 
}

void theEnd()
{
  timePassed = millis()-timeStart -timePaused;
  timeTempOne = millis()-timeStart;
  timeTempTwo = timePassed/timeTempOne;
  timeTempTwo *= 100;
  displaySwitch(2);
  

  if(timeTempTwo<=40)
  {
    digitalWrite(ledRedPin, HIGH);    
  }
  if(timeTempTwo>40 && timeTempTwo<70)
  {
    digitalWrite(ledBluePin, HIGH);    
  }
  if(timeTempTwo>=70)
  {
    digitalWrite(ledGreenPin, HIGH);    
  }
}
void sensorReadings()
{
    buttonStateOk = digitalRead(buttonOk);
    buttonStateMore = digitalRead(buttonMore);
    buttonStateLess = digitalRead(buttonLess);
    lightLevelFirst = analogRead(photoSensorPinFirst);
    lightLevelSecond = analogRead(photoSensorPinSecond);
}

void checkLight()
{
  if(lightLevelFirst >=800 && !isPaused)
  {
    pauseEvent(isPaused);

    isPaused = true;
  }else{
        if(lightLevelFirst<800)
        {
          pauseEvent(isPaused);
          isPaused = false;
        }        
    }
  if(lightLevelSecond >=800 && !isPaused)
  {
    pauseEvent(isPaused);

    isPaused = true;
  }else{
        if(lightLevelSecond<800)
        {
          pauseEvent(isPaused);
          isPaused = false;
        }        
    }
}
 void pauseEvent(bool isPaused)
 {
  if(isPaused)
  {
    timeTempTwo = millis();

    timePaused += timeTempTwo-timeTempOne;
  }else
  {
    timeTempOne = millis();
  }  
 }


void displaySwitch(int mode)
{
  char temp[]="";
  switch(mode){
    case 0:    
    lcd.setCursor(0,0);
    //lcd.print(blank);
    lcd.print("Player Number:");   
    lcd.setCursor(0,1);
   // lcd.print(blank);
    lcd.print(numberOfPeople);
    lcd.display();
    break;
    
    case 1:
    lcd.setCursor(0,0);
	//lcd.print(blank);
    lcd.print("Time Passed:");
    lcd.setCursor(0,1);
   // lcd.print(blank);
    lcd.print(millis()-timeStart);
  	lcd.display();
    break;
    
    case 2:
    lcd.setCursor(0,0);
    //lcd.print(blank);
    lcd.print("Success Rate:");
    lcd.setCursor(0,1);
   // lcd.print(blank);
    lcd.print(timePassed);
  	lcd.display();
    break;
    
    default:  
    lcd.setCursor(0,0);
   // lcd.print(blank);
    lcd.setCursor(0,1);
   // lcd.print(blank);
  	lcd.display();
  }
    
}

void lightShow()
{
  digitalWrite(ledRedPin, HIGH);
  delay(100);
  digitalWrite(ledGreenPin, HIGH);
  delay(100);
  digitalWrite(ledBluePin, HIGH);
  delay(100);
 digitalWrite(ledRedPin, LOW);
  delay(100);
  digitalWrite(ledGreenPin, LOW);
  delay(100);
  digitalWrite(ledBluePin, LOW);  
}


void modeSelection()
{
  	
    if(firstRun)
    {
      lightShow();
      firstRun= false;
    }
  do{
    displaySwitch(0);
    if(buttonStateMore == HIGH && numberOfPeople<7)
    {
     numberOfPeople++;
    }
    
    if(buttonStateLess == HIGH && numberOfPeople>1)
    {
     numberOfPeople--;
    }
    
    if(buttonStateOk == HIGH) 
    {
     isRunning=false;
     timeStart = millis();
    }
  }while(isRunning);

}
