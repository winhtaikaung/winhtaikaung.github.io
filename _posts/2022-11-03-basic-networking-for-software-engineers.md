---
title: Software Engineer တို့အတွက် အခြေခံ Cloud Networking | basic networking for software engineers
description: Title မှာ ကို ရေးထားတာက Software Engineer လို့ရေးထားတာဖြစ်တဲ့ Software Engineer လုပ်နေသူများကို အဓိကထားရည်ရွယ်ပါတယ်။ ဒီအကြောင်းအရာဟာNetworking ကို ကျွမ်းကျင်သူများအတွက်တော့ လွယ်ကူပေမယ့် Software Engineer တွေအတွက်တော့ ခက်ခဲလေ့ရှိကြပါတယ်။ အများစုက ကတော့ Organisation တော်တော်များများမှာ စနစ်တကျလုပ်ထားပြီးသားဖြစ်လေ့ရှိကြတဲ့ အပြင် ဒီအလုပ်တွေကို Devops တွေ CloudEngineer တွေက လုပ်ပေးလေ့ရှိကြပါတယ်။
author: winhtaikaung 
date: 2022-11-03 11:33:00 +0800
categories: [AWS, Networking]
tags: [aws, networking,cloud networking]
pin: true
math: true
mermaid: true
image:
  path: https://miro.medium.com/v2/resize:fit:1400/format:webp/1*WWHJqKHKiBS1MM5SL1UWJA.png 
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: AWS reserved instance & Saving Plans
---

Title မှာ ကို ရေးထားတာက Software Engineer လို့ရေးထားတာဖြစ်တဲ့ Software Engineer လုပ်နေသူများကို အဓိကထားရည်ရွယ်ပါတယ်။ ဒီအကြောင်းအရာဟာNetworking ကို ကျွမ်းကျင်သူများအတွက်တော့ လွယ်ကူပေမယ့် Software Engineer တွေအတွက်တော့ ခက်ခဲလေ့ရှိကြပါတယ်။ အများစုက ကတော့ Organisation တော်တော်များများမှာ စနစ်တကျလုပ်ထားပြီးသားဖြစ်လေ့ရှိကြတဲ့ အပြင် ဒီအလုပ်တွေကို Devops တွေ CloudEngineer တွေက လုပ်ပေးလေ့ရှိကြပါတယ်။

ကျနော်တို့ တွေက Code Push ပြီးရင် local မှာ စမ်းပြီးရင် အလုပ်ပြီးပြီလို့ယူဆလို့ရပေမယ့် တကယ်တမ်းကျတော့ အများကြီးကျန်ပါသေးတယ်။ CI/CD /QA ကတော့လောလောဆယ် ထားပါလေ နောက်ဆုံးကိုယ့် Application က Server ပေါ်ရောက်သွားမယ် အလုပ်လုပ်မှသာလျှင်အလုပ်ပြီးတယ်လို့ဆိုလိုတယ်။

Server ဆိုတာနဲ့ အရင်ကလို Server ခန်းကြီးတွေမဟုတ်တော့ပါဘူး အားလုံးက Cloud ပေါ်မှာပါ။ mouse လေးနဲ့ ဟိုနှိပ်ဒီနှိပ်လုပ်ပြီး မိမိလိုအပ်တဲ့ Server ကို လွယ်လွယ်ကူကူ ဖွင့်နိုင်ပါတယ်။ ကြည့်ရင်တော့လွယ်တယ်လိုထင်ရပေမယ့် တကယ်တော့ မလွယ်ပါဘူး ဘာလို့လဲဆိုတော့ Server ခန်းနဲ့ ကြိုးတွေနဲ့ မဟုတ်ပေမယ့် Virtual Server တွေနဲ့လိုအပ်တဲ့ network ချိတ်ဆက်မှု Setting တွေကို အစအဆုံးပြန်လည်လုပ်ဖို့လိုလာတာပါပဲ။

အဲ့ဒီအချိန်မှာ ကျနော်တို့ က အခြေခံ networkingတွေ ကို ပြန်လည်လေ့လာရပါတော့တယ်။

*   VPC(Virtual Private Cloud)
*   IP Address
*   Subnetting
*   Routing
*   Network Address Translation
*   FireWall

တခြား ပြောစရာတွေကတော့ ဒီနေရမှာမပြောပြတော့ဘူးစာရှည်မှာဆိုးလို့။

VPC(Virtual Private Cloud)
==========================

နာမည်ကို ကရှင်းနေတာဖြစ်တဲ့ အတွက် VPC ရဲ့ အဓိပ္ပယ်ကို မရှင်းပြတော့ဘူး။ ဒါပေမယ့် ဘာလုပ်လဲဆိုတာတော့သိဖို့လိုတယ်။ အလွယ်ပြောရရင် ကိုယ့် ရဲ့ ကိုယ်ပိုင်မြေနေရာလိုပဲဘာမှမရှိဘူး မြေကွက်လပ်ကွင်းပြင်တခုပေးထားမယ် ဒါပေမယ့် အဲ့ဒီကွက်လပ်မှာ နေထိုင်သူ အကန့်အသတ်တော့ရှိတယ်။ အဲ့ ဒီအကန့်အသတ်ကို ဘာနဲ့ သတ်မှတ်လဲဆိုတော့ CIDR(**Classless Inter-Domain Routing) Range** နဲ့သတ်မှတ်တယ်။ ဥပမာလူ၄၀၀၀ဆန့် လူ၁၀၀၀၀ ဆန့်စသည်ဖြင့်။ VPC မှာ ယျေဘူယျအားဖြင့် အများဆုံး host ပေါင်း **2,147,483,648** ကနေ အနည်းဆုံး **1** ဦး အထိ သတ်မှတ်လို့ရတယ်။

**တခုသတိထားရမှာက VPC CIDR က တခါသတ်မှတ်ပြီးသွားရင်ပြန်ပြင်ဖို့မလွယ်တာကိုသတိထားစေချင်ပါတယ်**

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*whxRibAXDq0Bs3mm2MJeVQ.png)

IP Address
==========

IP Address ကတော့ VPC ထဲမှာ ရှိသမျှhost တခုချင်းစီတိုင်းကိုသတ်မှတ်ချိတ်ဆက်ရာမှာလွယ်ကူအောင် မတူညီတဲ့လိပ်စာလေးတွေပေးတဲ့စနစ်ဖြစ်ပါတယ်။ လက်ရှိမှာတော့ Version 4 နဲ့Version 6 ရှိပေမယ့် V4 ကို အဓိကသုံးနေကြတာပဲ ဖြစ်ပါတယ်။

IP Address Classes
------------------

IP Address ကို 8 bit Integer 4 ခု နဲ့ တည်ဆောက်ထားတာဖြစ်လို့ IP တခု ဟာ 32 bit ရှိပါတယ်။ အဲ့ဒါတွေအသေးစိတ်ကိုတော့ ကိုယ့်ဟာကိုယ်လေ့လာဖို့တိုက်တွန်းလို့လိုပါတယ်။

IP Address ကို Class တွေနဲ့ သတ်မှတ်ထားပါတယ်။ **A, B , C** class အပြင် D,E class တွေ စသဖြင့် ၅ မျိုးခွဲထားပါတယ်။ အဓိကကတော့ A,B,C ပဲသုံးကြတာများပါတယ်။
D ကတော့ Multicast ဖြစ်ပြီး E ကတော့ Restricted/Experimental ပါ။

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*WWHJqKHKiBS1MM5SL1UWJA.png)

အကယ် လို့ Networkများများ နဲ့ Host နည်းနည်းထားရမယ်ဆိုရင် Class C ကိုသုံးလို့ရပြီး အကယ် လို့ Networkနည်းနည်းနဲ့ Hostများများ ထားရမယ်ဆိုရင်တော့ Class A ကိုသုံးလို့ရပါတယ်။ အလွယ်မှတ်ရမယ်ဆိုရင်

*   Large Network DataCenter ISP တွေမှာဆို class A သုံးလေ့ရှိကြပါတယ် ဒါမှသာHost များများထည့်လို့ရမှာပါ။သန်းနဲ့ချီတဲ့ Host တွေထည့်လို့ရပြီး Host တွေထဲမှာ Device တွေrouter,တွေပါဝင်ပါတယ်။ Network ကို 128 ခုထိခွဲလို့ရပါတယ်။
*   Medium Enterprise တွေဆိုရင် class B သုံးကြပါတယ် Network ရော Host ရော ညီတူမျှတူခွဲလို့ရပါတယ်။
*   Small Office တွေရုံးတွေကတော့ Class C ကို များသောအားဖြင့်သုံးကြပါတယ်။ လက်ခံနိုင်တဲ့ Host အရေအတွက်နည်းပါတယ်။ network ကတော့ သန်းနဲ့ချီခွဲလို့ရပါတယ်

Subnetting
----------

အပေါ်ကပြောထားတဲ့ VPC မှာ လက်ခံနိုင်မယ့် Host တွေက ရှိတာတော့ဟုတ်ပါပြီ ဒါပေမယ့် Host တခုခု ကို ဆက်သွယ်ဖို့ ဆိုရင် Host အကုန်လုံးကို လိုက်ရှာနေရတာလဲ အဆင်မပြေတော့ Host လေးတွေကို ကျနော်တို့ က အစုလိုက်ပြန်ခွဲ လိုက်တော့ ရှာရတာပိုလွယ်သွားတာပေါ့။

အောက်ကပုံက တော့ ကျနော်တို့ လိုချင်တဲ့ Subnet လေးကို ခွဲတဲ့နေရာမှာ မြင်သာအောင်ဆွဲပြပေးထားတာပဲဖြစ်ပါတယ်။

![subnetting logical diagram](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*-ozngHHBLFShd0WmCdCYEA.png)

ဒါပေမယ့်ကျနော်တို့ ကိုယ်တိုင်ဒီလိုSubnet တွေကိုခွဲဖို့ တွက်ချက်တတ်လားဆို မတွက်တတ်သေးပါဘူး။ ဒါပေမယ့် Subnet Calculator တွေ ရှိပါတယ်။ ကျနော်တို့ အနေနဲ့က Subnet Calculator ကနေတွက်ချက်ပေးတဲ့ အတိုင်းလိုက်ဆောက်ရတာဖြစ်ပါတယ်။ဒါပေမယ့် Subnet Range တွေတခုနဲ့ တခုတော့ ထပ်လို့မရတာကို သတိချပ်စေချင်ပါတယ်

![Subnet Range Overlapping Not Allowed](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*nxIRQ6YgcNDr4IvY9NL9nQ.png)

တခုမှတ်ထားရမှာက Subnet မတူညီပေမယ့် host တွေက Same CIDR Range ထဲမှာရှိနေတာဖြစ်လို့ တခုနဲ့တခုချိတ်ဆက်လို့ရပါတယ်။

တကယ့် ပြင်ပမှာတော့ Resource တွေကိုSecurity အရ **private** subnet မှာပဲ ထားလေ့ရှိကြပြီး ချိတ်ဆက်ပြုပြင်တဲ့အခါမှာတော့ Bastion Host(JumpHost)ကို **public** subnet မှာထားပြီး ထိုမှနေတဆင့်ဝင်ပြင်လေ့ရှိကြပါတယ်

![https://mxtoolbox.com/subnetcalculator.aspx](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*GIPBKgeeOUmOTpmoxHkDgw.png)

Routing
-------

ကျနော်ကိုယ်တိုင် Routing လို့ပြောရင် အရမ်း တွေရှုပ်နေမှာပဲလို့ထင်နေခဲ့ဖူးပါတယ်။ ဒါပေမယ့် သေချာအေးအေးစဥ်းစားပြီးနားလည်သွားတဲ့အခါမတော့ လွယ်သွားပါတယ်။

Routing မှာ အဓိကက၂ခုရှိပါတယ်

*   **သွားမည့်နေရာ**
*   **ပို့ပေးမယ့်နေရာ**

အလွယ်ပြောရရင် Routing ဆိုတာကတော့ ကျနော်တို့ API route တွေသတ်မှတ်သလိုပဲ ဘယ် Route ဆို ဘယ်Controller သွားစသဖြင့် သတ်မှတ်သလိုမျိုး Networking မှာကျတော့ ဝင်လာတဲ့ ip subnet ကတခု ကို နောက်ထပ် ip တခုကိုပြန်လည်လမ်းညွှန်ပေးတာဖြစ်ပါတယ်။ အဲ့လိုလမ်းညွှန်ပေးတဲ့ ညွှန်ကြားချက်တွေကို Table လိုတည်ဆောက်ထားလို့ Route Table လို့လဲခေါ်ကြပါတယ်။

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*UDkwPdH93VlGVHTJOFIfaA.png)

NAT(Network Address Translation)
--------------------------------

NAT ရဲ့ အဓိကလုပ်ဆောင်ချက်ကတော့ ip Address တခုသို့မဟုတ် အများကြီးတခုကို ipaddress တခုကို ဆက်သွယ်လို့ရအောင် ပြောင်းလဲပေးတဲ့ အလုပ်ပဲဖြစ်ပါတယ်။

NAT က မျက်လုံးထဲမြင်အောင်ပြောရရင် HashTable လိုပါပဲ Mapping လုပ်ထားပါတယ်။ အခြေခံအားဖြင့်

*   1 : 1
*   1 : many

၂ ခု အဓိကသုံးကြပါတယ်။ အသုံးများတဲ့ နေရာကတော့ Private Network ကနေ တခြားမတူညီနဲ့ Network တခုကို ချိတ်ဆက်တော့မယ်ဆိုရင် NAT ကို သုံးကြပါတယ်။ အောက်ကပုံကို ကြည့်ပါ။

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*KzNed8rngTicHu9xCOjDdA.png)

အပြာရောင် Subnet က Internet ကို သုံးပြီး Download ဆွဲဖို့လိုတယ်ဆိုကြပါစို့။ ဒါပေမယ့် Internet ကအပြာရောင်မှာမရှိနေဘူးဆိုဘယ်လိုလုပ်မလဲ။ ဒီနေရာမှာ NAT ကလုပ်ဆောင်ပေးရပါပြီ။

အပြာရောင် Subnet ရဲ့ Routing က Publicly Accessible မဟုတ်(Internet ကို Access မရှိ) တဲ့ အတွက် အပြာရောင်Network ကနေ မတူညီတဲ့ IP တွေစီသွားဖို့က NAT ကနေမှတဆင့် ဘာသာပြန်ပေးပြီး အစိမ်းရောင် Network ကို သွားဖို့ လုပ်ဆောင်ပေးပါတယ်။ အစိမ်းရောင် network ကို ရောက်တာနဲ့ အပြာရောင်ရဲ့ Request တွအကုန်လုံး ပြောင်းလဲပြီး သူအလိုရှိရာကို ဆက်သွားလို့ရပါတယ်။

![logical NAT trnanslation](https://miro.medium.com/v2/resize:fit:1016/format:webp/1*2Y9zB-9OyJVvTg-6GiCEpQ.png)

Firewall
--------

Firewall ဆိုတာကတော့ သိတဲ့ အတိုင်းပဲ ကိုယ့်ရဲ့ Network တွေ Resource တွေအတွက် မလိုအပ်တဲ့ IP တွေport တွေကို ပိတ်တာဖွင့်တာတွေလုပ်လို့ရတဲ့ အကာအကွယ်တခုပါ။

Cloud မှာတော့ Firewall လိုမျိုး လုပ်ဆောင် ချက်တွေ ကနေရာNetwork Layer တော်တော်များများမှာ Web Application Firewall ကစလို့ Network level Firewall အထိ ရှိပါတယ်။ Web Application Firewall ကတော့ Application နဲ့ ဆိုင်တဲ့ ဟာတွေကို ကာကွယ်ဖို့ အဓိကသုံးပါတယ်။
အခုပြောမှာကတော့ Network နဲ့ Resource တွေ အတွက် Firewall တွေပါ။

*   Network အတွက် ကျတော့ Access Control Policies ဆိုပြီး ခေါ်လေ့ရှိကြပီးတော့ သူတို့တွေက Subnet Level မှာ အလုပ်လုပ်ပါတယ်။
*   Resource တွေအတွက်ကျတော့ Security Group ဆိုတာမျိုးနဲ့ ခေါ်ကြလေ့ရှိပါတယ်။ Security Group ကတော့ instance level(Resource level)မှာအလုပ်လုပ်ပါတယ်

မတူညီတဲ့ Cloud Provider ပေါ်မူတည်ပြီး အခေါ်အဝေါ် ပြောင်းလဲနိုင်ပါတယ်။

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*nbMiZBK4WbsHpOMhyFXYBQ.png)

အခုလောက်ဆိုရင် စာရှုသူလဲ Cloud Networking အခြေခံနားလည်သွားလောက်ပြီမို့ ကိုယ့်ရုံးမှာဖြစ်စေ Company မှာဖြစ်စေလိုက်ပြီးကြည့်ရှုလေ့လာနိုင်လိမ့်မယ် ထင်ပါတယ်။ အခုပြောသွားတာလေးတွေက အခြေခံဖြစ်ပြီး လက်ရှိမှာ AWS ရဲ့ Networking ကို သုံးပြီး ပြောပြသွားတာဖြစ်ပါတယ်။ Cloud Vendor တွေမတူညီပေမယ့်လဲ အခြေခံလေးတွေကတော့ တူညီတာမို့ ပြန်လည်ဆက်စပ်ပြီး အသုံးချလို့ရပါတယ်။ နောက်တခုကတော့များများလက်တွေ့စမ်းသပ်တာကလဲ အကောင်းဆုံးသင်ယူလေ့လာမှုဆိုတာပြောရင်းနဲ့ ဒီမှာပဲ ရပ်ထားပါရစေ။

ဝန်ခံချက်။ ဒီစာမှာဖော်ပြထားတာတွေဟာ ကျနော့်ရဲ့ Personal အတွေးနဲ့ လက်တွေ့လုပ်ရတဲ့ အပေါ်မူတည်ပြီး နားလည်အောင် ရှင်းပြထားတာဖြစ်ပါတယ်။ ကိုယ်တိုင် ကလဲ Networking ကို လေ့လာနေဆဲ မို့ လိုအပ်တာများရှိလဲဝေဖန်ထောက်ပြပေးနိုင်ပါတယ်။
ကျေးဇူးတင်ပါတယ်။
