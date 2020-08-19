---
title: "How I motorized my Ikea SKARSTA standup desk with an Arduino UNO"
date: 2020-07-08T00:00:00-00:00
last_modified_at: 2020-07-10T00:00:00-06:00
comments: true
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

I happen to own an [Ikea Skarsta](https://www.ikea.com/us/en/p/skarsta-desk-sit-stand-beige-white-s19324815/){:target="_blank"} Standup Desk, it has a nice manual crank that you can turn to raise it and lower it. It takes around 80 turns (45 seconds) each time I want to change it's position... and I usually want to do so at least twice a day... **every day**. Some quick math shows it's around 3 minutes per day turning a crank... that's around **18 hours per year turning a crank!!!.**

I had to do something about it...

Sure, one option is to scrap it and buy an [Ikea Bekant](https://www.ikea.com/us/en/p/bekant-desk-sit-stand-white-s49022538/){:target="_blank"} Desk, which costs `$449 USD`, or around twice as much as the manual one. 
But I already had mine plus I'm an engineer, so I thought hey... maybe I can hack it?.

And that's what I did.

*I include the cost of the components below, but many of these you probably already have if you've ever done any other electronics projects, you know, like in college?. Anyway, if you already have stuff like an old breadboard and some jumper wires then you can look at spending around $100 bucks for the minimum components, but the cost may go a bit higher if you need to purchase every little tool for the job. Still keep in mind that if you spend $10 bucks on a bunch of resistors you can probably use those for life for other projects, so I wouldn't factor the whole cost in.*

## The main idea

Since we're using an Arduino I decided to go beyond simply motorizing it and actually implement a feature to be able to record the time it takes to raise and lower the desk, then through some button combination trigger the auto-raise and auto-lower mode for a fully automated experience!. Of course we should also be able to simply press the buttons to manually raise / lower the desk. I also wanted an On/Off main switch so that I could turn the whole thing off in case something went haywire, and a LED to indicate if you are in program mode or in auto-raise mode, as well as to handle internal error states and communicate in general to the user.

## So what's the Torque?

A quick calculation using a kitchen scale to measure the force applied on the crank revealed we need about 2Nm (20.4 kg.cm) of torque to successfully raise the desk. Therefore we need either a single motor that has that much torque **and can sustain it for about half a minute**, or two motors with a lower torque. I was only able to find smaller motors online so I went with 2 of these.

## Required Components:
- [Arduino Uno](https://www.amazon.com/dp/B008GRTSV6/ref=cm_sw_em_r_mt_dp_U_dxObFb4V7WQ6P){:target="_blank"} - $23
- [L298N Motor Driver](https://www.amazon.com/dp/B01M29YK5U/ref=cm_sw_em_r_mt_dp_U_vwObFbCN4ZHKT){:target="_blank"} - $8
- [2 x 24v DC Motors (23kg.cm torque)](https://www.pololu.com/product/4683){:target="_blank"} - $50
- [2 x L-Bracket for Motor](https://www.pololu.com/product/1084){:target="_blank"} - $8
- [29v AC/DC Adapter](https://www.amazon.com/dp/B07WSYSX6F/ref=cm_sw_em_r_mt_dp_U_XAObFbEEGQCMQ){:target="_blank"} - $22
- [LM2596S Step Down Buck Converter](https://www.amazon.com/dp/B07CVBG8CT/ref=cm_sw_em_r_mt_dp_U_5AObFb45EW83Q){:target="_blank"} - $6
- [Push Buttons](https://www.amazon.com/dp/B07F24Y1TB/ref=cm_sw_em_r_mt_dp_U_jHObFb4RMAZBK){:target="_blank"} - $2
- [2 x Motor Shaft Coupling (6mm to 8mm)](https://www.amazon.com/dp/B06X99P2XK/ref=cm_sw_em_r_mt_dp_U_gJObFbAGVMFC8){:target="_blank"} - $7
- [Barrel Jack to Cable adapter](https://www.amazon.com/dp/B07C61434H/ref=cm_sw_em_r_mt_dp_U_zEPbFb5WK2E0J){:target="_blank"} - $8
- [Junction Box (replaceable)](https://www.amazon.com/gp/product/B07SDS9GQH/ref=ppx_yo_dt_b_asin_title_o00_s01?ie=UTF8&psc=1){:target="_blank"} - $9 (you may need a larger one if you want a more comfortable fit of the components, I'd recommend you to buy this until you're almost ready to assemble so you can take some final measurements, you may also choose to use some other box you already have)

Stuff you may already have:
- [Solderless Breadboard](https://www.amazon.com/dp/B07PCJP9DY/ref=cm_sw_em_r_mt_dp_U_SFObFbWAXDA0X){:target="_blank"} - $2
- [10k, 330 OHM Resistors](https://www.amazon.com/dp/B07QXP4KVZ/ref=cm_sw_em_r_mt_dp_U_eOObFbBDE0Y8X){:target="_blank"} - $8 (kit)
- [Jumper Wires](https://www.amazon.com/dp/B081H2JQRV/ref=cm_sw_em_r_mt_dp_U_LJObFbQACVC1N){:target="_blank"} - $9 (kit)
- [Male to Female Jumper Wires](https://www.amazon.com/dp/B07GD2BWPY/ref=cm_sw_em_r_mt_dp_U_LQObFb5W131WT){:target="_blank"} - $5 (kit)
- [6mm Allen Wrench (optional)](https://www.amazon.com/dp/B0006HB20Y/ref=cm_sw_em_r_mt_dp_U_0AObFb1Y2MJH0){:target="_blank"} - $3
- [On/Off Switch (optional)](https://www.amazon.com/dp/B071Y7SMVQ/ref=cm_sw_em_r_mt_dp_U_jEObFbB8SQWXJ){:target="_blank"} - $1
- [A LED Diode (optional)](https://www.amazon.com/eBoot-Pieces-Emitting-Diodes-Assorted/dp/B06XPV4CSH/ref=sr_1_4?dchild=1&keywords=LED+diode&qid=1597628926&sr=8-4){:target="_blank"} - $1

## STEP 1: Load the program!

The Arduino program is the software that waits for a button to be pushed, when it detects it, it sends the appropriate PWM signal to the motor to power it, effectively raising and lowering your desktop. Arduino also has an internal EEPROM memory which we will use to store recorded programs (optionally).

- Install the [Arduino UNO IDE](https://www.arduino.cc/en/main/software){:target="_blank"}
- Download the MotorControl code from [My Repo (master branch)](https://github.com/cesar-moya/arduino-power-desktop){:target="_blank"}
- Plug in the Arduino to your computer using an [Arduino USB Cable](https://www.amazon.com/dp/B00NH11KIK/ref=cm_sw_em_r_mt_dp_U_FwPbFbTJJVCYX){:target="_blank"}, also known as a printer cable
- Compile and Load the code onto the Arduino using the IDE respective buttons
- Unplug the Arduino

*Note on usage: Please read the comments on the 'MotorControl.ino' file for detailed explanation about engaging program mode and auto-raise/auto-lower if you plan on using that feature*

## STEP 2: Adjust the voltage input for Arduino
We need to adjust the output voltage from the buck converter, Arduino can handle up to 12 volts, if you feed it 29 volts I suspect you may fry it. 
To do this, simply plug in the power (using the `Barrel Jack to Cable adapter` with 2 cables) to `Vin`, look at the display on the buck converter and use the little golden screw to adjust the voltage until it reads **5v**. 

## STEP 3: Wire it up!
Grab your breadboard, Arduino, L298N, LM2596, Motors, two buttons, the resistors, the barrel jack cable adapter and a bunch of jumper wires and wire it up as per the circuit diagram below.

![circuit-diagram](/assets/images/motorizing-standup-desk/circuit-diagram.png){:class="img-responsive"}
*Right click + Open in new tab for a larger image*

## STEP 4: Dry Test

At this point it should be safe to plug in the 29v power adapter onto the circuit (make sure you followed STEP 2 before doing this). The circuits should turn on and if you press the buttons the motor should turn.
You may be wondering why I chose a 29v power pupply, as it turns out the L298N drops around 5 volts in this particular setup, and I want the full 24 volts to reach the motor. 

Note: I took the following picture and video a bit later in the process, but I *highly* recommend that you test the circuit with the motors **before** installing them onto the desk. To perform the dry test, simply connect everything and give it a shot!. *P.S. Check out that automated raise! ;)*

![drytest-setup](/assets/images/motorizing-standup-desk/drytest-setup.jpg)

I recommend testing the whole setup for a few days - fully plugged in and with motors in place - before putting it on the enclosure and installing it in the final location.

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZE2WeiT5mnQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

*Note: You can tweak the values of the UP and DOWN speed in the program you loaded onto the Arduino, it's documented in there. if you feel the desk is going too fast - possibly because you don't have as much weight as I do - then play with some different values and re-load the program*

## STEP 5: Assemble Other Hardware

I opted for buying a large-ish 6mm Hex allen wrench and cutting the handle with a Dremel tool so that I didn't need to cut the actual crank that came with the desk, this is for emergency reasons, if our setup stops working you can always pull out the old crank and still have a functioning desktop.

![crank-1](/assets/images/motorizing-standup-desk/crank-1.jpg)

![crank-2](/assets/images/motorizing-standup-desk/crank-2.jpg)

Plug in the crank / wrench to the Motor Shaft using the Coupling:

![motor-with-allen](/assets/images/motorizing-standup-desk/motor-with-allen.jpg)

Remove the manual crank, insert the shaft, take measurements for where the bracket holes will be drilled and positioned. Install the brackets first, then the coupling, and finally the motors.

![motors-temp](/assets/images/motorizing-standup-desk/motors-temp.jpg)

Drill some holes on the enclosure (I used a 1/2" drill bit for the button holes). If you are installing the on-off switch, then you'll need to figure out a way to drill a square hole. I used a thin drill bit and drilled a bunch of small holes following the perimeter of the desired square hole, and then pushed the rectangle and sanded it. Remember to also drill a small hole in the front If you're installing the LED, and 2 holes on the back for the cables coming in and for the DC adapter

![enclosure-front](/assets/images/motorizing-standup-desk/enclosure-front.jpg)

![enclosure-back](/assets/images/motorizing-standup-desk/enclosure-back.jpg)

## STEP 6: Finishing up!
Almost there!, simply install the buttons onto the enclosure, pass the motor wires through the hole in the back, and re-plug the cables inside of the enclosure. Remember to install the top part of the enclosure to the desk first or it'll be tough to drill with the circuit already inside, just try to think ahead and you'll be fine!

![enclosure-closing](/assets/images/motorizing-standup-desk/enclosure-closing.jpg)

![desk-final](/assets/images/motorizing-standup-desk/desk-final.jpg)

### Demonstration of the Auto-Raise Feature

<iframe width="560" height="315" src="https://www.youtube.com/embed/lXZzmSOrM6A" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

### Demonstration of the Auto-Lower Feature

<iframe width="560" height="315" src="https://www.youtube.com/embed/HMcWuTiHAU8" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<br/>

That's it!, what did you think of this approach?, let me know in the comments, please share with me if you made it, and if you have any questions feel free to ask.

### References

- [Arduino DC Motor Control Tutorial](https://howtomechatronics.com/tutorials/arduino/arduino-dc-motor-control-tutorial-l298n-pwm-h-bridge/){:target="_blank"}
- [Torque Calculations](https://www.precisionmicrodrives.com/content/torque-calculations-for-gearmotor-applications/){:target="_blank"}

Similar Projects:
- [Ikea Desk Hack using a Drill](https://hackcorrelation.blogspot.com/2015/09/ikea-skarsta-sitstanding-desk-hack.html){:target="_blank"}
- [Motorized Desk](http://scott-stack.com/projects/desk.html){:target="_blank"}


{% if page.comments %}
### Comments

<div id="disqus_thread"></div>
<script>
/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://cesarmoya.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}
