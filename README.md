# Manual connecting distance sensor to arduino LED
Rosalie Groen
500880138
18-10-2024
![Untitled design copy](https://github.com/user-attachments/assets/4000ec92-cd63-41c5-a2c5-fcd87ee78c0f)

## introduction
I Build a smart trashcan called the BinBot. One of the components of this BinBot is that it opens when there is movement detected within 20 cm of the lid.
After the lid opened, the camera with smart ai will check in which bin the trash has to be sorted (for example: plastic or paper) the bin in which it is supposed to be in will light up. 

In this manual we are going to make the Led from NodeMCU light up when someone is moving 20 cm from the movement sensor using arduino.

Make sure arduino is already installed on your laptop.
If you haven't done this, use this manual: 
https://support.arduino.cc/hc/en-us/articles/360019833020-Download-and-install-Arduino-IDE

To install Node MCU use this manual:
https://randomnerdtutorials.com/installing-esp8266-nodemcu-arduino-ide-2-0/


required hardware components:
- node MCU (arduino)
- distance sensor (HC-SR04)
- laptop

## Step 1: connecting the distance sensor
To install the distance sensor , first plug in your arduino in your laptop and open the arduino IDE 2 app. 

### 1. The physical plugs
there will be 4 wires connected to the distance sensor. 

- **+VCC** This is the power source
- **Trig** Sends a trigger signal to send a ultrasonic sound
- **Echo** Recieves the ultrasonic sound to messure the distance between objects and the sensor
- **GND** Stands for Groud


1. connect the VCC to Vin on arduino
2. connect the Trig to D2
3. connect the Echo to D3
4. connect the GND to GND


## Step 2: connect the distance sensor
To check if the distance sensor is connected we are going to make some code which
will give us the distance between objects and the sensor in our serial monitor.
To do this, open a new sketch and add the following code:

```cpp
#define TRIG_PIN D2 // Pin voor de TRIG van de HC-SR04
#define ECHO_PIN D3 // Pin voor de ECHO van de HC-SR04

void setup() {
    Serial.begin(9600); // Begin seriële communicatie
    pinMode(TRIG_PIN, OUTPUT); // Zet TRIG-pin als output
    pinMode(ECHO_PIN, INPUT); // Zet ECHO-pin als input
}

void loop() {
    long afstand = meetAfstand(); // Meet de afstand
    Serial.print("Afstand: ");
    Serial.print(afstand);
    Serial.println(" cm"); // Print de afstand in cm
    delay(500); // Wacht 500 milliseconden
}

long meetAfstand() {
    digitalWrite(TRIG_PIN, LOW); // Zorg dat TRIG LOW is
    delayMicroseconds(2);
    digitalWrite(TRIG_PIN, HIGH); // Stuur een HIGH-signaal naar TRIG
    delayMicroseconds(10);
    digitalWrite(TRIG_PIN, LOW); // Zet TRIG weer LOW

    long duur = pulseIn(ECHO_PIN, HIGH); // Meet de tijd dat ECHO HIGH is
    long afstand = duur * 0.034 / 2; // Bereken de afstand in cm
    return afstand; // Geef de afstand terug
}
```

## Step 3: check the serial monitor
<img width="320" alt="Screenshot 2024-10-17 at 16 46 56" src="https://github.com/user-attachments/assets/4086fb1c-4e3f-419a-ac4e-316593831490">

The serial monitor should look something like this.
Try moving your hand or another object closer or further from the sensor to see the distance change.



## Step 4: making the Led light up
This code is going to make the led light up if an object is detected in 20cm or less near the sensor.

```cpp
#define TRIG_PIN D2 // Pin voor de TRIG van de HC-SR04
#define ECHO_PIN D3 // Pin voor de ECHO van de HC-SR04
#define LED_PIN D4 // Pin voor de LED

void setup() {
    Serial.begin(9600); // Begin seriële communicatie
    pinMode(TRIG_PIN, OUTPUT); // Zet TRIG-pin als output
    pinMode(ECHO_PIN, INPUT); // Zet ECHO-pin als input
    pinMode(LED_PIN, OUTPUT); // Zet LED-pin als output
    digitalWrite(LED_PIN, LOW); // Zet de LED standaard uit
}

void loop() {
    long afstand = meetAfstand(); // Meet de afstand
    Serial.print("Afstand: ");
    Serial.print(afstand);
    Serial.println(" cm"); // Print de afstand in cm

    // Zet de LED aan of uit op basis van de afstand
    if (afstand >= 20) {
        digitalWrite(LED_PIN, HIGH); // Zet de LED aan
        Serial.println("LED aan"); // Debug-informatie
    } else {
        digitalWrite(LED_PIN, LOW); // Zet de LED uit
        Serial.println("LED uit"); // Debug-informatie
    }

    delay(500); // Wacht 500 milliseconden
}

long meetAfstand() {
    digitalWrite(TRIG_PIN, LOW); // Zorg dat TRIG LOW is
    delayMicroseconds(2);
    digitalWrite(TRIG_PIN, HIGH); // Stuur een HIGH-signaal naar TRIG
    delayMicroseconds(10);
    digitalWrite(TRIG_PIN, LOW); // Zet TRIG weer LOW

    long duur = pulseIn(ECHO_PIN, HIGH); // Meet de tijd dat ECHO HIGH is
    long afstand = duur * 0.034 / 2; // Bereken de afstand in cm
    return afstand; // Geef de afstand terug
}
```

## Mistakes
- **Not checking your power source**
  One very important mistake which can make your whole project fail is when the power source is not connected. 
  This seems very simple but this can happpen pretty quick and can be fixed easily! Make sure you always check the plug in your laptop and in your arduino.
  Sometimes when your arduino doesn't respond to the code it helps if you unplug and replug it in your laptop.
  
- **Your code and pins do no align**
   Its very important the pins you used align with your code. Your arduino will not give you any error messages (mine didn't) and thats why 
   sometimes it takes a little while before you find out. 

   ```cpp
   #define TRIG_PIN D2 // Pin voor de TRIG van de HC-SR04
   #define ECHO_PIN D3 // Pin voor de ECHO van de HC-SR04
   ```
   
   if your code says this make sure the pins are really connected to D2 and D3 on your arduino.

- **+5V replacement**
   When using AI to write some of your code or using youtube video's or internet it can happen they don't use Node MCU. 
   This can make connecting the hardware complicated because most tutorials and other sources will tell you to connect to the +5v pin on your arduino but NodeMCU
   doesn't have one. The mistake I made was connecting it to 3v3 because some sources said so. After doing more research I found out this will not work because the Echo pin      from the distance sensor gives out a 5v signal which can be too high for the 3v3 inputs from arduino. This is why you should use the VIN pin.

- **Damaged nodeMCU board**
   Whenever your Board is not responding or doesnt have a little light burning after uploading the code it is also a possibility your board is damaged.
   Try to replace this with a new board before giving up all hope;)

## Personal trial and error
Here is a little overview of my personal trial and error to see what my journey was to get the code right.

the code didn't seem to work so i asked ai to explain what could be wrong. The problem seems to be with the motion sensor so I will test the motion sensor seperatly 
with this code:

```cpp
#define TRIG_PIN 2
#define ECHO_PIN 3

void setup() {
  Serial.begin(9600);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
}

void loop() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duur = pulseIn(ECHO_PIN, HIGH);
  long afstand = duur * 0.034 / 2; // Bereken afstand in cm

  Serial.print("Afstand: ");
  Serial.print(afstand);
  Serial.println(" cm");

  delay(500);
}
```

this didnt seem to do anything so I am now going to check the connection
the arduino doesnt seem to be connected because the light isnt burning. 


after checking online souces, asking other students and using AI to ask what I can do to change this I found out that If the node MCU light doesn't work and the 
led strip does, the problem is most likely with:
- the power
- the wrong plugs

The power source seemed to be one of the issues. The internet told me the 3v3 pin where i got my power from maybe didnt give enough power to make it work. 
I changed it to connect it to the Vin pin on my Node MCU. 

after adding this code:

```cpp
#define TRIG_PIN D2 // Pin voor de TRIG van de HC-SR04
#define ECHO_PIN D3 // Pin voor de ECHO van de HC-SR04

void setup() {
    Serial.begin(9600); // Begin seriële communicatie
    pinMode(TRIG_PIN, OUTPUT); // Zet TRIG-pin als output
    pinMode(ECHO_PIN, INPUT); // Zet ECHO-pin als input
}

void loop() {
    long afstand = meetAfstand(); // Meet de afstand
    Serial.print("Afstand: ");
    Serial.print(afstand);
    Serial.println(" cm"); // Print de afstand in cm
    delay(500); // Wacht 500 milliseconden
}

long meetAfstand() {
    digitalWrite(TRIG_PIN, LOW); // Zorg dat TRIG LOW is
    delayMicroseconds(2);
    digitalWrite(TRIG_PIN, HIGH); // Stuur een HIGH-signaal naar TRIG
    delayMicroseconds(10);
    digitalWrite(TRIG_PIN, LOW); // Zet TRIG weer LOW

    long duur = pulseIn(ECHO_PIN, HIGH); // Meet de tijd dat ECHO HIGH is
    long afstand = duur * 0.034 / 2; // Bereken de afstand in cm
    return afstand; // Geef de afstand terug
}
```

and connecting the distance sensor I got output in the serial monitor and the distance sensor worked:
<img width="320" alt="Screenshot 2024-10-17 at 16 46 56" src="https://github.com/user-attachments/assets/e9425579-a384-4924-9f18-d8351e08e8ca">



For the next step I added code to make the light go on whenever the distance was under 20 cm

```cpp
#define TRIG_PIN D2 // Pin voor de TRIG van de HC-SR04
#define ECHO_PIN D3 // Pin voor de ECHO van de HC-SR04
#define LED_PIN D4 // Pin voor de LED

void setup() {
    Serial.begin(9600); // Begin seriële communicatie
    pinMode(TRIG_PIN, OUTPUT); // Zet TRIG-pin als output
    pinMode(ECHO_PIN, INPUT); // Zet ECHO-pin als input
    pinMode(LED_PIN, OUTPUT); // Zet LED-pin als output
}

void loop() {
    long afstand = meetAfstand(); // Meet de afstand
    Serial.print("Afstand: ");
    Serial.print(afstand);
    Serial.println(" cm"); // Print de afstand in cm

    // Zet de LED aan of uit op basis van de afstand
    if (afstand <= 20) {
        digitalWrite(LED_PIN, HIGH); // Zet de LED aan
    } else {
        digitalWrite(LED_PIN, LOW); // Zet de LED uit
    }

    delay(500); // Wacht 500 milliseconden
}

long meetAfstand() {
    digitalWrite(TRIG_PIN, LOW); // Zorg dat TRIG LOW is
    delayMicroseconds(2);
    digitalWrite(TRIG_PIN, HIGH); // Stuur een HIGH-signaal naar TRIG
    delayMicroseconds(10);
    digitalWrite(TRIG_PIN, LOW); // Zet TRIG weer LOW

    long duur = pulseIn(ECHO_PIN, HIGH); // Meet de tijd dat ECHO HIGH is
    long afstand = duur * 0.034 / 2; // Bereken de afstand in cm
    return afstand; // Geef de afstand terug
}
```


This worked but the light went off when i moved my hand closer instead of on.
The only thing I ended up changing was changing the direction of this arrow

```cpp
  if (afstand <= 20)
```
  to
  
```cpp
  if (afstand >= 20) 
```

Now it worked!

  
## sources

https://www.youtube.com/watch?v=KGwtit2bFyo
https://icthva.sharepoint.com/:w:/s/FDMCI_ORG__CMD-Amsterdam/EVr1sv9I5bpJmXeDdq6tSeABsShKe7Zq0ahkOEFHcop1Hg?e=hLMyBo

**for the code:**
chatgpt





