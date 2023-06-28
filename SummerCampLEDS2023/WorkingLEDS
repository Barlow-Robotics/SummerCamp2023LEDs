#include <FastLED.h>

#define NUM_LEDS 62
#define LED_PIN 2
#define COLOR_ORDER GRB  //GREEN RED BLUE DO NOT CHANGE
#define CHIPSET WS2812B
#define BRIGHTNESS 30
#define FRAMES_PER_SECOND 120
// #define REDALLIANCE 2   //currentMode & 00000010 == 00000010
// #define BLUEALLIANCE 0  //currentMode & 00000010 == 00000000
// #define ENABLED 8       //currentMode & 00001000 == 00001000
// #define ISSHOOTING 1   // currentMode & 00000001 == 00000001
// #define AUTO 4         //currentMode & 00000100 == 00000100

uint8_t hue = 0;
uint8_t currentMode = 0x00;
CRGB leds[NUM_LEDS];
CRGB Black = CRGB(0, 0, 0);
CRGB RedAlliance = CRGB(200, 0, 0);
CRGB BlueAlliance = CRGB(0, 0, 255);
CRGB Shooting = CRGB(100, 0, 100);
CRGB Auto = CRGB(100, 200, 100);
CRGB Enabled = CRGB(0, 255, 15);


class Glitter {

private:
  CRGB* leds;
  int num_leds;
  int num_glitters;
  float fadeValue;

  struct GlitterStatus {
    int index;
    int value;
  };

  GlitterStatus glitterStatus[100];

public:
  Glitter(CRGB* l, int num_leds, int num_glitters, float fadeValue) {
    this->leds = l;
    this->num_leds = num_leds;
    this->num_glitters = num_glitters;
    this->fadeValue = fadeValue;

    for (int i = 0; i < num_glitters; i++) {
      glitterStatus[i].value = 0;
    }
  }

  void Update() {
    for (int i = 0; i < num_glitters; i++) {
      float v = glitterStatus[i].value;
      float nv = v * fadeValue;
      uint8_t iv = (uint8_t)nv;
      glitterStatus[i].value = iv;

      if (glitterStatus[i].value < 4) {
        uint16_t led_num = random16(num_leds);
        uint8_t brightness = random8(255);
        glitterStatus[i].index = led_num;
        glitterStatus[i].value = brightness;
      }
    }
  }

  void Draw() {
    for (int i = 0; i < num_glitters; i++) {
      uint8_t value = glitterStatus[i].value;
      leds[glitterStatus[i].index] = CRGB(value, value, value);
    }
  }
};

Glitter theGlitter(leds, 47, 20, 0.8);


class breathe {
  private:
    CRGB* leds;
    int num_leds;
    int effected_leds;
    float fadeValue;
    int lineOneToGo;
    int lineTwoToGo;
    int effectedPerSide;
    int middle_led;
    int changed_leds;
    bool state;
    int lineOne;
    int lineTwo;

  public:
    breathe(CRGB* l, int num_leds, int effected_leds, float fadeValue) {
      this->leds = l;
      this->num_leds = num_leds;
      this->effected_leds = effected_leds;
      this->fadeValue = fadeValue;
      effectedPerSide = floor(effected_leds/2);
      middle_led = floor(num_leds/2);
      changed_leds = middle_led-effected_leds;
      lineOneToGo = changed_leds;
      lineTwoToGo = middle_led + effected_leds;
      state = true; 
      lineOne = middle_led;
      lineTwo = middle_led;
    }
  

    void update() {

      FastLED.show();
      if (lineOne <= lineOneToGo && lineTwo >= lineTwoToGo) {
        state = true;
      } 
      if (lineOne >= middle_led && lineTwo <= middle_led) { 
        state = false; 
      }

      if (state) {
        leds[lineOne] = Enabled;
        leds[lineTwo] = Enabled; 
        lineOne++;
        lineTwo--;
      }
      else {
        leds[lineOne] = Black; // fix, custom 
        leds[lineTwo] = Black; 
        lineOne--;
        lineTwo++;
      }
    
}

      // only use effected_Leds\
      / start from middle
      // extend
      // go back; slow down as you reach the end

};

breathe theBreathing (leds, 47, 20, 0.8);


// class breathe {
//   private:
//     CRGB* leds;
//     int num_leds;
//     int effected_leds;
//     float fadeValue;

//     struct breathedStatus {
//       int index;
//       int value;
// //     }
// //   public:
// //     breathe(CRGB* 1, int num_leds, int effected_leds, float fadeValue) {
// //       this->leds 1
// //     }

// // }


void setup() {
  FastLED.addLeds<CHIPSET, LED_PIN, COLOR_ORDER>(leds, NUM_LEDS);
  FastLED.setBrightness(BRIGHTNESS);
  FastLED.clear();
  FastLED.show();

  Serial.begin(9600);
  Serial.setTimeout(0);
}

int segmentLength = 1;
int sL2 = NUM_LEDS - segmentLength;
int start = -1;
int direction = 1;
int modifier = 1;
uint8_t buffer[10];
int numRead;


void loop() {
  // currentMode = 0b00000000;
  bool redAlliance = (currentMode & 0b00000010) == 0b00000010;
  bool blueAlliance = (currentMode & 0b00000010) == 0b00000000;
  bool enabled = (currentMode & 0b00001000) == 0b00001000;
  bool isShooting = currentMode & 0b00000001 == 0b00000001;
  // bool (isShooting = currentMode % 2) == 1; 
  bool autoActivated = (currentMode & 0b00000100) == 0b00000100;

  if (isShooting) {
    for (int i = 0; i < NUM_LEDS; i++) {
      leds[i] = Shooting;
    }
  } else if (autoActivated) {
    for (int i = 0; i < NUM_LEDS; i++) {
      leds[i] = Auto;
    }
  }
    else if (!enabled) {
    for (int i = 0; i < NUM_LEDS; i++) {
      Serial.println("a");
      leds[i] = Enabled;
    }
  } else if (redAlliance) {  //red = 1 = 2 to the power of 1
    for (int i = 0; i < NUM_LEDS; i++) {
      leds[i] = RedAlliance;
    }
  } else if (blueAlliance) {  //blue = 0
    for (int i = 0; i < NUM_LEDS; i++) {
      leds[i] = BlueAlliance;
      // (!enabled);
    }
  } 
  else {
    for (int i = 0; i < NUM_LEDS; i++) {
      leds[i] = Enabled;
    }
  }
  FastLED.show();
  
 // theGlitter.Draw();


  EVERY_N_MILLISECONDS(50) {
    // theGlitter.Update();
    theBreathing.update();
    // currentMode = AUTO ;
    while (Serial.available() > 0) {
      // read the incoming byte:
      // Serial.readBytes(buffer, 1) ;
      uint8_t incomingByte = Serial.read();
      // Serial.print("byte is ") ;
      // Serial.println(incomingByte) ;
      if (incomingByte >= 1 && incomingByte <= 15) {
        currentMode = incomingByte;
      }
      // if (buffer[0] >= 1 && buffer[0] <= 5) {
      //   currentMode = buffer[0];
      // }
      // }
    }
    FastLED.show();
  }
}
