## 1. Create Project in your phone

1. download blynk phone app.
   [BLYNK FOR ANDROID](https://play.google.com/store/apps/details?id=cc.blynk)
   [BLYNK FOR IPHONE](https://itunes.apple.com/us/app/blynk-control-arduino-raspberry/id808760481?ls=1&mt=8)
2. Create a new account in Blynk App.
3. Create a New Project. Then choose the board and connection you will use.
4. After the project was created, we will send you Auth Token over email.
5. Check your email inbox and find the **Auth Token**.

## 2. Install Blynk library files for arduino

Download the latest release .zip file. [DOWNLOAD BLYNK LIBRARY](#)

1. Unzip it. 
2. Copy all these libraries to **your\_sketchbook\_folder **of **Arduino IDE**  
   To find the location of`your_sketchbook_folder`, go to top menu in Arduino IDE:

   Windows:`File → Preference`

   Mac OS:`Arduino → Preferences`

or you can use Arduino IDE to intall it.![](/assets/bylnk_lib_install.png)

## 3. Code of Wemos board

1. Power on Wemos board.
2. upload below code. The code is auto generated from [Blyink online tool](https://examples.blynk.cc/?board=WeMos D1&shield=ESP8266 WiFi&example=GettingStarted%2FBlynkBlink).

```
/*************************************************************
  Download latest Blynk library here:
    https://github.com/blynkkk/blynk-library/releases/latest

  Blynk is a platform with iOS and Android apps to control
  Arduino, Raspberry Pi and the likes over the Internet.
  You can easily build graphic interfaces for all your
  projects by simply dragging and dropping widgets.

    Downloads, docs, tutorials: http://www.blynk.cc
    Sketch generator:           http://examples.blynk.cc
    Blynk community:            http://community.blynk.cc
    Follow us:                  http://www.fb.com/blynkapp
                                http://twitter.com/blynk_app

  Blynk library is licensed under MIT license
  This example code is in public domain.

 *************************************************************

  You can use this sketch as a debug tool that prints all incoming values
  sent by a widget connected to a Virtual Pin 1 in the Blynk App.

  App project setup:
    Slider widget (0...100) on V1
 *************************************************************/

/* Comment this out to disable prints and save space */
#define BLYNK_PRINT Serial


#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

// You should get Auth Token in the Blynk App.
// Go to the Project Settings (nut icon).
char auth[] = "";

// Your WiFi credentials.
// Set password to "" for open networks.
char ssid[] = "";
char pass[] = "";

// This function will be called every time Slider Widget
// in Blynk app writes values to the Virtual Pin 1
BLYNK_WRITE(V1)
{
  int pinValue = param.asInt(); // assigning incoming value from pin V1 to a variable
  // You can also use:
  // String i = param.asStr();
  // double d = param.asDouble();
  Serial.print("V1 Slider value is: ");
  Serial.println(pinValue);
}

void setup()
{
  // Debug console
  Serial.begin(9600);

  Blynk.begin(auth, ssid, pass);
  // You can also specify server:
  //Blynk.begin(auth, ssid, pass, "blynk-cloud.com", 8442);
  //Blynk.begin(auth, ssid, pass, IPAddress(192,168,1,100), 8442);
}

void loop()
{
  Blynk.run();
}
```

Once upload, open ser monitor, it shows as below:

![](/assets/blynk_out.png)





# UNO and Blynk

![](/assets/blynk_uno_example.png)

Find the path to Blynk library, such as `My Documents\Arduino\libraries\Blynk\scripts`

right click, Open commond wind here,

Run`blynk-ser.bat`file. For example :`blynk-ser.bat -c COM4`\(where COM4 is port with your Arduino\)

And press “Enter”, press “Enter” and press “Enter”



