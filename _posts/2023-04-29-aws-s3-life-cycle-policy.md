---
title: AWS s3 မှာ သိသင့်တဲ့ Life Cycle Policy
description: Life Cycle Policy ကို မပြောခင်မှာ S3 Object Versioning Control အကြောင်းကို ဦးစွာပထမပြောပါရစေ 
author: winhtaikaung 
date: 2023-04-29 11:33:00 +0800
categories: [AWS, S3]
tags: [aws,storage,object storage,lifecycle policy]
pin: true
math: true
mermaid: true
image:
  path: https://miro.medium.com/v2/resize:fit:1400/format:webp/1*kCiNXES0aeVKNU_y0BSa9w.png 
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: AWS reserved instance & Saving Plans
---

AWS s3 မှာ သိသင့်တဲ့ Life Cycle Policy
--------------------------------------

S3 Object Versioning
-------------------

Life Cycle Policy ကို မပြောခင်မှာ S3 Object Versioning Control အကြောင်းကို ဦးစွာပထမပြောပါရစေ

s3 Object Versioning က ဘာလုပ်ပေးလဲ ဆိုတော့ တူညီတဲ့ Object ရဲ့ မတူညီတဲ့ Version တွေကို သိမ်းထားပေးပါတယ်။ s3 မှာ Object တခု လာWrite လိုက်တဲ့ အခါ အရင် ကရှိနေပြီးသားဆိုရင် object ကိုreplace လုပ်တာမဟုတ်ပဲ အသစ်ရောက်လာတဲ့ ဟာကို Version အသစ်လို့သတ်မှတ်ပြီးတော့ နောက်မှရောက်လာတာကို Version အဟောင်းအဖြစ်ထားလိုက်တာဖြစ်ပါတယ်။

အကယ် လို့ ကျနော်တို့ ကဖျက်လိုက်မယ် ဆိုရင် တော့ S3 က အဲ့ဒီObject ကို တကယ် ဖျက်လိုက်တာမဟုတ်ပဲ delete markerလေး တခုသာသွားထားလိုက်တာပါ။ ဆိုတော့ တကယ် ကျနော်တို့ ဖျက်လိုက်တဲ့ ဖိုင်တွေက Access လုပ်လို့သာမရတော့ပေမယ့် S3 ရဲ့ Storage မှာ ရှိနေပါသေးတယ်။ အဲ့လိုရှိနေသေးတဲ့ ဖိုင်တွေရဲ့ storage ပါထည့်တွက်ပြီး Bill မှာ ပါလာပါတယ်။

ဒီနေရာမှာ ပြောပြချင်တာ S3 Versioning ဟာ Recovery လုပ်တဲ့ နေရာမှာ အသုံးဝင်တဲ့ Feature တခုဖြစ်လို့ ဖွင့်ထားသင့်တဲ့ Feature ဖြစ်တယ်လို့ပြောပါရစေ။

S3 Life Cycle Policy နဲ့ Transitioning
--------------------------------------

Life Cycle Transitioning ဘယ်လိုအလုပ် လုပ် လဲ ဆိုတော့ S3 မှာ ကျနော်တို့ LifeCycle Policy တွေသတ်မှတ်လို့ရပါတယ်။ LifeCycle Policy သတ်မှတ်တဲ့ အပေါ်မူတည်ပြီး S3ကနေပေးထားတဲ့ Tier အလိုက်Object တွေကိုTier Transitionလုပ်ပေးတာဖြစ်ပါတယ်။

ဒီအောက်မှာပြထားတာကတော့ S3 ကနေပေးတဲ့ Tier တွေဖြစ်ပါတယ်။ တခုမှတ်ထားရမှာက Standard က ကျနော်တို့ default သိမ်းနေတဲ့ tier ဖြစ်ပြီးတော့ ကျန်တဲ့ Tierတွေကိုလဲ ကိုယ်လိုအပ်ချက်ပေါ်မူတည်ပြီးရွေးချယ်သိမ်းဆည်းလို့ရပါတယ်။

မြှားတွေကတော့ ကျနော်တို့ Object တွေကို ကိုယ်လိုသလို အချိန်နဲ့ Data Retrieval ပေါ်မူတည်ပြီးTier transition လုပ်လို့ရတာကိုပြထားတာပါ။ Tier တခုနဲ့ တခုData Availablity နဲ့ Data Transfer Cost တချို့လဲအပြောင်းအလဲရှိနိုင်ပါတယ်။

![s3 Storage Tiers](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*kCiNXES0aeVKNU_y0BSa9w.png)

> **ဒီနေရာမှာအထူးသတိပြု ရမှာက Tier တခု ကနေ သူ့အောက်မည်သည့်Tierကိုမဆို ဆင်းလို့ရပါတယ် ဒါပေမယ့်အပေါ်ကိုပြန်တက်လို့မရဘူးဆိုတာသိထားစေချင်ပါတယ်။**

အောက်ကပုံမှာပြထားပါတယ်

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*ZrDIHdQefblb0M43pPNYLw.png)

> S3 Standard ကနေ S3 Standard-IA to S3 Standard-IA or S3 One Zone-IA ပြောင်းမယ်ဆိုရင် တော့ အနည်းဆုံး ရက်ပေါင်း ၃၀ Standard Tier မှာ ထားရမှာ ဖြစ်ပါတယ်။ ဆိုလိုတာ
> **Standard** မှာတရက်ပဲ သိမ်းပြီးနောက်နေ့မှာ **Standard-IA** ကို တန်းပြောင်းတာမျိုးမရပါဘူး။

S3 Expiration Actions
---------------------

ဒီတခုကတော့ S3 မှာ ကျနော်တို့ Life Cycle policy သတ်မှတ်တဲ့ နေရာမှာ Lifecycle Policy သက်တမ်းကုန်သွားရင်ဘာလုပ်မလဲဆိုတာကို သတ်မှတ်ဖို့လဲလိုပါတယ်။ အများသောအားဖြင့် Expire ဖြစ်သွားရင် S3 Bucket ကနေတကယ် ဖျက်လိုက်ပါတယ်။ တကယ်Object ဖျက်တာ က တော့ Asynchronous (ချက်ချင်းလက်ငင်းဖျက်တာ မဟုတ်)ဖြစ်လို့ တခါတရံ မှာ s3 API ကနေတဆင့် Delete Marker ထားတယ်လို့ပြန်ပြီး response ပြန်တာမျိုးကြုံရနိုင်ပါတယ်။ သို့သော်လဲ ကျနော်တို့ အနေနဲ့ Expired Object တွေအတွက် တပြားမှတော့ ပေးစရာမလိုတော့ပါဘူး။

အတိုချုပ်အနေနဲ့ S3 Life Cycle ကိုသုံးပြီး Object တွေကို ဖျက်တဲ့ နေရာမှာ ကျနော်ကတော့ အောက်က ဇယားလေးကိုအဓိက အခြေခံပြီး Life Cycle Policy ကို သတ်မှတ်လေ့ရှိပါတယ်။

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*5TL_JbNO9DK7K19k2PQdUQ.png)

ဒီဇယားလေး ကတော့ ကျနော့်ရဲ့ ကိုယ်ပိုင်အမြင်သာဖြစ်ပြီး စာရှုသူအနေနဲ့ လိုအပ်သလိုပြင်ဆင်ပြောင်းလဲ ပြီး LifeCycle policy သတ်မှတ်လို့ရပါတယ်။

ခုလောက်ဆိုရင်တော့ ကျနော်တို့ S3 သုံးတဲ့ နေရာမှာ အသုံးဝင်တဲ့ Life Cycle Feature ကို အကြမ်းဖြင်းအားဖြင့်တော့ သဘောပေါက်နားလည်ပြီလို့ထင်ပါတယ်။ စာကို အဆုံးထိဖတ်ပေးတဲ့ အတွက်ကျေးဇူးတင်ပါတယ်။

AWS s3 နဲ့ ပတ်သက်တဲ့ အချက်အလက်တချို့ကို တော့ အောက်ပါ Site များကနေယူထားပါတယ်။

Ref

[https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html)

[docs.aws.amazon.com](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html?source-post_page-----826e9bf338e1--------------------------------)

[https://docs.aws.amazon.com/AmazonS3/latest/userguide/archived-objects.html](https://docs.aws.amazon.com/AmazonS3/latest/userguide/archived-objects.html)
