---
title: Docker နဲ့ Development လုပ်ကြမယ်
description: Docker ဆိုတာအလွယ်ပြောရရင် Apps တွေ ကို လွယ်လွယ်ကူကူ ထည့်ပြီး နောက်ထပ်တနေရာမှာ ဘာမှ ကြိုတင် Setupလုပ်စရာတွေအများကြီးမလိုပဲ Docker သွင်းထားရုံနဲ့ ချက်ခြင်း Deployလုပ်ပြီး Runလို့ရအောင်လုပ်ပေးထားတဲ့ စနစ်တခုပါပဲ။ 
author: winhtaikaung 
date: 2018-09-01 11:33:00 +0800
categories: [Containers]
tags: [Docker,Development,Tools]
pin: true
math: true
mermaid: true
 
---
Docker ကိုအသုံးပြုပြီး Development လုပ် တဲ့ကျွန်တော့်ကိုယ်တွေ့

ကျွန်တော်စာသိပ်ရေးလေ့လဲ မရှိလို့ မှားတာရှိရင် ခွင့်လွှတ်ပေးကြပါလို့။တချို့သိပြီးသားစာရှုသူများအနေနဲ့ မှားနေရင်လဲထောက်ပြဖို့ မေတ္တာရပ်ခံအပ်ပါတယ်။

ဆိုတော့ က ဒီအကြောင်းအရာ ကိုမဖတ်ခင် စာရှုသူ အနေနဲ့ Docker နဲ့ ရင်းနှီးထားဖို့လိုလိမ့်မယ်။Docker ဆိုတာအလွယ်ပြောရရင် Apps တွေ ကို လွယ်လွယ်ကူကူ ထည့်ပြီး နောက်ထပ်တနေရာမှာ ဘာမှ ကြိုတင် Setupလုပ်စရာတွေအများကြီးမလိုပဲ Docker သွင်းထားရုံနဲ့ ချက်ခြင်း Deployလုပ်ပြီး Runလို့ရအောင်လုပ်ပေးထားတဲ့ စနစ်တခုပါပဲ။ Docker ကဘာလဲ ဆိုတာအသေးစိတ်သိချင်ရင် တော့ [ဒီမှာ](https://www.docker.com/) ဖတ်နိုင် ပါတယ်။

ဘာလို့Docker သုံးမှာလဲ။
-----------------------

ဟ ငါတို့ စက်ထဲမှာ CompilerတွေInterpreter တွေထည့်ထားပြီး Developလို့ရတာပဲဟာ။Databaseတွေလဲထည့်ထားလို့ရတာပဲ ဘာလို့Docker ကိုထည့်ပြီးDevelop လုပ်ရမှာလဲ။ Docker တင်ရင် ပိုပြီးလေးသွားမှာပေါ့ အစရှိသဖြင့်ပြောကြပါတယ် ကျွန်တော်လည်းပြောခဲ့ဖူးပါတယ်။သို့ပေမယ့် ကျွန်တော် တို့ Development လုပ်တဲ့ စက်ပျက်သွားတဲ့အခါကျတော့ ကျွန်တော် တို့ ပြသနာစတက်ပါပြီ။ ဘာကြောင့်လဲဆိုတော့ ကျွန်တော်တို့ ဘာတွေသွင်းခဲ့လဲဆိုတာကိုမမှတ်မိတာရယ် နောက်တခုက Database တွေကို အစအဆုံး ပြန်သွင်းရ Configure ပြန်လုပ်ရနဲ့ တော်တော်ရှုပ်ပါတယ်။ အဆိုးဆုံးတချက်က project Delivery time နားကပ်ဖြစ်ရင်ပိုဆိုးပါတယ်။အမြန်ဆုံးဆိုရင်တောင် ၁ရက်ကျော်ကျော်ကြာပါတယ်။ ဒါကလိုင်းတော်တော်ကောင်းမှနော်။

ဆိုတော့ကျွန်တော်တို့ development လုပ်တဲ့environmentနဲ့ sourceကို VirtualBox လိုမျိုးထဲမှာထည့်ပြီး သိမ်းရင်ရောမဖြစ်နိုင်ဘူးလား ဆိုတဲ့အတွေးရပါတယ်ဒါနဲ့ ကျွန်တော်လိုက်ရှာကြည့်တော့ HashiCorpက [Vagrent](https://www.vagrantup.com/) ကိုတွေ့တယ် သို့ပေမယ့် သူက VirtualBoxကို Runရမယ်ဆိုတော့ ကျွန်တော့်စက်က RAM က8G နဲ့ဆိုတော့ အဆင်သိပ်မပြေချင်ဘူးVagrant Box ကိုRunလိုက်ရင် ကျွန်တော့်စက်ကနေ 2 GB က နေရာယူသွားပီလေနော် တခြားService တချို့ကိုပိတ်ထားလည်း VirtualBoxကAllocateလုပ်ထားတာဆိုတော့ resource efficient မဖြစ်တာရယ် နောက်ပြီးတခု က Deployment အတွက် Ready သိပ်မဖြစ်သလိုပဲ။ ကျွန်တော့် personalအမြင်ပါ ။ ဒါနဲ့ ကျွန်တော်တခြားထပ်ရှာကြည့်တော့ Docker မှာ Development Environment ကို Docker container ထဲ ထည့်ပြီး development လုပ်တဲ့ [Article](https://medium.com/phytochemia-tech-blog/how-docker-can-improve-your-development-environments-731cdfda0b9a) တခု ကိုတွေ့ပါတယ်။ Docker ရဲ့ သဘောက one container one process ပါ ။Container တခုထည်းမှာ Process တခုထက်မက လုပ်လို့တော့ရပါတယ် ။ဒါပေမယ့် ကောင်းတော့မကောင်းဘူးပေါ့။ ကျွန်တော်တို့က container တခုမှာapp ကိုတင်မယ် နောက်တခုမှာ dbကိုတင်မယ်ပ​ြီးရင် app နဲ့dbနဲ့ container ခြင်းLinkချိတ်ကြတော့မယ်ဆိုရှုပ်လာတော့မှာပါ။အဲ့လိုဟာတွေကို docker-compose နဲ့ဆိုအများက​ြီးအသေးစိတ်လိုက်လုပ်စရာမလိုတော့ပါဘူး။multiple container တွေကို Docker ပေါ်မှာ ချိတ်ဆက်ပြီးလုပ်ဆောင်ရာမှာ.yml config file‌က‌နေတဆင့် လွယ်လွယ်ကူကူ လုပ်လို့ရအောင်စီမံပေးပါတယ်။ဆိုတော့ ကျွန်တော်တို့က appတင်မကတော့ပဲမတူညီတဲ့databaseတွေ service တွေကြိုက်သလောက်တိုးလာပါစေ Config ဖိုင်မှာပြင်လိုက်ရုံပါပဲ။

Docker ကိုစက်ထဲ သွင်းတဲ့သွင်းပုံသွင်းနည်းကတော့ စာရှုသူ ရဲ့ အသုံးပြုတဲ့ OS ပေါ်မူတည်ပြီး အနည်းငယ်ကွဲပြားမှုရှိပါလိမ့်မယ်။သွင်းပုံသွင်းနည်းကတော့ [ဒီမှာ](https://www.docker.com/get-started) ရှင်းပြထားပါတယ်။ဒါပေမယ့်Docker ကို မပြောခင် မှာ Docker ရဲ့ အခေါ်အဝေါ်လေးတွေကို နည်းနည်းပြောပြပါရစေ။ဒီနေရာမှာတော့ စာနည်းနည်းရှည်မှာဆိုးလို့ အကျဥ်းချုံးပဲရှင်းပြထားပါတယ် တချို့နေရာမှာလိုအပ်တာလေးတွေရှိခဲ့ရင် Dockerရဲ့သက်ဆိုင်တဲ့ Website မှာပြန်ကြည့်ဖို့မေတ္တာရပ်ခံပါတယ်။

**Dockerfile**
--------------

Docker file ဆိုတာကတော့ Docker Image Template တခုကို တည်ဆောက်ဖို့လိုတဲ့ Configuration တွေကို ရေးထားတဲ့ ဖိုင်တခုပါပဲ။ ဒီဖိုင်ထဲမှာ အများသောအားဖြင့် ကျွန်တော် တို့ CLI မှာရိုက်နေကျ command တွေ Dependencies ပါဝင်ပါတယ်။ အလွယ်ဥပမာနဲ့ပြောရရင် ဟင်းမချက်မှီကျွန်တော် တို့ ဒီဟင်းမှာဘာတွေလိုအပ်မယ့် အမယ်တွေကို ချရေးသလိုမျိုးရေးထားတာပါ။

**Docker Image**
----------------

Docker Image ဆိုတာကတော့ Dockerfile မှာရေးသားထားတဲ့ ConfigတွေCommand တွေ Dependencies ကို ကြိုတင်ပြင်ဆင်ပေးတဲ့ Read-Only Templateတခုပါ။ အလွယ်ဥပမာနဲ့ပြောရရင် ဟင်းချက်ဖို့ လိုတဲ့ ပစ္စည်းတဲ့တွေကို ကြိုတင်ပြင်ဆင်တဲ့သဘောပါ။

Docker Containers
-----------------

Docker Containers ဆိုတာကတော့ DockerImageကသတ်မှတ်တဲ့အတိုင်း ကိုတကယ် တည်ဆောက်ထားတဲ့ Containerတခုပါပဲ။ Containerတခုမှာ operating system တွေ application code တွေ runtime တွေ system tools တွေ system libraries တွေ အစရှိသဖြင့်ပါဝင်ပါတယ်။ Containerတခုနဲ့တခုလဲ ချိတ်ဆက်လို့ရပါတယ်။ အလွယ်ဥပမာနဲ့ပြောရရင် ဟင်းချက်တဲ့ Menuအတိုင်းတကယ်ချက်ထားတဲ့ ဟင်းတခွက်ပေါ့။

DockerCompose
-------------

သူကတော့ DockerContainer တွေအများကြီးကို ချိတ်ဆက်ပေးပြီးတစုတစည်းထဲ အလုပ်လုပ်လို့ရအောင် လုပ်ပေးတာပါ။ Containerတွေချိတ်ဆက်ဖို့ကတော့ DockerComposeဆိုတဲ့ docker-compose.yml ဖိုင်မှာသွားပြီးConfigurationတွေရေးရမှာပါ။ Docker Container တခုချင်းစီကို လိုက် ပြီးCommand တွေရိုက်စရာမလိုတော့ဘူးပေါ့။

ပြောနေကြာတယ် ကွာ လုပ်ကြည့်မယ်
-----------------------------

ဒီနေရာမှာတော့ ကျွန်တော် python(Flask framework) ကိုသုံးပြီးရေးထားတဲ့ WebApplication တခုနဲ့ Postgres Database ကို ချိတ်ထားတဲ့ environmentကိုexample လုပ်ပြပေးသွားမှာဖြစ်ပါတယ်။ဒီနေရာမှာစာဖတ်သူမိတ်ဆွေက တခြား Stack တခု ကိုစမ်းကြည့်ချင်လည်းရပါတယ်။သို့ပေမယ့် သဘောတရားကတော့ တူတူပါပဲ။

ပထမဦးဆုံး folder တခုဆောက်ပြီး Dockerဖိုင်တခုcreate လုပ်ပါမယ်။

```
mkdir your-folder-namecd your-folder-nametouch Dockerfile touch requirements.txt
```

ပြီးရင် Dockerfile ကိုText editor တခုမှာဖွင့်ပီး အောက်မှာပြထားတဲ့ snippet ကိုကူးထည့်လိုက်ပါ

```
FROM python:3.6LABEL maintainer "yourname@yourmail.com"RUN apt-get updateRUN mkdir /appWORKDIR /appCOPY . /appRUN pip install --no-cache-dir -r requirements.txtENV FLASK_ENV-"docker"EXPOSE 5000
```

**Explanation**

ပထမဦးဆုံး snippetထဲမှာမြင်ရတဲ့ `FROM` python3.6.5 ဆိုတာကတော့ python 3.6.5 မှာအခြေခံပြီးစပါဆိုတဲ့instruction ပါ။ဒါပေမယ့်မိမိနှစ်သက်ရာLanguage ပေါ်မူတည်ပီး ပြောင်းလဲနိုင်ပါတယ်။ ဒါပေမယ့်မိမိရဲ့ appက specific operating system နဲ့ runtime မှာrunမယ်ဆိုရင်တော့ FROM မှာ operating system name ကနေစပြီး အစအဆုံးsetup လုပ်လို့ရပါတယ်။ခု ကျွန်တော် တို့က python 3.6.5 လို့ စထားတဲ့အတွက် docker image build တဲ့အချိန်ကျရင် default OS ဖြစ်တဲ့Debian Jessie ကို သွင်းပြီးမှ Pythonကိုသွင်းမှာဖြစ်ပါတယ်။

`WORKDIR` ဆိုတာကတော့ cdနဲ့တူတူပါပဲ ဒီနေရာကိုသွားပါလို့instruction‌ ပေးထားတာပါ။

`ADD` ဆိုတာကတော့ ဒီ Container ထဲကိုဘာတွေထည့်မလဲဆိုတာ ကြေငြာတဲ့instructionပါ။

`COPY` ကတော့ တခုချင်းဆီမထည့်ပဲ folderလိုက်ကို container ထဲကူးထည့်ဖို့instruction ပေးချင်တဲ့အခါသုံးပါတယ်။ဒီနေရာမှာ `.dockerignore` ဆိုတဲ့ဖိုင်ရှိနေရင်`.dockerignore` ထဲမှာကြေငြာထားတဲ့ဟာတွေကို မထည့်ပဲကျော်သွားမှာဖြစ်ပါတယ်။ `.gitignore` နဲ့လုပ်ဆောင်ပုံသဘောတရားတူပါတယ်။

`RUN` ဆိုတာက တော့ ကျွန်တော် တို့DockerContainerထဲက CLI မှာ ရိုက်သွင်းမယ့် command တွေကို ရိုက်သွင်းလိုတဲ့ အခါသုံးတဲ့instruction တခုဖြစ်ပါတယ်။ခုဒီမှာSampleထဲမှာပြထားတဲ့ `pip install — no-cache-dir -r requirements.txt` က python package အတွက် လိုအပ်တဲ့ Dependencies များကို directory cache မလုပ်ပဲသွင်းသွားပေးမှာဖြစ်ပါတယ်။မိမိ ရဲ့ Langauge နဲ့ Framework stack ပေါ်မူတည်ပြီး လိုသလိုပြောင်းလဲသုံးစွဲနိုင်ပါတယ်။

`EXPOSE` ဆိုတာကတော့ မိမိrunနေတဲ့appကိုချိတ်နိုင်မယ့် port numberကို accessလုပ်လို့ရအောင်ဖွင့်ပေးထားခြင်းဖြစ်ပါတယ်။

ကဲDockerFileရေးပြီးရင်တော့ ကျွန်တော် တို့ ဒီ DockerFile ရေးထားတာမှန်မမှန်buildပြီးစစ်ကြည့်ပါမယ်။

```
sudo docker build ./ -t app
```

အဲ့ဒီအချိန်ကျရင် တော့Dockerfileမှာကြေငြာထားတဲ့အတိုင်း‌Image တခုတည်ဆောက်ပေးမှာဖြစ်ပါတယ်။ ပထမbuildတဲ့အချိန်နဲနဲကြာတတ်ပါတယ် repositoryထဲကနေပီးdependenciesတွေဆွဲနေလို့ပါ နောက်တခေါက်ထပ်buildရင်တော့ ရှိပီးသားDependencies ဆို ထပ်မဆွဲပဲ ရှိပီးသားထဲက ပြန်သုံးပေးသွားပါတယ်။

**App**
-------

ကဲဒါဆိုရင်တော့ SimpleFlask App လေးတခု က‌နေစကြည့်မယ် အောက်က Snippet လေးကို ကူးပြီး app.py ဆိုပြီးဆောက်ပေးပါ။

<b>[other]Simple Python Flask App[/other]</b>

ကျွန်တော် က တော့ package manager pip ကိုသုံးလို့ လိုအပ်တဲ့ Package instances တွေကို requirements.txt မှာ ဒီလိုကြေငြာမှာပါ။

```
Flask**--**0.12.2
Flask-Migrate**--**2.2.1
Flask-Script**--**2.0.6
SQLAlchemy**--**1.1.11
Flask-SQLAlchemy**--**2.3.2
psycopg2-binary**--**2.7.5
alembic**--**0.9.9
python-dotenv**--**0.7.1
```

ခုဒီနေရာမှာ တော့ စာအရမ်းရှည်မှာမို့လို့ packageတခုချင်းစီကိုမရှင်းပြတော့ပါဘူးpsycopg2 ကတော့ Postgres နဲ့ ကျွန်တော် တို့ Appနဲ့ချိတ်ပေးမယ့်Driverပါ။ Alembicကတော့ Database migration အတွက် libပါ။

docker-compose.yml
------------------

ပြီးရင်တော့ DockerCompose fileတခုဆောက်ပြီးကျွန်တော် တို့ လိုအပ်တဲ့ container တခုနဲ့တခုချိတ်တဲ့ Configurationတွေကိုတွေကို သတ်မှတ်ပေးရမှာဖြစ်ပါတယ်။

project folder ထဲမှာသွား ပြီး အောက်က Commandလေးကို ရိုက်ပြီး အောက်က snippet လေးကိုကူးထည့်လိုက်ကြမယ်။

```
touch docker-compose.yml 
```<b>[other]docker-compose.yml[/other]</b>

**Compose explanation**

ဒီCompose ဖိုင် Configuration ကတော့ ၂ပိုင်းပါပါတယ် ပထမ တခုက postgres နဲ့ နောက်တခုက app ဖြစ်ပါတယ်။

```
postgres:
  restart: always
  image: postgres:10
  environment:
    - POSTGRES_USER-${DB_USER_NAME}
    - POSTGRES_PASSWORD-${DB_PASSWORD}
    - POSTGRES_DB-${DB_NAME}
  networks:
    - db_nw
  volumes:
    - ./postgres-data/postgres:/var/lib/postgresql/data
  ports:
    - "5432:5432"
```

postgres ကို စကြည့်ကြမယ် ကျွန်တော်တို့

ဒီနေရာမှာ `postgres:10` မဟုတ်ပဲတခြားVersionတွေကို လည်းမိမိလိုအပ်ချက်အလိုက်ပြောင်းလို့ရပါတယ်။

environment ကတော့ ကျွန်တော်တို့ postgres Database containerမှာ သတ်မှတ်လိုတဲ့ environment variableတွေကို သတ်မှတ်ဖို့အတွက်သုံးတာပါ။ အကယ်လို့စာရှုသူက ကြိုတင်သတ်မှတ်ထားတဲ့ Environment Variable တွေကိုသိမ်းထားတဲ့ `.env` ဖိုင်ထဲက Variableတွေကိုသုံးချင်တယ် ဆိုရင်တော့ $ signနဲ့ variable name ကိုရေးပေးလိုက်ရုံပါပဲ။ ဒါပေမယ့်ဒီနေရာမှာတခုသတိချပ်ဖို့ ကတော့ .envဖိုင်ကတော့ ProjectFolder ရဲ့ rootမှာရှိနေဖို့လိုပါတယ်။

```
DB_HOST-postgres
DB_PORT-5432
DB_NAME-db_flask_test
DB_USER_NAME-postgres
DB_PASSWORD-123456
```

volumes ကတော့ ကျွန်တော်တို့ PostgresDatabase ထဲက Dataတွေကို ဘယ်မှာသိမ်းမလဲဆိုတာကိုသတ်မှတ်ပေးဖို့ကြေငြာထားတာပါ။ သူ့ရဲ့Formatကတော့

```
[HOST LOCATION]:[CONTAINER LOCATION]
```

ဖြစ်ပါတယ်။Container ရဲ့Default path က /var/lib/postgresql/data ပါ။

**App Image**

```
app:
  restart: always
  build: .
  ports:
    - 5000:5000
  volumes: 
    - .:/app
  networks:
    - db_nw
    - web_nw
  depends_on:
    - postgres
  entrypoint: ["python", "manage.py","runserver"]
```

ဒီနေရာမှာတော့ကျွန်တော်တို့အသစ်မြင်ရတာက build ,depends_on ,entry_point

`build` ကတော့ ကျွန်တော် တို့ container ကို စမယ့် location ကိုကြေငြာပေးထားတာပါ။ခုကတော့ ကျွန်တော်ကRootကနေစဆောက်မှာမို့ . ဆိုပြီးသတ်မှတ်ထားခဲ့ပါတယ်။

`depends_on` ကတော့ ကျွန်တော် တို့ ဒီ container ကတခြားcontainer စပြီးမှစဆောက်ဖို့ကြေငြာတဲ့နေရာမှာသုံးပါတယ်။ခုဒီနေရာမှာဆိုကျွန်တော်တို့ ကpostgres containerစပြီးမှ ကျွန်တော်တို့ app ကိုစမှာမို့လို့ postgres ကို ကြေငြာပေးထားပါတယ်။ postgres ကတော့ container name ပါdatabase container name ကို တခြားနာမည်ပေးထားခဲ့ရင် တခြားနာမည်သုံးလို့ရပါတယ်။

`entry_point` ကတော့ ကျွန်တော် တို့ app ကို စမယ့် command တွေကိုကြေငြာပေးတာပါ။rails app ဆို [“rails”, “s”]ဆိုပြီးကြေငြာရမှာပါ။ခုကတော့ python app မို့လို့ ကျွန်တော်တို့ က [“python”, “app.py”] ဆိုပြီးကြေငြာထားတာပါ။

```
networks:
  db_nw:
    driver: bridge
  web_nw:
    driver: bridge
```

`networks` ကတော့ container တခုနဲ့တခု ချိတ်ဖို့ network တွေကြေငြာ ဖို့အတွက် သုံးပါတယ်။default ကတော့ bridge modeပါ။ခုလောလောဆယ် ကျွန်တော် တို့ compose မှာတော့ network နှစ်ခုသတ်မှတ်ပါမယ် database အတွက်တခု web app အတွက်တခု ဆိုပြီး ကြေငြာထားပါမယ်။အကယ်လို့ ကျွန်တော်တို့ နောက်ပိုင်းမှာ တခြား nginx လိုမျိုးContainerတွေနဲ့ ချိတ်ဆက်တဲ့ အခါ same network ကိုပဲပြန်သုံးမှာမို့လို့ပါ။

ခုnetworkလဲကြေငြာပြီးပြီ ဆိုတော့ ကြေငြာထားတဲ့ netowk ကို Apply လုပ် ပါမယ်။ ကျွန်တော် တို့ compose config ရဲ့ နေရာလေးတွေကို နဲနဲ ပြင်ပါမယ်။

```
postgres:
# Other Configs here
  networks:
    - db_nw
app:
# Other configs here  
  networks:
    - db_nw
    - web_nw
```

ပြီးရင်တော့ ကျွန်တော်တို့ docker-compose ဖိုင်ထဲ က Config တွေမှန်မမှန် စစ်ကြည့်ပါမယ်

```
docker-compose config
```

ပြီးရင်တော့ ကျွန်တော်တို့ docker-compose ကိုbuild လုပ်ပြီးRun ပါမယ်။

```
docker-compose build
docker-compose up -d
```

App run နေပြီဆိုရင်တော့ ကျွန်တော်တို့ တခြား migration scriptလိုမျိုးတွေကို container တွေက တဆင့် run လို့ရပါပြီ။

ခုကတော့ ကျွန်တော်တို့ app container ထဲ က နေတဆင့် Database migrate လုပ်မှာဖြစ်ပါတယ်။

```
docker-compose exec app python manage.py db init
docker-compose exec app python manage.py db migrate
docker-compose exec app python manage.py db upgrade
```

ပြီးရင်တော့ [http://localhost:5000](http://localhost:5000) ကိုခေါ်လိုက်ရင်တော့ ကျွန်တော်တို့ container ထဲကနေserve လုပ်ထားတဲ့ app ကိုမြင်ရမှာဖြစ်ပါတယ်။

<b>[other]Yeah[/other]</b>

Docker Commands
---------------

ဒီ နေရမှာ ကျွန်တော်ပြောချင်တာက docker-compose က docker ပေါ်မှာ လုပ်တာဖြစ်လို့ စာရှုသူအနေနဲ့ Docker commands တွေနဲ့ ရင်းနှီးနေဖို့လိုပါလိမ့်မယ်။ ကျွန်တော်ကိုယ်တိုင် လဲ မေ့တတ်လို့ ကျွန်တော် Reference လုပ်နေကျ [Cheat sheet](https://medium.com/statuscode/dockercheatsheet-9730ce03630d) လေးကို မျှဝေချင်ပါတယ်။

Conclusion (နိဂုံ:)
-------------------

တကယ်တော့ Docker ကကျွန်တော်ပြောပြတာတွေထက် ပိုပြီး လုပ်ဆောင်လို့ ရတဲ့ feature တွေ Techniques တွေရှိပါသေးတယ်။ကျွန်တော် ယခုတင်ပြ ခဲ့တာ ကတော့ ကျွန်တော်တို့ အမြဲတမ်း ကြုံတွေ့ရတဲ့ မတူညီတဲ့ development environment ပြသနာ ၊ Rapid Development လုပ်တဲ့အချိန်မှာ resource တွေကို ထိထိရောက်ရောက် အသုံးမချမိတာမျိုးတွေ ၊CI/CD readiness လဲဖြစ်စေတဲ့ပြင်ဆင်ဖို့ ဖြေရှင်းတဲ့ နည်းလမ်းများ ထဲမှာ တခုပါ။နောက်တခု က ဒီ Development Environment က တခြားမတူညီတဲ့ Databaseတွေ Service တွေကို မိနစ်ပိုင်းအတွင်း Ready ဖြစ်အောင်လုပ်ပေးနိုင်တယ်ဆိုတာနဲ့ တင် ကျွန်တော်ကတော့ သဘောကျပါတယ်။ ဒီArticleထဲမှာပါတဲ့ source sample ကတော့ [github](https://github.com/winhtaikaung/simple-flask-template) မှာတင်ပေးထားပါတယ်။ အကယ်လို့စာရှုသူမိတ်ဆွေမှာလဲ တခြားမတူညီတဲ့ ideaဖြစ်စေ ဒီarticleနဲ့ ပတ်သက်လို့ ပြောချင်တာ ရှိခဲ့ရင် Comment သို့မဟုတ် email winhtaikaung28@hotmail.com ကိုDirect ပို့ပြီး ဆွေးနွေးနိုင်ပါတယ်။


### Simple Flask Boiler Plate using DockerCompose. Contribute to winhtaikaung/simple-flask-template development by creating…

[https://github.com/winhtaikaung/simple-flask-template](https://github.com/winhtaikaung/simple-flask-template)
