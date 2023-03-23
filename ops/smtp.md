# O'zingizning SMTP pochta jo'natish serveringizni yarating

## muqaddima

SMTP to'g'ridan-to'g'ri bulutli sotuvchilardan xizmatlarni sotib olishi mumkin, masalan:

* [Amazon SES SMTP](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali bulutli elektron pochta orqali](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Shuningdek, siz o'zingizning pochta serveringizni yaratishingiz mumkin - cheksiz jo'natish, past umumiy xarajat.

Quyida biz o'z pochta serverimizni qanday yaratishni bosqichma-bosqich ko'rsatamiz.

## Server tanlash

O'z-o'zidan joylashtirilgan SMTP serveri 25, 456 va 587 portlari ochiq bo'lgan umumiy IP-ni talab qiladi.

Odatda ishlatiladigan umumiy bulutlar bu portlarni sukut bo'yicha bloklab qo'ygan va ularni ish buyrug'i berish orqali ochish mumkin bo'lishi mumkin, ammo bu juda muammoli.

Men ushbu portlari ochiq bo'lgan va teskari domen nomlarini o'rnatishni qo'llab-quvvatlaydigan xostdan sotib olishni tavsiya qilaman.

Bu erda men [Contabo ni](https://contabo.com) tavsiya qilaman.

Contabo 2003-yilda tashkil etilgan, Germaniyaning Myunxen shahrida joylashgan hosting provayderi bo'lib, juda raqobatbardosh narxlarda.

Xarid valyutasi sifatida evroni tanlasangiz, narx arzonroq bo'ladi (8 Gb xotira va 4 protsessorga ega server yiliga taxminan 529 yuan turadi va dastlabki o'rnatish to'lovi bir yil davomida bepul).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Buyurtma berishda `prefer AMD` ko'rsating va AMD protsessorli server yaxshi ishlashga ega bo'ladi.

Quyida men o'z pochta serveringizni qanday yaratishni ko'rsatish uchun misol sifatida Contabo-ning VPS-ni olaman.

## Ubuntu tizim konfiguratsiyasi

Bu erda operatsion tizim Ubuntu 22.04

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

Agar ssh serverida `Welcome to TinyCore 13!` (quyidagi rasmda ko'rsatilganidek), bu tizim hali o'rnatilmaganligini anglatadi. Iltimos, ssh-ni uzing va tizimga qayta kirish uchun bir necha daqiqa kuting.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

`Welcome to Ubuntu 22.04.1 LTS` paydo bo'lganda, ishga tushirish tugallandi va siz quyidagi bosqichlarni davom ettirishingiz mumkin.

### [Ixtiyoriy] Rivojlanish muhitini ishga tushiring

Bu qadam ixtiyoriy.

Qulaylik uchun [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) ga ubuntu dasturini o'rnatish va tizim konfiguratsiyasini qo'ydim.

Bir marta bosish bilan o'rnatish uchun quyidagi buyruqni bajaring.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Xitoylik foydalanuvchilar, iltimos, uning o'rniga quyidagi buyruqdan foydalaning va til, vaqt mintaqasi va boshqalar avtomatik ravishda o'rnatiladi.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Contabo IPV6 ni yoqadi

SMTP ham IPV6 manzillari bilan elektron pochta xabarlarini yuborishi uchun IPV6 ni yoqing.

`/etc/sysctl.conf` ni tahrirlang

Quyidagi qatorlarni o'zgartiring yoki qo'shing

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

[Contabo qoʻllanmasini bajaring: Serveringizga IPv6 ulanishini qoʻshish](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

`/etc/netplan/01-netcfg.yaml` ni tahrirlang, quyidagi rasmda ko'rsatilganidek, bir nechta qatorlarni qo'shing (Contabo VPS standart konfiguratsiya faylida allaqachon bu qatorlar mavjud, ularni izohsiz qoldiring).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Keyin o'zgartirilgan konfiguratsiya kuchga kirishi uchun `netplan apply` .

Konfiguratsiya muvaffaqiyatli bo'lgandan so'ng, tashqi tarmog'ingizning ipv6 manzilini ko'rish uchun `curl 6.ipw.cn` foydalanishingiz mumkin.

## Konfiguratsiya ombori operatsiyalarini klonlash

```
git clone https://github.com/wactax/ops.soft.git
```

## Domen nomingiz uchun bepul SSL sertifikatini yarating

Pochta jo‘natish shifrlash va imzolash uchun SSL sertifikatini talab qiladi.

Sertifikatlarni yaratish uchun biz [acme.sh](https://github.com/acmesh-official/acme.sh) dan foydalanamiz.

acme.sh ochiq manbali avtomatlashtirilgan sertifikat imzolash vositasidir,

Ops.soft konfiguratsiya omborini kiriting, `./ssl.sh` ishga tushiring va **yuqori katalogda** `conf` papkasi yaratiladi.

[Acme.sh dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) dan DNS provayderingizni toping, `conf/conf.sh` ni tahrirlang.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Keyin domen nomingiz uchun `123.com` va `*.123.com` sertifikatlarini yaratish uchun `./ssl.sh 123.com` ishga tushiring.

Birinchi ishga tushirish avtomatik ravishda [acme.sh ni](https://github.com/acmesh-official/acme.sh) o'rnatadi va avtomatik yangilash uchun rejalashtirilgan vazifani qo'shadi. Siz `crontab -l` ko'rishingiz mumkin, quyidagi qator mavjud.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Yaratilgan sertifikat uchun yo'l `/mnt/www/.acme.sh/123.com_ecc。`

Sertifikatni yangilash `conf/reload/123.com.sh` skriptini chaqiradi, ushbu skriptni tahrirlang, tegishli ilovalarning sertifikat keshini yangilash uchun `nginx -s reload` kabi buyruqlarni qo'shishingiz mumkin.

## Chasquid bilan SMTP serverini yarating

[chasquid](https://github.com/albertito/chasquid) Go tilida yozilgan ochiq manbali SMTP serveridir.

Postfix va Sendmail kabi qadimiy pochta serveri dasturlari o'rnini bosuvchi sifatida chasquid oddiyroq va ishlatish uchun qulayroq va ikkinchi darajali rivojlanish uchun ham osonroqdir.

`./chasquid/init.sh 123.com` bir marta bosish bilan avtomatik ravishda o'rnatiladi (123.com ni jo'natuvchi domen nomingiz bilan almashtiring).

## DKIM elektron pochta imzosini sozlang

DKIM xatlarni spam sifatida qabul qilishning oldini olish uchun elektron pochta imzolarini yuborish uchun ishlatiladi.

Buyruq muvaffaqiyatli bajarilgandan so'ng, sizdan DKIM yozuvini o'rnatish so'raladi (quyida ko'rsatilganidek).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

DNS-ga TXT yozuvini qo'shing (quyida ko'rsatilganidek).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Xizmat holati va jurnallarni ko'rish

 `systemctl status chasquid` Xizmat holatini ko'rish.

Oddiy ish holati quyidagi rasmda ko'rsatilganidek

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` yoki `journalctl -xeu chasquid` xatolar jurnalini ko'rishi mumkin.

## Teskari domen nomi konfiguratsiyasi

Teskari domen nomi IP-manzilni tegishli domen nomi bilan hal qilish imkonini beradi.

Teskari domen nomini o'rnatish elektron pochta xabarlarini spam sifatida aniqlashning oldini oladi.

Xat qabul qilinganda, qabul qiluvchi server jo'natuvchi serverning to'g'ri teskari domen nomiga ega ekanligini tasdiqlash uchun jo'natuvchi serverning IP-manzilida teskari domen nomi tahlilini amalga oshiradi.

Agar jo'natuvchi serverda teskari domen nomi bo'lmasa yoki teskari domen nomi jo'natuvchi serverning IP manziliga mos kelmasa, qabul qiluvchi server elektron pochtani spam deb bilishi yoki uni rad etishi mumkin.

[https://my.contabo.com/rdns](https://my.contabo.com/rdns) saytiga tashrif buyuring va quyida ko'rsatilgandek sozlang

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Teskari domen nomini o'rnatganingizdan so'ng, IPv4 va ipv6 domen nomining serverga to'g'ridan-to'g'ri ruxsatini sozlashni unutmang.

## chasquid.conf xost nomini tahrirlang

`conf/chasquid/chasquid.conf` teskari domen nomi qiymatiga o'zgartiring.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Keyin xizmatni qayta ishga tushirish uchun `systemctl restart chasquid` dasturini ishga tushiring.

## Conf ni git omboriga zaxiralang

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Misol uchun, men conf jildini o'zimning github jarayonimga quyidagi tarzda zaxiralayman

Avval shaxsiy omborni yarating

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

conf katalogini kiriting va omborga yuboring

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Yuboruvchini qo'shing

yugur

```
chasquid-util user-add i@wac.tax
```

Yuboruvchini qo'shish mumkin

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Parol to'g'ri o'rnatilganligini tekshiring

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

Foydalanuvchini qo'shgandan so'ng, `chasquid/domains/wac.tax/users` yangilanadi, uni omborga topshirishni unutmang.

## DNS SPF yozuvini qo'shadi

SPF (Sender Policy Framework) elektron pochta orqali firibgarlikning oldini olish uchun foydalaniladigan elektron pochtani tekshirish texnologiyasidir.

U pochta jo‘natuvchining identifikatorini jo‘natuvchining IP-manzili u da’vo qilgan domen nomining DNS yozuvlariga mos kelishini tekshirib, firibgarlarning soxta elektron pochta xabarlarini yuborishining oldini oladi.

SPF yozuvlarini qo'shish elektron pochta xabarlarini iloji boricha spam sifatida aniqlashning oldini oladi.

Agar sizning domen nomi serveringiz SPF turini qo'llab-quvvatlamasa, shunchaki TXT tipidagi yozuvni qo'shing.

Masalan, `wac.tax` ning SPF darajasi quyidagicha

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

`_spf.wac.tax` uchun SPF

`v=spf1 a:smtp.wac.tax ~all`

Esda tutingki, men bu yerda `include:_spf.google.com` kiritdim, chunki men `i@wac.tax` manzilini keyinroq Google pochta qutisiga jo‘natuvchi manzil sifatida sozlayman.

## DNS konfiguratsiyasi DMARC

DMARC — (Domain-based Message Authentication, Reporting & Conformance) qisqartmasi.

U SPF sakrashlarini yozib olish uchun ishlatiladi (ehtimol konfiguratsiya xatolaridan kelib chiqqan yoki kimdir sizni spam yuborayotgandek ko'rsatmoqda).

TXT yozuvini qo'shing `_dmarc` ,

Tarkibi quyidagicha

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Har bir parametrning ma'nosi quyidagicha

### p (siyosat)

SPF (Sender Policy Framework) yoki DKIM (DomainKeys Identified Mail) tekshiruvidan o‘tmagan elektron pochta xabarlarini qanday boshqarish kerakligini ko‘rsatadi. p parametri uchta qiymatdan biriga o'rnatilishi mumkin:

* yo'q: Hech qanday chora ko'rilmaydi, faqat tekshirish natijasi elektron pochta xabarlari mexanizmi orqali jo'natuvchiga qaytariladi.
* Karantin: Tekshiruvdan o'tmagan xatni spam jildiga qo'ying, lekin to'g'ridan-to'g'ri xatni rad etmaydi.
* rad etish: Tekshirishda muvaffaqiyatsizlikka uchragan elektron pochta xabarlarini bevosita rad etish.

### fo (Muvaffaqiyatsizlik variantlari)

Hisobot mexanizmi tomonidan qaytarilgan ma'lumotlar miqdorini belgilaydi. U quyidagi qiymatlardan biriga o'rnatilishi mumkin:

* 0: Barcha xabarlar uchun tekshirish natijalari haqida xabar bering
* 1: Faqat tekshirilmagan xabarlar haqida xabar bering
* d: Faqat domen nomini tekshirishda xatoliklar haqida xabar bering
* s: faqat SPF tekshiruvi xatoliklari haqida xabar bering
* l: Faqat DKIM tekshiruvidagi xatolar haqida xabar bering

### rua va ruf

* rua (Aggregate hisobotlar uchun URI hisoboti): Birlashtirilgan hisobotlarni qabul qilish uchun elektron pochta manzili
* ruf (Sud ekspertizasi hisobotlari uchun URI hisoboti): batafsil hisobotlarni olish uchun elektron pochta manzili

## E-pochtalarni Google Mail-ga yuborish uchun MX yozuvlarini qo'shing

Men universal manzillarni qo‘llab-quvvatlaydigan bepul korporativ pochta qutisini topa olmaganligim sababli (Catch-All, ushbu domen nomiga yuborilgan har qanday elektron pochta xabarlarini prefikslarga cheklanmagan holda qabul qila oladi), men barcha xatlarni Gmail pochta qutimga yo‘naltirish uchun chasquid-dan foydalanardim.

**Agar sizning shaxsiy pullik biznes pochta qutingiz bo'lsa, MX-ni o'zgartirmang va bu bosqichni o'tkazib yubormang.**

`conf/chasquid/domains/wac.tax/aliases` tahrirlang, pochta qutisini yo'naltirishni o'rnating

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` barcha elektron pochta xabarlarini bildiradi, `i` yuqorida yaratilgan yuboruvchi foydalanuvchining elektron pochta manzili prefiksidir. Pochtani yo'naltirish uchun har bir foydalanuvchi qator qo'shishi kerak.

Keyin MX yozuvini qo'shing (quyidagi rasmdagi birinchi qatorda ko'rsatilganidek, bu erda to'g'ridan-to'g'ri teskari domen nomining manziliga ishora qilaman).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Konfiguratsiya tugallangandan so'ng, siz Gmail orqali xatlarni qabul qilishingiz mumkinligini bilish uchun `i@wac.tax` va `any123@wac.tax` manzillariga elektron pochta xabarlarini yuborish uchun boshqa elektron pochta manzillaridan foydalanishingiz mumkin.

Aks holda, chasquid jurnalini tekshiring ( `grep chasquid /var/log/syslog` ).

## Google Mail orqali i@wac.tax manziliga xat yuboring

Google Mail xatni olgandan so'ng, men tabiiy ravishda i.wac.tax@gmail.com o'rniga `i@wac.tax` bilan javob berishga umid qildim.

[https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) saytiga tashrif buyuring va "Boshqa elektron pochta manzilini qo'shish" tugmasini bosing.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Keyin, yuborilgan elektron pochta orqali olingan tasdiqlash kodini kiriting.

Nihoyat, u standart jo'natuvchi manzili sifatida o'rnatilishi mumkin (bir xil manzil bilan javob berish imkoniyati bilan birga).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Shunday qilib, biz SMTP pochta serverini yaratishni yakunladik va shu bilan birga elektron pochta xabarlarini yuborish va qabul qilish uchun Google Mail-dan foydalanamiz.

## Konfiguratsiya muvaffaqiyatli yoki yo'qligini tekshirish uchun test elektron pochta xabarini yuboring

`ops/chasquid` kiriting

`direnv allow` (direnv oldingi bir tugmani ishga tushirish jarayonida o'rnatilgan va qobiqqa ilgak qo'shilgan)

keyin yugur

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Parametrlarning ma'nosi quyidagicha

* foydalanuvchi: SMTP foydalanuvchi nomi
* o'tish: SMTP paroli
* kimga: oluvchi

Siz test elektron pochta xabarini yuborishingiz mumkin.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Konfiguratsiyalar muvaffaqiyatli yoki yo'qligini tekshirish uchun test elektron pochta xabarlarini olish uchun Gmail-dan foydalanish tavsiya etiladi.

### TLS standart shifrlash

Quyidagi rasmda ko'rsatilganidek, bu kichik qulf mavjud, bu SSL sertifikati muvaffaqiyatli yoqilganligini anglatadi.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Keyin "Asl elektron pochtani ko'rsatish" tugmasini bosing.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Quyidagi rasmda ko'rsatilganidek, Gmail asl pochta sahifasida DKIM ko'rsatiladi, bu DKIM konfiguratsiyasi muvaffaqiyatli bo'lganligini anglatadi.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Asl e-pochtaning sarlavhasida Qabul qilingan ni tekshiring va jo'natuvchi manzili IPV6 ekanligini ko'rishingiz mumkin, ya'ni IPV6 ham muvaffaqiyatli sozlangan.
