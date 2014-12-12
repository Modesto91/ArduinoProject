ArduinoProject
==============

Final Project for my associates Degree. Involved an Arduino and an RGB LED Matrix
#include <Adafruit_GFX.h>
#include <RGBmatrixPanel.h>

#define CLK 50  // MUST be on 50 for Mega
#define LAT A3
#define OE  51  //Must be 51 for Mega
#define A   A0
#define B   A1
#define C   A2
RGBmatrixPanel matrix(A, B, C, CLK, LAT, OE, false);

char   str[]   = "ET2799 Capstone Project - Blank Line Game!!";  //Intro Text
int    textX   = matrix.width(),
textMin = sizeof(str) * -12;
long   hue     = 0;
int counter = 500; //How long to Scroll Intro

int buttonPin =  4; //The Button Pin
int buttonState = 0; //The state of the Button High or low
int buttonCount = 1; //Keep track of tries...
int StartUp=100; //??????
int tempo= 175; //How Fast is the Blue
int speedincrease = 10; //How Fast increase for Correct hit

int r=0, g=0, b=2; //RGB Colors for Blue Line
int x = 0;  //Pixel Position
int y = 0;  //Pixel Positon

void setup() {

  pinMode(buttonPin, INPUT); //Set Button as Input to read

  matrix.begin();
  matrix.setTextWrap(false); // Allow text to run off right edge
  matrix.setTextSize(2);
  //Begin Scroll Loop
  for(int scr=0; scr<counter; scr++){
    byte i;

    // Clear background
    matrix.fillScreen(0);

    // Draw big scrolly text on top
    matrix.setTextColor(matrix.ColorHSV(hue, 255, 255, true));
    matrix.setCursor(textX, 1);
    matrix.print(str); //Print the Text

    // Move text left (w/wrap), increase hue
    if((--textX) < textMin) textX = matrix.width();
    delay(15); //How Fast the Text Scrolls
    hue += 7; //How Pretty the Text IS

    // Update display
    matrix.swapBuffers(false);  
  }
  //END Scroll Loop
  delay(1000);
  matrix.fillScreen(0); //Blank the Screen with Black before we start the Game

  //New Text here for Game begins in 3,2,1...

  //Draw First Rect
  matrix.fillRect(0, 0, 32, 16, matrix.Color333(7, 0, 0)); //RED SQUARE Whole Board
  matrix.fillRect(0, 4, 32, 8, matrix.Color333(4, 2, 0)); //Yellow Square Middle
  matrix.fillRect(0, 7, 32, 2, matrix.Color333(0, 7, 0)); //Green Square

  //Erases Bottom to top Initial Erase Prepare for Blue Line
  for (int y = 16; y>0; y--){
    matrix.drawPixel(x, y, matrix.Color333(0,0,0 ));
    delay (tempo);
  }
}


void loop() {

  //Draws Blue from Top to Bottom
  for (int y=0; y < 16; y++) { 
    buttonState=digitalRead(buttonPin);  //Read Button
    if(buttonState==LOW&&(y==7||y==8)) //If Button Pressed 
    {
      matrix.drawLine(buttonCount, 0, buttonCount, 16, matrix.Color333(0, 0, 0)); //Erase Line
      buttonCount++; //Increment Button Count
      tempo = tempo - speedincrease;  //Speed Up if Pressed on green..
    }
    else
    {
      //What Else if no press...
    }
    matrix.drawPixel(x, y, matrix.Color333(r, g, b )); //Draw Blue Line
    delay (tempo);
  }
  //Erases bottom to Top
  for (int y = 16; y>0; y--){
    buttonState=digitalRead(buttonPin); 
    if(buttonState==LOW&&(y==7||y==8))
    {
      matrix.drawLine(buttonCount, 0, buttonCount, 16, matrix.Color333(0, 0, 0));
      buttonCount++;
      tempo = tempo - speedincrease; //Speed Up if Pressed on Green
    }
    else
    {
      //What Else if no press
    }
    matrix.drawPixel(x, y, matrix.Color333(0,0,0 ));
    delay (tempo);
  }
}
