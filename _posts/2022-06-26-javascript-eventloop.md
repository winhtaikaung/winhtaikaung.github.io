---
title: Poorman way of Application Localization 
description: Javascript က Single Threaded language နဲ့ အလုပ်လုပ်တယ် ဆိုတာ ကိုအားလုံးသိကြပြီးဖြစ်မှာပါ။ သို့ပေမယ့် ကျနော်တို့ အများစုသိထားတာက Javascript က Single Threaded ပေမယ့် Concurrent အလုပ်လုပ်လို့ ရအောင် Event Loop ကိုသုံးထားတယ်ဆိုတဲ့ အချက်ပေါ့။ Event Loop ရဲ့ အတွင်းကျကျ အသေးစိတ်အားလုံးကိုနားမလည်ရင်တောင် Event Loop ကိုတော့ နားလည်ထားရင် ကျနော်တို့ အနေနဲ့ Javascript ရေးသားရာမှာ asynchronous operation ပိုပြီး လွယ်လွယ်ကူကူ လုပ်ဆောင်နိုင်ပါလိမ့်မယ်။Event Loop ကိုနားလည်လွယ်အောင် အပေါယံအားဖြင့် သုံးပိုင်းခွဲထားပါတယ်။

author: winhtaikaung 
date: 2022-06-26 11:33:00 +0800
categories: [Javascript, EventLoop]
tags: [Javascript, EventLoop]
pin: true
math: true
mermaid: true
image:
  path: https://miro.medium.com/v2/resize:fit:1400/format:webp/0*woyox6ZV_Nvgxm5- 
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Photo by Filip Mroz on Unsplash 
---

Javascript ရဲ့ Event Loop မှာပါတဲ့ Task Queue ဘယ်လိုအလုပ်လုပ်လဲ။
----------------------------------------------------------------

Javascript က Single Threaded language နဲ့ အလုပ်လုပ်တယ် ဆိုတာ ကိုအားလုံးသိကြပြီးဖြစ်မှာပါ။ သို့ပေမယ့် ကျနော်တို့ အများစုသိထားတာက Javascript က Single Threaded ပေမယ့် Concurrent အလုပ်လုပ်လို့ ရအောင် Event Loop ကိုသုံးထားတယ်ဆိုတဲ့ အချက်ပေါ့။ Event Loop ရဲ့ အတွင်းကျကျ အသေးစိတ်အားလုံးကိုနားမလည်ရင်တောင် Event Loop ကိုတော့ နားလည်ထားရင် ကျနော်တို့ အနေနဲ့ Javascript ရေးသားရာမှာ asynchronous operation ပိုပြီး လွယ်လွယ်ကူကူ လုပ်ဆောင်နိုင်ပါလိမ့်မယ်။Event Loop ကိုနားလည်လွယ်အောင် အပေါယံအားဖြင့် သုံးပိုင်းခွဲထားပါတယ်။

1.  API Calls/function calls
2.  Task Queue
3.  CallStack

ကျနော်တို့ API Calls ဆိုတဲ့ နေရာမှာ Language ရဲ့ API Call တွေfunction တခုချင်းစီတိုင်းကိုဆိုလိုတာဖြစ်ပါတယ်။ ဥပမာ setTimeout, Promise, async စသဖြင့်

ဒီQuestion လိုမျိုးက interview မှာမေးကြလေ့ရှိတယ်။ အောက်ကကုဒ်ကို မrun ပဲ ထွက်လာမယ့် output ကိုပြောပြပါဆိုပြီး

```
console.log("first")
Promise.resolve('Second').then((res)=> console.log(res))
console.log("third")
```

ဒီ အပေါ်က Code ကို Run လိုက်ရင် ဒီလိုပေါ်လာမှာဖြစ်တယ်။

```
// OUTPUT
first
third
Second
```

ဘာကြောင့်လဲ ကျနော်တို့ ဒီ Code တွေ ကို Javascript Run time တခုအနေနဲ့ ကြည့်ကြည့်လိုက်ရအောင်

၁။ console.log()

၂။ Promise

၃။ console.log()

ပထမဦးဆုံး console.log သည် asynchronous operation မဟုတ်သည့်အတွက် တန်းပြီး execute လုပ်တယ်။
နောက်တခု Promise သည် asynchronous operation ဖြစ်တဲ့ အတွက် တန်းပြီး callstack ပေါ် ရောက်တာနဲ့ မ run ပဲ Task Queue ထဲသွားထည့်ပါတယ်။
နောက်တခုကတော့ asynchronous operation မဟုတ်သည့်အတွက် တန်းပြီး execute လုပ်ပါတယ်။

ဒီနေရာမှာပုံနဲ့ ပြထားပါတယ်

![Try Here](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*A_tyItYpSKx6caQIoAXG5g.png)

ဒီပုံမှာဆိုရင် ကျနော်တို့ မြင်ရမှာ MicroTask Queue ထဲကို anonymous တခုဝင်သွားမှာဖြစ်ပါတယ်။ အဲ့ ဒီ anonymous က ကျနော်တို့ ရေးခဲ့တဲ့ Promise ပါ။ ပြီးမှ EventLoop က ပြန်ပြီး Queue ထဲမှာ ရှိတဲ့ Promise ကို Call Stack ပေါ်တင်ပြီး Call Stack မှာ item မကုန်မချင်း run တာ မြင်ရမှာဖြစ်ပါတယ်။

ဒီနေရာမှာ မေးခွန်းတခု ရှိလာပါတယ် Javascript က promise တွေပဲasync operation လုပ်ပေးမှာ လား ဒါဆို setTimout တို့ လိုမျိုး API Call တွေရော ဘယ်လိုလုပ်မှာလဲမေးစရာရှိလာပါတယ်။
ဟုတ်ပါတယ် သူတို့ကိုလဲ တူညီစွာပဲ TaskQueue ထဲ ထည့်မယ် ပြီးရင် Event Loop ကနေ Task Queue ထဲမှာ ရှိတဲ့ setTimeout ရဲ့ delay timeout ပေါ်မူတည်ပြီး အငယ်ဆုံး ရယ် ဝင်ထားတဲ့ အစဥ် အလိုက်ပေါ်မူတည်ပြီး dequeue လုပ်မှာပါ။

![Try Here](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*N6sCPfmgiOvv9Yxmhj71-A.png)

ဟုတ်ပြီ ဒါဆို TaskQueue ထဲမှာ Promise ရော setTimeout ရော ထည့်ထားတာကို EventLoop က CallStack ပေါ်တင်ပေးတာလား။ မဟုတ်ပါဘူး ကျနော်တို့ TaskQueue မှာ ၂ မျိုးခွဲထားပါတယ်။

*   **MicroTaskQueue**
*   **MacroTaskQueue**

တို့ဖြစ်ကြပါတယ်

အလွယ်ပြောရရင်

**MacroTask** ထဲ မှာ setTimeout, setInterval, setImmediate တို့ပါဝင်ပြီးတော့

**MicroTask** ထဲမှာ တော့ Promise,Promise Callback, queueMicroTask တို့ ပါဝင်ပါတယ်။

ဆိုတော့ ကျနော်တို့ TaskQueue ၂ ခုကို ဘယ်လို prioritize လုပ်ပြီး CallStack ထဲထည့်မလဲ။ TaskQueue ၂ ခု ယှဥ်လာပြီ ဆိုရင် **MicroTaskQueue** ကို ဦးစားပေးအနေနဲ့ CallStack ထဲ ကို ထည့်ပါတယ်။ ပြီးမှသာ **MacroTaskQueue** ကိုထည့်တာပါ။

၁ ။ ပထမဦးဆုံး callStack ထဲမှာ တွေ့သမျှ function တွေကို execute လုပ်ပါတယ်။ အဲ့ဒီfunction တွေ ကreturn ပြန်တာနဲ့ CallStack ထဲကနေ ထုတ်ပစ်ပါတယ်

၂။ ပြီးရင် CallStack မှာ function အားလုံးကုန်သွားတာနဲ့ MicroTaskQueue ထဲမှာ function တွေ ရှိသလားကြည့်ပါတယ်။ ရှိရင် သူတို့ ကို CallStack ပေါ်တင်ပြီး အပေါ်ကလို မကုန်မချင်း execute လုပ်ပါတယ်။ ပြီးရင် stack ထဲကထုတ်ပစ်ပါတယ်။

၃။ နောက်ဆုံးအနေနဲ့ CallStack နဲ့ MicroTaskQueue မှာ function တွေ ကုန်သွားတာနဲ့ MacroTaskQueue ထဲမှာ function တွေ ရှိသလားကြည့်ပါတယ်။ ရှိရင် သူတို့ ကို CallStack ပေါ်တင်ပြီး အပေါ်ကလို မကုန်မချင်း execute လုပ်ပါတယ်။ ပြီးရင် stack ထဲကထုတ်ပစ်ပါတယ်။

![Try Here](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*JQHtyUxufUhRY5Y6mBxqng.png)

TaskQueue အကြောင်းကတော့ ဒီလောက်ပါပဲ ကျနော် စာရှည်မှာဆိုးလို့ ချန်လှပ်ထားတာတွေရှိပါတယ်။ Promise အကြောင်းတွေ async/await ဘာလဲဆိုတာတွေ တော့ ချန်လှပ်ထားတာရှိပါတယ်။ ဒါပေမယ့် ကျနော့် article လေးက နေ Event Loop အကြောင်း TaskQueue အကြောင်း ဗဟုသုတအနေနဲ့ မိတ်ဆွေရသွားရင်စာရေးရကျိုးနပ်ပါတယ်။ အဆုံးထိဖတ်ပေးတဲ့ အတွက် ကျေးဇူးတင်ပါတယ်။

ဒီ article မှာပါတဲ့ Try Here Link တွေ ကို ဒီ[နေရာ](https://www.jsv9000.app/?code=)မှာ လုပ်ထားတာပါ။
