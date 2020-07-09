---
title: "DRAFT - How I motorized my Ikea SKARSTA standup desk with an Arduino UNO"
date: 2020-07-08
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

**This post is still on DRAFT. I'll update the final version very soon!**

I recently purchased an [Ikea Skarsta](https://www.ikea.com/us/en/p/skarsta-desk-sit-stand-beige-white-s19324815/){:target="_blank"} Standup Desk, it has a nice manual crank that you can turn to raise it and lower it. It takes around 80 turns (45 seconds) each time I want to change it's position... and I usually want to do so at least twice a day... **every day**. Some quick math shows it's around 3 minutes per day turning a crank... that's around **18 hours per year turning a crank!!!**

I had to do something about it...

Sure, one option is to buy an [Ikea Bekant](https://www.ikea.com/us/en/p/bekant-desk-sit-stand-white-s49022538/){:target="_blank"} Desk, which costs `$449 USD`, or around twice as much as the manual one. 
But I already had mine plus I'm an engineer, so I thought hey... maybe I can hack it?

And that's what I did.

*I include the cost of the components below, but many of these you probably already have if you've ever done any other electronics projects, you know, like in college?. Anyway, if you already have stuff like an old breadboard and some jumper wires then you can look at spending around $100 bucks for the minimum components, but the cost may go a bit higher if you need to purchase every little tool for the job. Still keep in mind that if you spend $10 bucks on a bunch of resistors you can probably use those for life for other projects, so I wouldn't factor the whole cost in.*


Required Components:
- [Arduino Uno](https://www.amazon.com/dp/B008GRTSV6/ref=cm_sw_em_r_mt_dp_U_dxObFb4V7WQ6P){:target="_blank"} - $23
- [L298N Motor Driver](https://www.amazon.com/dp/B01M29YK5U/ref=cm_sw_em_r_mt_dp_U_vwObFbCN4ZHKT){:target="_blank"} - $8
- [24v DC Motor (with at least 23kg.cm torque)](https://www.pololu.com/product/4683){:target="_blank"} - $25
- [L-Bracket for Motor](https://www.pololu.com/product/1084){:target="_blank"} - $8
- [29v AC/DC Adapter](https://www.amazon.com/dp/B07WSYSX6F/ref=cm_sw_em_r_mt_dp_U_XAObFbEEGQCMQ){:target="_blank"} - $22
- [LM2596S Step Down Buck Converter](https://www.amazon.com/dp/B07CVBG8CT/ref=cm_sw_em_r_mt_dp_U_5AObFb45EW83Q){:target="_blank"} - $6
- [Push Buttons](https://www.amazon.com/dp/B07F24Y1TB/ref=cm_sw_em_r_mt_dp_U_jHObFb4RMAZBK){:target="_blank"} - $2
- [6mm to 8mm Motor Shaft Coupling](https://www.amazon.com/dp/B06X99P2XK/ref=cm_sw_em_r_mt_dp_U_gJObFbAGVMFC8){:target="_blank"} - $7

Stuff you may already have:
- [Solderless Breadboard](https://www.amazon.com/dp/B07PCJP9DY/ref=cm_sw_em_r_mt_dp_U_SFObFbWAXDA0X){:target="_blank"} - $2
- [10k OHM Resistor](https://www.amazon.com/dp/B07QXP4KVZ/ref=cm_sw_em_r_mt_dp_U_eOObFbBDE0Y8X){:target="_blank"} - $8 (kit)
- [Jumper Wires](https://www.amazon.com/dp/B081H2JQRV/ref=cm_sw_em_r_mt_dp_U_LJObFbQACVC1N){:target="_blank"} - $9 (kit)
- [Male to Female Jumper Wires](https://www.amazon.com/dp/B07GD2BWPY/ref=cm_sw_em_r_mt_dp_U_LQObFb5W131WT){:target="_blank"} - $5 (kit)
- [6mm Allen Wrench - Optional](https://www.amazon.com/dp/B0006HB20Y/ref=cm_sw_em_r_mt_dp_U_0AObFb1Y2MJH0){:target="_blank"} - $3, *(if you don't want to cut the crank bar)*
- [On/Off Switch - Optional](https://www.amazon.com/dp/B071Y7SMVQ/ref=cm_sw_em_r_mt_dp_U_jEObFbB8SQWXJ){:target="_blank"} - $1
- Any plastic container (like a tupperware food container), about 6" x 5" x 3" (or close)
- [](){:target="_blank"}

Pro Setup (optional) - **Move this to a separate post**
- [Soldering Kit](https://www.amazon.com/dp/B07GTGGLXN/ref=cm_sw_em_r_mt_dp_U_TRObFb0JFGGWQ){:target="_blank"} - $18
- [Solderable Breadboard](https://www.amazon.com/gp/product/B07ZV8FWM4/ref=ppx_yo_dt_b_asin_title_o08_s01?ie=UTF8&psc=1){:target="_blank"} - $2
- [ATMEGA328P](https://www.amazon.com/dp/B007SH0D0A/ref=cm_sw_em_r_mt_dp_U_HTObFbWK427VX){:target="_blank"} - $7
- [16Mhz Crystal Oscillator](https://www.amazon.com/dp/B0816FWKNT/ref=cm_sw_em_r_mt_dp_U_JTObFbTGDA42T){:target="_blank"} - $1
- [FTDI USB to TTL Serial Adapter](https://www.amazon.com/dp/B07XF2SLQ1/ref=cm_sw_em_r_mt_dp_U_LTObFbB347AW2){:target="_blank"} - $3
- [22pf Capacitor](https://www.amazon.com/dp/B083WQNRDK/ref=cm_sw_em_r_mt_dp_U_PTObFbG2Z1XT1){:target="_blank"} - $1
- [](){:target="_blank"}






<!-- [](){:target="_blank"} -->

![circuit-diagram](/assets/images/motorizing-standup-desk/circuit-diagram.png){:class="img-responsive"}

![arduino-bread-1](/assets/images/motorizing-standup-desk/arduino-bread-1.jpg)

![arduino-bread-2](/assets/images/motorizing-standup-desk/arduino-bread-2.jpg)

![crank-1](/assets/images/motorizing-standup-desk/crank-1.jpg)

![crank-2](/assets/images/motorizing-standup-desk/crank-2.jpg)

![motor-with-allen](/assets/images/motorizing-standup-desk/motor-with-allen.jpg)



<!-- <a href="/assets/images/circuit-diagram.png">
  <img src="/assets/images/circuit-diagram.png" alt="Circuit Diagram">
</a> -->

<!-- ```ruby
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
``` -->

<!-- [jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/ -->
