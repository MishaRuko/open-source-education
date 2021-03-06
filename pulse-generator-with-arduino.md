# How to make a pulse generator with an arduino

### What's needed:
* 1 arduino board
* A buzzer or an LED
* Two or three wires

## The wiring
The first step will be to wire up the arduino to the buzzer/LED.
1. Connect a wire to any digital I/O pin (other than 0 or 1) on the arduino (They usually have "Digital I/O" written next to them)
2. Connect a wire to a ground pin on the arduino (It should be labelled "gnd")
<br>
<p align="center">
<img src="https://github.com/MishaRuko/open-source-education/blob/main/images/pulse-generator-with-arduino/image1.JPG" width=300>
</p>
<br><br>
This tutorial assumes that you have connected a wire to digital I/O pin 5.
<br><br>

If you have an LED, then connect the wire from the digital I/O pin to the longer terminal of the LED (this is the anode which is positive), and the ground wire to the shorter terminal (which is the cathode and is negative). 
<br><br>
If you have a buzzer module like this: 
<br>
<p align="center">
<img src="https://img.dxcdn.com/productimages/sku_151561_1.jpg" width=300>
</p>
<br>
Then connect the pin labelled "vcc" to a 5V output pin on the arduino and connect wire from the digital I/O pin to the "I/O" pin on the module.
<br>
<p align="center">
<img src="https://github.com/MishaRuko/open-source-education/blob/main/images/pulse-generator-with-arduino/image2.JPG" width=300>
</p>
<br><br>

If you have just the black buzzer part, connect the wire from the digital I/O pin to the pin which has a "+" in a circle on the top of the buzzer.
<br>
<img src="https://github.com/MishaRuko/open-source-education/blob/main/images/pulse-generator-with-arduino/image3.jpg" height=150>
<br>
## The programming
Open the Arduino IDE. There will be two functions defined: `setup()` and `loop()`. 
<br>
The setup() function runs once when the arduino is turned on and the loop function restarts everytime it finishes.
<br>
```
void setup() {
    // put your setup code here, to run once:

}

void loop() {
    // put your main code here, to run repeatedly:

}
```
First, we will make the digital I/O pin that we have connected a wire to, act as an output:
<br>
```
void setup() {
    pinMode(5, OUTPUT);
}
```
Then we'll make the LED/buzzer turn on in the `loop()` function:
<br>
```
void loop(){
    digitalWrite(5, HIGH);
}
```
This makes pin 5 produce a voltage which may be different depending on the Arduino board that you have. 
<br><br>

To turn off the LED/buzzer after 1 second, we can do this:
<br>
```
void loop(){
    digitalWrite(5, HIGH);
    delay(1000);
    digitalWrite(5, LOW);
}
```
Here the `delay()` function makes the program stop for 1000 milliseconds (which is 1 second).
<br>
`digitalWrite(5, LOW);` makes pin 5 stop producing a voltage.
<br><br>
There is a problem however, the LED/buzzer doesn't turn off! This is because just after the last line of the `loop()` function executes and turns off the LED/buzzer, the first line of the function executes, turning the LED/buzzer back on because the `loop()` function loops back to the beginning.
To fix this, we can wait another second after we turn the LED/buzzer off and before we turn it back on.
<br>
```
void loop(){
    digitalWrite(5, HIGH);
    delay(1000);
    digitalWrite(5, LOW);
    delay(1000);
}
```
Try different numbers in the `delay()` function and watch the result!
<br><br>
Now let's make it so that we can type in how many times per second we want the LED/buzzer to turn on. 
<br>
We'll start by adding `Serial.begin(9600)` to the beginning of the `setup()` function. This will allow us to send data to the Arduino from our computer.
<br>
Then we will ask the user for an input like this and then print it out:
<br>
```
float x = 1;
void loop() {
    if (Serial.available()) {
        x = Serial.readString().toFloat();
        Serial.println(x);
    }
    digitalWrite(5, HIGH);
    delay(1000);
    digitalWrite(5, LOW);
    delay(1000);
}
```
Before we enter the loop we create a variable, x, which will store a float (which is a number like 3.5).
<br>
Then at the beginning of the loop we check if there is any input with `Serial.available()`, and if there is any we get it with `Serial.readString()`, then we convert it to a float and store it in x.
<br>
We have to convert the input to a float because otherwise it will be a string (just text) and x can only store floats.
<br>
Then we print out the value stored in x with `Serial.println(x);`
<br>
To access the serial monitor click this button:
<br>
<p align="center">
<img src="https://github.com/MishaRuko/open-source-education/blob/main/images/pulse-generator-with-arduino/image4.png" height=300>
</p>
<br><br>

Now x should be the number of times that we should turn the LED/buzzer on and off every second (or the number of on/off cycles that happen every second. One cycle per second is also called one Hertz). To make the program actually do that we need to change the `loop()` function to:
<br>
```
float x = 1;
void loop() {
    Serial.println(x);
    if (Serial.available()) {
        x = Serial.readString().toFloat();
        Serial.println(x);
    }
    digitalWrite(5, HIGH);
    delay((1000/x)/2);
    digitalWrite(5, LOW);
    delay((1000/x)/2);
}
```
### How does this work?
* Let's say that we need the LED/buzzer to do x cycles every 1000 milliseconds (or every second). 
* Let's also say that one cycle means that we have to turn the LED/buzzer on once and turn it off once.
* So how much time would we need to spend doing x cycles if we have only 1000 milliseconds?
* We would need to spend 1000/x milliseconds for every cycle.
* But how much of the time should the LED/buzzer be on and how much time off every cycle? Let's say that half of a cycle should be spent on and the other half should be spent off.
* This means that if we spend 1000/x milliseconds per cycle, then we need to spend half of 1000/x milliseconds on which is (1000/x)/2 milliseconds and the same amount of time off. So we put those values into the delay function and we are done!

<br>
Now as the program is running, you can open the serial monitor and type in the number of times that you want the LED to blink or the buzzer to beep every second.

