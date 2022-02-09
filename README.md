// SmartWatch 
// Zoe Robinson


// States
#include "tinysnore.h"  // Include TinySnore Library
int mode = -1;          
int numPixel = 0;

// NeoPixels
#include <Adafruit_NeoPixel.h>
Adafruit_NeoPixel strip(12, 0, NEO_GRB + NEO_KHZ800);

// Buttons
#include <Bounce2.h>
int buttonPins[2] = {1, 2};
Bounce * buttons = new Bounce[2];

// Time
int curHours = 0;
int curMinutes = 0;
int curSeconds = 0;

int buttonOnePressed = 0;
int buttonTwoPressed = 0;

long sleepTimer = 0;
long sleepInterval = 5000;


void setup() {
  initButtons();
  initNeopixels();
  updateTime();
  //fallAsleep();

}

void loop() {
  updateTime();
  updateButtons();
  showTime();
  
}

void showTime() {
  strip.clear();
  if(((curSeconds / 20) + 1 + buttonOnePressed - buttonTwoPressed) < 0) {
    numPixel = 0;
  }
  else {
    numPixel = ((curSeconds / 20) + 1 + buttonOnePressed - buttonTwoPressed) % 12;
  }
  for (int i = 0; i <= numPixel; i++) {
    strip.setPixelColor(i, 255, 255, 255);
  }
  if (numPixel == 5) {
    for (int i = 0; i <= numPixel; i++) {
      strip.setPixelColor(i, 255, 165, 0);z
    }
  }
  if (numPixel == 7) {
    for (int i = 0; i <= numPixel; i++) {
      strip.setPixelColor(i, 0, 0, 255);
    }
  }
  strip.show();
}

void initButtons() {
  for (int i = 0; i < 2; i++) {
    buttons[i].attach(buttonPins[i], INPUT);
    buttons[i].interval(25);
  }
}

void updateButtons() {
  //for (int i = 0; i < 2; i++)  {
  buttons[0].update();
  buttons[1].update();

  if (buttons[0].rose()) {
    buttonOnePressed++;
  }

  if(buttons[1].rose()){
    buttonTwoPressed++;
  }
  

  strip.show();

}

// NeoPixel Functions

void initNeopixels(){
  strip.begin();
  strip.clear();
  strip.show();
  strip.setBrightness(10);
}

void allNeopixels(int c){
  for (int i = 0; i < strip.numPixels(); i++){
    strip.setPixelColor(i, Wheel(c));
  }
  strip.show();
  mode = -1;
}

uint32_t Wheel(byte WheelPos) {
  WheelPos = 255 - WheelPos;
  if(WheelPos < 85) {
    return strip.Color(255 - WheelPos * 3, 0, WheelPos * 3);
  }
  if(WheelPos < 170) {
    WheelPos -= 85;
    return strip.Color(0, WheelPos * 3, 255 - WheelPos * 3);
  }
  WheelPos -= 170;
  return strip.Color(WheelPos * 3, 255 - WheelPos * 3, 0);
}
         

void fallAsleep(){
  if(millis() - sleepTimer > sleepInterval){
    strip.clear(); strip.show();  // Clear out NeoPixel Display
    snore(5000);                 // Go to Sleep for 5 Seconds
    sleepTimer = millis();
  }
}
