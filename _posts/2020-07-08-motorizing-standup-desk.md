---
title: "How I motorized my Ikea SKARSTA standup desk with an Arduino UNO"
date: 2020-07-08T00:00:00-00:00
last_modified_at: 2020-07-10T00:00:00-06:00
categories:
  - blog
tags:
  - Arduino
  - Skarsta
  - Standup
  - Desk
  - Hack
  - Motorize
  - Automation
---

**UPDATE July 10, 2020: This post is still on DRAFT. The setup below works fine for lowering the desk, but for raising it the motor stalls after about 5 seconds, which isn't ideal. I have a solution for that issue upcoming, which is simply adding a second motor to the other end of the shaft to duplicate the torque and reduce the load on the motors. I'll update this post very soon with the details!**

I recently purchased an [Ikea Skarsta](https://www.ikea.com/us/en/p/skarsta-desk-sit-stand-beige-white-s19324815/){:target="_blank"} Standup Desk, it has a nice manual crank that you can turn to raise it and lower it. It takes around 80 turns (45 seconds) each time I want to change it's position... and I usually want to do so at least twice a day... **every day**. Some quick math shows it's around 3 minutes per day turning a crank... that's around **18 hours per year turning a crank!!!.**

I had to do something about it...

Sure, one option is to buy an [Ikea Bekant](https://www.ikea.com/us/en/p/bekant-desk-sit-stand-white-s49022538/){:target="_blank"} Desk, which costs `$449 USD`, or around twice as much as the manual one. 
But I already had mine plus I'm an engineer, so I thought hey... maybe I can hack it?.

And that's what I did.

*I include the cost of the components below, but many of these you probably already have if you've ever done any other electronics projects, you know, like in college?. Anyway, if you already have stuff like an old breadboard and some jumper wires then you can look at spending around $100 bucks for the minimum components, but the cost may go a bit higher if you need to purchase every little tool for the job. Still keep in mind that if you spend $10 bucks on a bunch of resistors you can probably use those for life for other projects, so I wouldn't factor the whole cost in.*

### Quick Note Regarding Torque

A quick calculation using a kitchen scale to measure the force applied on the crank revealed we need about 2Nm (20.4 kg.cm) of torque to successfully raise the desk. Therefore we need a motor that has at least that much torque and can sustain it for about half a minute.

### Required Components:
- [Arduino Uno](https://www.amazon.com/dp/B008GRTSV6/ref=cm_sw_em_r_mt_dp_U_dxObFb4V7WQ6P){:target="_blank"} - $23
- [L298N Motor Driver](https://www.amazon.com/dp/B01M29YK5U/ref=cm_sw_em_r_mt_dp_U_vwObFbCN4ZHKT){:target="_blank"} - $8
- [24v DC Motor (23kg.cm torque)](https://www.pololu.com/product/4683){:target="_blank"} - $25
- [L-Bracket for Motor](https://www.pololu.com/product/1084){:target="_blank"} - $8
- [29v AC/DC Adapter](https://www.amazon.com/dp/B07WSYSX6F/ref=cm_sw_em_r_mt_dp_U_XAObFbEEGQCMQ){:target="_blank"} - $22
- [LM2596S Step Down Buck Converter](https://www.amazon.com/dp/B07CVBG8CT/ref=cm_sw_em_r_mt_dp_U_5AObFb45EW83Q){:target="_blank"} - $6
- [Push Buttons](https://www.amazon.com/dp/B07F24Y1TB/ref=cm_sw_em_r_mt_dp_U_jHObFb4RMAZBK){:target="_blank"} - $2
- [6mm to 8mm Motor Shaft Coupling](https://www.amazon.com/dp/B06X99P2XK/ref=cm_sw_em_r_mt_dp_U_gJObFbAGVMFC8){:target="_blank"} - $7
- [Barrel Jack to Cable adapter](https://www.amazon.com/dp/B07C61434H/ref=cm_sw_em_r_mt_dp_U_zEPbFb5WK2E0J){:target="_blank"} - $8

Stuff you may already have:
- [Solderless Breadboard](https://www.amazon.com/dp/B07PCJP9DY/ref=cm_sw_em_r_mt_dp_U_SFObFbWAXDA0X){:target="_blank"} - $2
- [10k OHM Resistor](https://www.amazon.com/dp/B07QXP4KVZ/ref=cm_sw_em_r_mt_dp_U_eOObFbBDE0Y8X){:target="_blank"} - $8 (kit)
- [Jumper Wires](https://www.amazon.com/dp/B081H2JQRV/ref=cm_sw_em_r_mt_dp_U_LJObFbQACVC1N){:target="_blank"} - $9 (kit)
- [Male to Female Jumper Wires](https://www.amazon.com/dp/B07GD2BWPY/ref=cm_sw_em_r_mt_dp_U_LQObFb5W131WT){:target="_blank"} - $5 (kit)
- [6mm Allen Wrench (Optional)](https://www.amazon.com/dp/B0006HB20Y/ref=cm_sw_em_r_mt_dp_U_0AObFb1Y2MJH0){:target="_blank"} - $3
- [On/Off Switch (Optional)](https://www.amazon.com/dp/B071Y7SMVQ/ref=cm_sw_em_r_mt_dp_U_jEObFbB8SQWXJ){:target="_blank"} - $1
- Any plastic container (like a tupperware food container), about 6" x 5" x 3" (or close), a junction box, or some wooden box

### STEP 1: Load the program!

The Arduino program is the software that waits for a button to be pushed, when it detects it, it sends the appropriate PWM signal to the motor to power it, effectively raising and lowering your desktop.

- Install the [Arduino UNO IDE](https://www.arduino.cc/en/main/software){:target="_blank"}
- Download the MotorControl code from [My Repo](https://github.com/cesar-moya/arduino-power-desktop){:target="_blank"}
- Plug in the Arduino to your computer using an [Arduino USB Cable](https://www.amazon.com/dp/B00NH11KIK/ref=cm_sw_em_r_mt_dp_U_FwPbFbTJJVCYX){:target="_blank"}, also known as a printer cable
- Compile and Load the code onto the Arduino using the IDE respective buttons
- Unplug the Arduino

### STEP 2: Wire it up!
Grab your breadboard, Arduino, L298N, LM2596S, Motor, two buttons, two 10k OHM resistors, the barrel jack cable adapter and a bunch of jumper wires and wire it up as per the circuit diagram below. **DON'T POWER IT YET**:

![circuit-diagram](/assets/images/motorizing-standup-desk/circuit-diagram.png){:class="img-responsive"}

Once wired, it should look something like this (hopefully cleaner!).

![arduino-bread-1](/assets/images/motorizing-standup-desk/arduino-bread-1.jpg)

![arduino-bread-2](/assets/images/motorizing-standup-desk/arduino-bread-2.jpg)

You will notice I'm using a more basic [Push Button](https://www.amazon.com/dp/B07WF76VHT/ref=cm_sw_em_r_mt_dp_U_CHPbFbSWMNNYM){:target="_blank"} in these pictures, that's fine for testing but you probably don't want your buttons down in the circuit, they would be hard to reach!. This is a good setup for testing before the final installation. If you only have the buttons I specified on the components simply plug them in using jumper wires. The final setup will come a bit later.

**Don't plug in the power yet**

### STEP 3: Adjust the voltage input for Arduino
We need to adjust the output voltage from the buck converter, Arduino can handle up to 12 volts, if you feed it 29 volts I suspect you may fry it. But fear not!, simply unplug the `Vin` pin from the Arduino so that there is no power coming to it. you can also unplug the `VOUT` cables from the buck converter if you want to be extra-safe.
Then, *with Arduino unplugged*, plug in the 29v adapter. Don't worry about the L298N, it supports up to 35v, so it will be happy. Look at the display on the buck converter and use the little golden screw to adjust the voltage until it reads **5v**. A multimeter is also a tool you can use if you have one at hand, but it's not required as long as you follow the steps.

![voltage-adjusted](/assets/images/motorizing-standup-desk/buck-reading.jpg){:class="img-responsive"}

Now that the output voltage is adjusted, wire the Arduino back and you are ready for the next step

### STEP 4: Dry Test A

At this point it should be safe to plug in the 29v power adapter onto the circuit (make sure you followed STEP 3 before doing this). The circuits should turn on and if you press the buttons the motor should turn.
You may be wondering why I chose a 29v power pupply, as it turns out the L298N drops around 5 volts in this particular setup, and I want the full 24 volts to reach the motor.

![motor-running](/assets/images/motorizing-standup-desk/motor-running.gif){:class="img-responsive"}

### STEP 5: Assemble Other Hardware

I opted for buying a large-ish 6mm Hex allen wrench and cutting the handle with a Dremel tool so that I didn't need to cut the actual crank that came with the desk, this is for emergency reasons, if our setup stops working you can always pull out the old crank and still have a functioning desktop.

![crank-1](/assets/images/motorizing-standup-desk/crank-1.jpg)

![crank-2](/assets/images/motorizing-standup-desk/crank-2.jpg)

Plug in the crank / wrench to the Motor Shaft using the Coupling:
![motor-with-allen](/assets/images/motorizing-standup-desk/motor-with-allen.jpg)

### STEP 6: Dry Test B

Remove the manual crank, insert the motor with the shaft installed in whichever way you like, temporarily secure it with tape, plug in the motor to the circuit - if it wasn't already - plug in the power and give it a shot!

<video width="480" height="320" controls="controls">
  <source src="/assets/images/motorizing-standup-desk/drytest-a.mp4" type="video/mp4">
</video>

*Note: You can tweak the values of the UP and DOWN speed in the program you loaded onto the Arduino, it's documented in there. if you feel the desk is going too fast - possibly because you don't have as much weight as I do - then play with some different values and re-load the program*

### STEP 7: Finishing touches

Use the L-Bracket to secure the motor in position under the desk, then we need to create some sort of container where you can put the whole circuit in (except the motor), along with the buttons on a position that you can easily reach, and secure it to the underside of the desk, this step is very flexible and I'm sure everyone will have some other preferred or more creative way of doing it. You could even opt for taping the circuit onto the underside of the table! (ugh...). Or maybe putting it in a pot along with some plants and make it a nice decor on top of your desk?.

Anyway, I ended up drilling some holes into a plastic container so that you can insert and secure the buttons. I also made some extra holes for air circulation and for the cables that go from the back and towards the motor.

**<<picture and final video coming soon!>>**

That's it!, what did you think of this approach? If you made it please let me know in twitter and share some pictures!

### References

- [Arduino DC Motor Control Tutorial](https://howtomechatronics.com/tutorials/arduino/arduino-dc-motor-control-tutorial-l298n-pwm-h-bridge/){:target="_blank"}
- [Torque Calculations](https://www.precisionmicrodrives.com/content/torque-calculations-for-gearmotor-applications/){:target="_blank"}

Similar Projects:
- [Ikea Desk Hack using a Drill](https://hackcorrelation.blogspot.com/2015/09/ikea-skarsta-sitstanding-desk-hack.html){:target="_blank"}
- [Motorized Desk](http://scott-stack.com/projects/desk.html){:target="_blank"}



