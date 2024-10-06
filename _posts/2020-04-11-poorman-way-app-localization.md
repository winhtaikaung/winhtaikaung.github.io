---
title: Poorman way of Application Localization 
description: Since I’ve working on many projects for several web applications for different products, we have to use application localization to support multiple languages.Hang on , What is localization ?
author: winhtaikaung 
date: 2020-04-29 11:33:00 +0800
categories: [Localization,Google Sheet]
tags: []
pin: true
math: true
mermaid: true
image:
  path: https://miro.medium.com/v2/resize:fit:4800/format:webp/0*42DnbERXncyIWCB3
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: AWS reserved instance & Saving Plans
---

Since I’ve working on many projects for several web applications for different products, we have to use application localization to support multiple languages.Hang on , What is localization ?

> “**Localization is the process of changing and refining an app in order to appeal to a geographically specific target market. You want to make sure that your app is as appealing and easy to use outside your headquarters’ country as it is within**”

Yes, we have to do it(localization) and we should be doing it even if you plan your application target to your local market. Because we shouldn’t mix our codes with hard coded text. And also it is harder to scale if you are about to welcome to multiple users across the world.

How it can be done ?
--------------------

Yea there are several way of doing it.Although basic procedure is as follow.First you will keep a set of key/value based json or yaml file for multiple languages.

![localization jsons](https://miro.medium.com/v2/resize:fit:492/format:webp/1*n_w4Nr1LHSDJvSW0EERwgA.png)

Then load the translation id instead of translated string.As for me I use [formatjs](https://github.com/formatjs/formatjs) for my projects as my projects are in javascript.

How can we prepare those localization?
--------------------------------------

Here we can use third party services like

1.  [Phrase](https://phrase.com/)( Formerly Phraseapp) *the one I used to love the most
2.  [LingoHub](https://lingohub.com/)
3.  [Crowdin](https://crowdin.com/)
4.  [POEditor](https://poeditor.com)

and many other services.

The benefit of those services provide the whole team collaboration among developer ,designers , project managers & translators. And another thing is you can manage multiple localization under one roof.

Apart from all the features they provide they are a bit pricey for me as our [EzySign](https://ezysign.cc)(providing services for workflow automation with Electronic Signature across the region) team is quite small and self-funded startup based in Myanmar. As for the startup we have to be penny-pinching on our process as much as we could.

So what we have to do is we have to build our own localization service.

What!! Building your own localization service ?It may sounds abit insane right. Yes we do have to.

So I’ve started doing it by manual creation of json files in my code editor and add a value whenever I have to add a new string. But it become painful when you have two engineers working together. Because our merge conflicts for translations rise up after we add the json files in translations. Which took hundreds of seconds to resolve and rebase on each PR.

Here we need some kind of centralized place where all of our team can work together.

Well ,so lets list down what we need for our localization service ?

*   Centralized key value(translations)
*   Synchronize those key values
*   Automatically generate json files
*   Should be able to trace back the changes history
*   Should be able to comment or remark by translator in future 😅

<b>[other]Brainstorming[/other]</b>

So we finally decide to use [GoogleSheet](https://sheets.google.com) to save all of our translation key values because google sheet is pretty straight forward and most of our team members are familiar with that including non technical people from our team. google sheet enable multiple sheets inside single GoogleSheet document so that we can store multiple languages key values. I will tell about more later.

And another cool thing is it enables us to access via [API](https://developers.google.com/sheets/api).

Ok now what we have to do is we have to move our json to google sheet first for english language. Then we create multiple sheets inside single GoogleSheet file.

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*hAiO3ZGXdo86XK4-EU8AXQ.png)

Then we have to create service account as follow.

![Service account Creation](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*tmo7S_6K2g26dAuLwK9rIw.png)

After service account creation you will receive 1 client-secret.json file and one email (mentioned on above figure) that you will be using to access your sheets via api. Then what we will be doing is we will invite the email that we just got from service account to our Sheet

![inviting service account](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*nSQAt9plWRkffc2CIZqOrg.png)

Now we’ve done our part on google sheet side.

Now lets get back to our coding to access our sheet api. P.S here I use javascript as my project is react.But for your case you may use different languages that is fits enough to access google sheet api.

![Google Sheet id From Browser URL](https://miro.medium.com/v2/resize:fit:726/format:webp/1*0Yqzt7njgHWtoogf68c7uA.png)

Here is the code and copy to root of your project. You may want to change the folder path as you need.

you can run above script as follow

`node translation.js`

So now our script will fetch from google sheet api for translations and saving the multiple languages sheets into respective json files.

Sheet Structure
---------------

Ok here it comes the important part. how should we structure our sheet.
we have two part

*   for english language
*   for other languages

**For english language** : we will create our sheet table as follow

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*2CafUfB3pGn-lDibLL0-Gg.png)

For other Languages : we will create as follow

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*Fx_krTz7oVdMMHvAmxMnOw.png)

You will have a question why do you have 3 columns for other languages.
Yes because we would like to use the out of the box google translation service inside google sheet cells.

![captionless image](https://miro.medium.com/v2/resize:fit:466/format:webp/1*3GkWdJ9pdHFqpIpmKLW-fg.png)```
-GOOGLETRANSLATE(B5,”en”,”th”)
```

Pretty cool right ? But you cannot rely google translate all the time . I would suggest to seek help from translator or your friends to take a look after that.

**Synchronizing**
-----------------

Well there is another problem here. How can we sync our english string to other sheets. First I copy paste the two columns to different sheets all the time. Later I found the tricks that does automatically for me within sheet

![import range](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*ftZtJt2yk8YcqbLd_FHttw.png)

let me explain what it basically does

the first parameter is the **_sheet URL_** that you want to import and second parameter starting from left to right is

`**-IMPORTRANGE("sheet URL","sheetname**!**CellRangeStart**:**CellRangeEnd"**)`

No worries I will share the sample sheet that you can copy for yourself.

Sample Translation Sheet
------------------------

### en key,value menu.home,Home

[docs.google.com](https://docs.google.com/spreadsheets/d/1WAkJjagKrog4My4wwYtfE9gyEt9TQC1xtbVZ-iXRAoA/edit?usp-sharing&source-post_page-----2eae8c3dc14e--------------------------------)

Wow we finally got our localization service. As a conclusion, the services that we built is based on our requirements. You may take it or leave it based on your needs. I would still suggest to use paid services if you have to work diverse projects across large team members. As they have solved the problems that I’m trying to solve more efficiently.

Thanks for reading guys, if you found this article useful please give me clap so that I could write more article like this.
