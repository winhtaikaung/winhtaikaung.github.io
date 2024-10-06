---
title: AWS Saving Plans | Reserve Instance အကြောင်း လို-တို-ရှင်း 
description: ကျနော်တို့ AWS cloud service ကို ကိုယ့်ရဲ့ personal သုံးတဲ့ နေရာမှာ သာမာန်အားဖြင့် ဒီလိုမျိုး Financial planning မလုပ်ကြပေမယ့် တကယ့် ကိုယ့် ရဲ့ လုပ်ငန်းကကြီးလာရင်Long term ဖြစ်လာရင်တော့ infrastructure တွေ resource တွေအတွက်ဒီလိုPlanning မရှိရင် မရပါဘူး Cost Saving planning ရှိမှ မလိုအပ်တဲ့ ကုန်ကျစရိတ်တွေ လျော့ချနိုင်မှာဖြစ်ပါတယ် ။ ကျနော်ကိုယ်တိုင် လဲ ကျနော့် Team အတွက် လုပ်နေရင်းနဲ့ လုပ်တဲ့ အတွေ့အကြုံကိုအခြေခံပြီး ရေးသားထားတာဖြစ်ပါတယ်။ 
author: winhtaikaung 
date: 2023-04-23 11:33:00 +0800
categories: [AWS, Reservation]
tags: [aws,cost saving,cloud cost]
pin: true
math: true
mermaid: true
image:
  path: https://miro.medium.com/v2/resize:fit:1400/format:webp/1*EalpV9QWOHgJ8mzssE-VwA.png 
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: AWS reserved instance & Saving Plans
---

AWS Saving Plans | Reserve Instance အကြောင်း လို-တို-ရှင်း
==========================================================

ကျနော်တို့ AWS cloud service ကို ကိုယ့်ရဲ့ personal သုံးတဲ့ နေရာမှာ သာမာန်အားဖြင့် ဒီလိုမျိုး Financial planning မလုပ်ကြပေမယ့် တကယ့် ကိုယ့် ရဲ့ လုပ်ငန်းကကြီးလာရင်Long term ဖြစ်လာရင်တော့ infrastructure တွေ resource တွေအတွက်ဒီလိုPlanning မရှိရင် မရပါဘူး Cost Saving planning ရှိမှ မလိုအပ်တဲ့ ကုန်ကျစရိတ်တွေ လျော့ချနိုင်မှာဖြစ်ပါတယ် ။ ကျနော်ကိုယ်တိုင် လဲ ကျနော့် Team အတွက် လုပ်နေရင်းနဲ့ လုပ်တဲ့ အတွေ့အကြုံကိုအခြေခံပြီး ရေးသားထားတာဖြစ်ပါတယ်။

Saving Plans(SP)
----------------

Savin Plans က တိုတိုပြောရမယ်ဆိုရင်

*   Flexible ဖြစ်တယ်
*   နာရီနဲ့ လိုက်ပြီးတွက်တယ်
*   Usage အလိုက် Discount ကိုပေးတယ်
*   Saving Plan က EC2 , Lambda , Fargate, SageMaker ပဲ Cover ဖြစ်တယ်။

Saving Plans မှာ EC2 Saving Plans နဲ့ Compute Savingplans ဆိုပြီး ၂မျိုးခွဲထားပါတယ်။ EC2 Saving Plans ကတော့ EC2 Family တွေမှာ ပဲ အကျုံးဝင်ပြီး Discount ရတာ Compute Saving Plans ထက်ပိုသာပါတယ်။ Compute Saving Plans ကတော့ EC2 မဟုတ်တဲ့ တခြားCompute resource တွေအတွက်ပါ ပါဝင်တာမို့လို့ Flexibility အရကြည့်မယ်ဆို ပိုကောင်းပါတယ်။

Reserve Instance(RI)
--------------------

Reserve instance ကတော့

*   (Instance Type,family, generation, region)Specific ဖြစ်တယ်
*   Service တော်တော်များများ EC2, RDS, Redshift, OpenSearch , DynamoDB, Elastic Cache တွေမှာ Coverဖြစ်တယ်

Commitment
----------

Saving Plans ရော Reserve Instance ရော ၂ခုစလုံး အချိန်အားဖြင့်

*   **၁ နှစ်** စာ သို့မဟုတ်
*   **၃နှစ်စာ** ငွေကြိုခြေလို့ရပါတယ်။

ကိုယ် ဝယ်မယ့် plan ၁နှစ် လား၃ နှစ်လားအပေါ် Discount ရတာလဲကွာပါသေးတယ်။နောက်ပြီး ငွေပေးတဲ့ method

*   **All Upfront**(အကုန်လုံးတထိုင်တည်းပေး),
*   **Partial Upfront**(ခွဲခွဲပြီးပေး) ပေါ်မူတည်ပြီး Discount ရတာလဲကွာပါတယ်။

Capacity Reserevation
---------------------

Capacity Reservation ကတော့ Commitment လုပ်စရာမလိုပါဘူး။့ သူကတော့ Resource ပေါ်မှာ အခြေခံတာဖြစ်ပြီးအထူးသဖြင့် အရေးကြီးတဲ့Application တွေအတွက် EC2 Autoscaling အတွက်အရမ်းအသုံးဝင်ပါတယ်။ နောက်ထပ်တခု ကတော့ ကိုယ်Capacity Reserve လုပ်ထားတဲ့ Resource တွေကို Saving plan သို့ RI နဲ့ သုံးပြီး Discount ရအောင်လုပ်လို့ရပါတယ်။
** ဒါပေမယ့် Region တခု မှာ On-demand ဖွင့် တဲ့ instance အရေအတွက်ကတော့ limit ရှိတယ် ဆိုတာကတော့ သတိထားရမှာဖြစ်ပါတယ်။

ကိုယ်နဲ့ ကိုက်ညီတဲ့ Plan ဘယ်လိုရွေးချယ်မလဲ
==========================================

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*EalpV9QWOHgJ8mzssE-VwA.png)

ဒီနေရာမှာကိုယ့် ရဲ့ လိုအပ်ချက် ပေါ်မူတည်ပြီး ရွေးချယ် ရမှာ ဖြစ်ပါတယ်။
လိုအပ်ချက်မှာ အဓိကက ကိုယ့် ရဲ့ Future မှာ လိုအပ်တဲ့ Resourceတွေ, AZ ,OS , Region စတဲ့ အချက်အလက်တွေကိုပါထည့်သွင်းစဥ်းစားဖို့လိုပါတယ်။

အကြမ်းအားဖြင့် Dicount များလေ Flexible နည်းလေဖြစ်ပြီး Flexibleဖြစ် လေ Discount နည်းလေဖြစ်တယ် လို့ သတ်မှတ်လို့ရပါတယ်။

ဒီတခေါက်တော့ AWS Saving Plans နဲ့ Rerseve Instance အကြောင်း သိလောက်ပြီလို့ထင်ပါတယ် နောက်တခေါက်မှာ တော့ ကိုယ့် ရဲ့ ဝယ်ထားတဲ့ Plan တွေ Cover ဖြစ်ရဲ့ လားမဖြစ်ဘူးလားကို ဘယ်လိုစစ်ကြည့်မလဲ ဆိုတာ ကို ရေးသားပေးသွားမှာဖြစ်ပါတယ်။ အဆုံးထိဖတ်ပေးတဲ့ အတွက် ကျေးဇူးတင်ပါတယ်ခင်ဗျာ။
