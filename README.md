# FlightSimDongle
Used for Converting a CPPM signal from an RC Transmitter Trainer Port to a Game Controller Format. Tested with DCL and Liftoff on my Spektrum DX6i



//Aleksander Zbikowski
//July 3, 2019
// CPPM code was written with the help of Abhilash Patel's video on youtube: https://www.youtube.com/watch?v=7dfyhNpu2qY&t=141s
//Joystick.h library can be found on https://github.com/MHeironimus/ArduinoJoystickLibrary

//Had trouble running on Liftoff, but added USBAPI.h and downloaded  "Unsupported Standard Game Port" into the "Sound, Video, and Game Controllers" section in Device Manager
//Action->Add Legacy Hardware->Next->Install the hardware that I manually select from a list->Next->Sound, Video, and Game Controllers->Next->Aztech System->Unsupported Standard Game Port->Next
//Try without USBAPI.h, if it doesn't work, download Library

//#include <USBAPI.h>
//___________________________________________________________________________________________________________________________________________________________________________________________________

//CPPM for 6 channel
//Tested with DX6i

#include <Joystick.h>
Joystick_ Joystick;

int TH = 0;
int AIL = 0;
int ELE = 0;
int RUD = 0;
int AUX = 0;
int FL = 0;

unsigned long int a,b,c;
int x[15], ch1[15], ch[7], i;

const bool testAutoSendMode = true;

void setup() {
 if (testAutoSendMode)
  {
    Joystick.begin();
  }
  else
  {
    Joystick.begin(false);
  }
//Serial.begin(9600);
pinMode(2, INPUT);
attachInterrupt(digitalPinToInterrupt(2), read_me, FALLING);

  Joystick.setXAxisRange(0, 1023);
  Joystick.setYAxisRange(0, 1023);
  Joystick.setZAxisRange(0, 1023);
  Joystick.setRxAxisRange(0, 1023);

}

void loop() {
 read_rc();
 
 TH = ch[1];
 AIL = ch[2];
 ELE = map(ch[3],0,1023,1023,0);
 RUD = ch[4];
 AUX = ch[5];
 FL = ch[6];

  Joystick.setXAxis(AIL);
  Joystick.setYAxis(ELE);
  Joystick.setZAxis(RUD);
  Joystick.setRxAxis(TH);
  
  if(AUX > 650)
  {
   Joystick.pressButton(0);
  }
  else
  {
   Joystick.releaseButton(0);
  }
   if(FL > 650)
  {
    Joystick.pressButton(1);
  }
  else
  {
    Joystick.releaseButton(1);
  }
  //Uncomment to test values on Serial Monitor
    //Serial.print("TH");Serial.print(ch[1]);Serial.print("\t");
    //Serial.print("AIL");Serial.print(ch[2]);Serial.print("\t");
    //Serial.print("ELE");Serial.print(ch[3]);Serial.print("\t");
    //Serial.print("RUD");Serial.print(ch[4]);Serial.print("\t");
    //Serial.print("AUX");Serial.print(ch[5]);Serial.print("\t");
    //Serial.print("FL");Serial.print(ch[6]);Serial.print("\n");
 
}

void read_me()
{
  a=micros();
  c=a-b;
  b=a;
  x[i]=c;
  i++;
  if(i==15)
  {
    for(int j=0; j<15;j++)
    {
      ch1[j]=x[j];
    }
    i=0;
  }
}

void read_rc()
{
  int i,j,k=0;
  for(k=14;k>-1;k--)
  {if(ch1[k]>10000)
    {
    j=k;
    }
  }
  for(i=1;i<=6;i++)
  {
    ch[i]=(ch1[i+j]-1000);
  }
  if (testAutoSendMode == false)
    {
      Joystick.sendState();
    }
}
