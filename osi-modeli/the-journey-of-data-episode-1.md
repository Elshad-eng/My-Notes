# The journey of data (episode 1)

## 👑 Layer 7: Application Layer (Tətbiq Qatı) – Giriş Qapısı

Çox insanın qarışdırdığı ən birinci şey budur: Layer 7 sənin Chrome brauzerinin özü deyil. Chrome bir proqramdır (Application). Layer 7 isə Chrome brauzerinin şəbəkə ilə (internetlə) danışmaq üçün istifadə etdiyi interfeys və protokollardır. Yəni proqramla şəbəkə arasındakı tərcüməçi pəncərədir.

🛠️ Realda nə baş verir?

Sən Google Drive-a bir şəkil yükləmək istəyirsən və "Upload" (Yüklə) düyməsinə klikləyirsən. Həmin an Chrome brauzeri əməliyyat sisteminə müraciət edir. Amma əməliyyat sisteminə "mən şəkil göndərmək istəyirəm" demir. O, Layer 7-nin daxilindəki məşhur bir protokoldan istifadə edir: HTTP (Hypertext Transfer Protocol).

Chrome sənin o şəklini götürür və onun önünə Layer 7 səviyyəli bir başlıq (HTTP Header) qoyur. Bu başlığın içində kompüter dilində təxminən belə bir əmr yazılır:

"POST /upload HTTP/1.1 \r\n Host: drive.google.com ..."

(Tərcüməsi: "Google Drive, mən sənə HTTP protokolu vasitəsilə bir fayl göndərirəm, qəbul et").

🎛️ Bu qatın "Səlahiyyətləri" və Vəzifəsi nədir?

* **İstifadəçi ilə Təmas:** İnsanın kompüterdə etdiyi hərəkəti (klikləməyi, yazı yazmağı) şəbəkənin başa düşəcəyi protokola çevirmək.
* **Protokol Təyini:** Datanın növünə görə protokol seçir. Əgər veb saytdırsa HTTP, əgər bir fayl serverinə birbaşa fayl atırsansa FTP, əgər Outlook-dan mail göndərirsənsə SMTP protokolunu işə salır.

## 🎭 Layer 6: Presentation Layer (Təqdimat Qatı) – Tərcüməçi və Şifrələyici

Bu qatın əsas vəzifəsi datanın necə görünəcəyini və necə qorunacağını təyin etməkdir. O, datanın məzmununu dəyişmir, sadəcə onun forması ilə məşğul olur.

Sənin kompüterinin daxilində bu qatın icra etdiyi 3 böyük missiya var:

{% stepper %}
{% step %}
### 1. Şifrələmə (Encryption / TLS-HTTPS)

Bayaq danışdığımız o möhtəşəm "Qutu və Kilid" oyunu məhz bu qatda reallaşır. Layer 7-dən gələn o xalis fayl və HTTP başlığı tamamilə açıq mətndir (clear text). Layer 6 deyir: "Dayan! Bu data internetə belə açıq çıxa bilməz, xakerlər oğurlayar".

O, dərhal işə düşür və Google-dan aldığı o kilidlə (Public Key) datanı tanınmaz hala salır, yəni şifrələyir. Bu prosesə TLS/SSL şifrələməsi deyilir və brauzerdə gördüyün o HTTP-ni HTTPS-ə çevirən yer məhz buradır.
{% endstep %}

{% step %}
### 2. Formatlaşdırma (Formatting)

Kompüter daxilində şəkillər (JPEG, PNG), videolar (MP4), yazılar (ASCII, UTF-8) fərqli dillərdə olur. Layer 6 bu datanın formatını elə bir standart formaya salır ki, qarşı tərəfdəki Google serveri onu qəbul edəndə "Bu nə sənədidir, mən bunu aça bilmirəm" deməsin. Datanın vizual kimliyini təyin edir.
{% endstep %}

{% step %}
### 3. Sıxışdırma (Compression)

Əgər göndərdiyin fayl çox böyükdürsə, Layer 6 internet xəttini doldurmamaq üçün datanı daxildə bir az sıxışdırır (eyni WinRAR proqramı kimi, amma bunu şəbəkə səviyyəsində avtomatik edir).
{% endstep %}
{% endstepper %}

## 📞 Layer 5: Session Layer (Seans Qatı) – Şəbəkə Operatoru

Bu qatın real olaraq gördüyü iş, iki fərqli kompüter (sənin kompüterin və Google serveri) arasında virtual bir rabitə kanalını (seansı) açmaq, idarə etmək və iş bitəndə təhlükəsiz şəkildə bağlamaqdır.

Sən Google Drive-da düyməyə basdığın an, bu qatın icra etdiyi 3 əsas vəzifə var:

{% stepper %}
{% step %}
### 1. Seansın Açılması (Authentication və Authorization)

Layer 5 Google serverinə zəng edir: "Salam, mən filan istifadəçiyəm, Google Drive qovluğuma fayl yükləmək üçün icazə istəyirəm". Google baxır ki, sən öz hesabına daxil olmusan (Authentication) və bəli, bu qovluğa fayl atmaq hüququn var (Authorization). Layer 5 bu təsdiqi alan kimi iki cihaz arasında virtual bir "danışıq tuneli" (Seans) rəsmiləşdirir.
{% endstep %}

{% step %}
### 2. Sinxronizasiya və Nəzarət Nöqtələri (Checkpoints)

Bu, xüsusilə böyük fayllar yükləyərkən həyat xilas edən bir funksiyadır. Layer 5 göndərilən datanın arasına gizli "Yoxlama Nöqtələri" (Checkpoints) qoyur.

Nümunə: Təsəvvür et ki, 1 GB-lıq bir video yükləyirsən. 500 MB yüklənəndə evdə işıq sönür və ya Wi-Fi kəsilir. İnternet geri gələndə fayl 0-dan yox, məhz qaldığı yerdən (500 MB-dan) davam edir axı? Bax, bunu təmin edən Layer 5-dir. O, serverlə davamlı olaraq danışır: "Bura qədər hər şey çatdı? Bəli. Əla, işarə qoyaq, davam edək".
{% endstep %}

{% step %}
### 3. Seansın Bağlanması

Fayl tam yüklənib bitdikdən sonra, Layer 5 hər iki tərəfə xəbər verir ki, iş bitdi. Serverin resurslarını boş yerə məşğul etməmək üçün seansı sivil qaydada bağlayır.
{% endstep %}
{% endstepper %}

## 🚂 Layer 4: Transport Layer (Nəqliyyat Qatı) – Vasvası Konduktor

Bu qatın əsas vəzifəsi datanı sağ-salamat, itkisiz və düzgün qapıya (porta) çatdırmaqdır. O, datanın məzmunu ilə maraqlanmır, onun üçün əsas məsələ daşınmadır.

Kompüterinin daxilində bu qat işə düşən an 3 kritik hadisə baş verir:

{% stepper %}
{% step %}
### 1. İlk Parçalanma: Segment (Seqment) Yaradılır!

Layer 4 yuxarıdan gələn o böyük dataya baxıb deyir: "Bu çox böyükdür. Mən bunu internet xətti ilə bir dəfəyə göndərə bilmərəm (çünki şəbəkə xətlərinin MTU adlanan bir daşıma limiti var). Mən bunu hissələrə bölməliyəm".

Datanı kiçik parçalara ayırır və hər parçanın önünə bir Layer 4 Başlığı (Header) yapışdırır. Hər başlığa bir sıra nömrəsi (Sequence Number: 1, 2, 3...) verir ki, qarşı tərəf onları yenidən ardıcıllıqla yığa bilsin.

📦 Datanın yeni adı: Segment (Seqment).
{% endstep %}

{% step %}
### 2. Port nömrələrinin təyini (Ünvanlama)

Layer 4 seqmentin üzərinə gedəcəyi və çıxdığı qapıların (portların) nömrəsini yazır.

Mənbə Portu (Source Port): Sənin kompüterinin Chrome üçün ayırdığı müvəqqəti, təsadüfi bir qapı (Məsələn: 52345). Google cavab qaytaranda bu qapıya göndərəcək.

Hədəf Portu (Destination Port): Google-ın bizi gözləyən qapısı. Biz HTTPS (şifrəli veb) ilə getdiyimiz üçün bu port standart olaraq 443-dür.
{% endstep %}

{% step %}
### 3. Protokol Seçimi: TCP, yoxsa UDP? 🤝

Layer 4 datanın növünə baxıb iki fərqli daşıma üsulundan birini seçir:

TCP (Etibarlı): Bizim ssenaridə (Google Drive-a fayl yükləyirik) bircə bit belə itsə, şəkil və ya fayl korlanar. Ona görə Layer 4 TCP protokolunu seçir. TCP vasvasıdır. Bayaq danışdığımız o TCP Handshake (açıq salamlaşma) siqnallarını hazırlayan və yolda itən seqment olanda "Dərhal o 3 nömrəli hissəni mənə yenidən göndər!" deyə tələb edən mexanizm məhz buradadır.

(Əgər sən onlayn oyun oynasaydın və ya canlı yayım izləsəydin, o sürət üçün UDP-ni seçərdi).
{% endstep %}
{% endstepper %}

## 🗺️ Layer 3: Network Layer (Şəbəkə Qatı) – Virtual Coğrafiyaçı

Layer 4-dən gələn seqmentlərdə port nömrələri var (yəni hansı proqramdan çıxıb hansı proqrama gedəcəyi bəllidir). Amma hələ də ən vacib sualın cavabı yoxdur: Bu məlumat dünya xəritəsində hansı kompüterdən çıxır və qlobal internetdə hansı fiziki serverə gedir?

Bax, Layer 3-ün yeganə və ən böyük missiyası bu seqmentləri qlobal dünyada ünvanlandırmaqdır. Burada 2 kritik hadisə baş verir:

{% stepper %}
{% step %}
### 1. Yeni Qablaşdırma: Packet (Paket) Yaradılır! 📦

Layer 3 yuxarıdan gələn hər bir seqmenti götürür və onu daha böyük bir qutunun içinə qoyur. Bu qutunun önünə Layer 3 Başlığı (IP Header) yapışdırır. Bu başlığın içində ən vacib virtual ünvanlar yazılır:

Mənbə IP (Source IP): Sənin kompüterinin ev şəbəkəsindəki daxili IP ünvanı (Məsələn: 192.168.1.5).

Hədəf IP (Destination IP): Google Drive serverinin qlobal internetdəki unikal IP ünvanı (Məsələn: 142.250.185.78).

📦 Datanın yeni adı: Packet (Paket).
{% endstep %}

{% step %}
### 2. Yolun Təyin Olunması Məntiqi (Routing) 🛣️

Kompüterin daxilindəki Layer 3 hədəf IP-yə (Google-a) baxır və dərhal öz daxili marşrut cədvəlinə (Routing Table) göz gəzdirir: "Bu IP mənim evimin daxilində deyil. Deməli, mən bu paketi evdən çölə çıxarmaq üçün bizim 'Çıxış Qapısına', şəbəkə dili ilə desək Default Gateway-ə (yəni evdəki Router-ə) göndərməliyəm".

⚠️ Çox vacib NAT qeydi: Sən əvvəlki söhbətimizdə demişdin ki, “Router onların üzərinə öz IP ünvanını yazır (NAT-a görə)”. Tamamilə haqlısan! Amma unutma, hələ kompüterin daxilindəyik. Kompüter paketi hazırlayanda bura öz daxili IP-sini (192.168.1.5) yazır. Paket evdən çıxanda, yəni Routerin daxilindən keçəndə, Router o paketi tutub Layer 3 səviyyəsində NAT əməliyyatını edəcək və IP-ni dəyişəcək. Amma paketin ilk doğulduğu yer olan sənin kompüterində hər şey sənin öz IP-n ilə başlayır.
{% endstep %}
{% endstepper %}

## 🌉 Layer 2: Data Link Layer (Kanal Qatı) – Pasport Nəzarəti

Layer 3-dən gələn paketlərdə IP-lər var (qlobal dünya ünvanları). Amma bizim kompüter o paketi birbaşa okeanın o biri tayındakı Google-a fırlada bilməz, çünki onun qolu o qədər uzun deyil. Kompüterin edə biləcəyi yeganə şey, o paketi evdəki o ağ qutuya (Router/Modemə) çatdırmaqdır.

Bax, Layer 2 qlobal ünvanlarla (IP) yox, lokal (eyni otaqdakı) cihazların fiziki ünvanları ilə işləyir. Burada 2 kritik hadisə baş verir:

{% stepper %}
{% step %}
### 1. Son Qablaşdırma: Frame (Kadr) Yaradılır! 🖼️

Kompüterinin şəbəkə kartı (NIC) yuxarıdan gələn paketi götürür və onu sonuncu zirehli qutunun içinə qoyur. Önünə Layer 2 Başlığı (MAC Header) yapışdırır. Ora dünyanın ən dəqiq, heç vaxt dəyişməyən fiziki ünvanlarını yazır:

Mənbə MAC (Source MAC): Sənin kompüterinin şəbəkə kartının zavodda üzərinə qazınmış unikal fiziki kodu.

Hədəf MAC (Destination MAC): Evindəki Routerin şəbəkə kartının MAC ünvanı. (Kompüterin bu MAC ünvanını tapmaq üçün daxildə ARP adlanan xüsusi bir sorğu mexanizmi işlədir).

🖼️ Datanın yeni adı: Frame (Kadr və ya Blok).
{% endstep %}

{% step %}
### 2. Xətaların Yoxlanılması (Trailer - Quyruq) 🛑

Layer 2 digər qatlardan fərqli olaraq paketin yalnız önünə yox, həm də arxasına (Trailer/Quyruq) bir kod yapışdırır (buna FCS - Frame Check Sequence deyilir). Bu bir növ riyazi təhlükəsizlik kilididir. Məqsəd budur: data yolda gedərkən havada və ya kabeldə parazitə məruz qalıb dəyişilsə, qarşı tərəf (Router) bu quyruqdakı koda baxıb dərhal anlayacaq ki, data yolda zədələnib və onu zibil qutusuna atacaq.
{% endstep %}
{% endstepper %}

## ⚡ Layer 1: Physical Layer (Fiziki Qat) – Enerji Canavarı

Layer 2-dən gələn Frame hələ də kompüterin beynindəki rəqəmsal bir koddur. Amma kabellər və ya hava bu rəqəmsal kodları (faylları, şəkilləri) birbaşa daşıya bilməz. Onlar ancaq fiziki enerjini başa düşürlər.

Bax, Layer 1-in yeganə və ən böyük missiyası həmin o rəqəmsal seyfi (Frame-i) fiziki enerjiyə çevirib çölə fırlatmaqdır. Burada 2 kritik hadisə baş verir:

{% stepper %}
{% step %}
### 1. Son Parçalanma: Bits (Bitlər - 0 və 1-lər) 🔢

Layer 1 o hazır Frame-i götürür və onu kompüterin ən kiçik vahidlərinə — 0 və 1-lərə (Bitlərə) parçalayır. Şəbəkədə hərəkət edən ən təməl element budur.

📦 Datanın son adı: Bits (Bitlər).
{% endstep %}

{% step %}
### 2. Modulyasiya və Siqnala Çevrilmə (Fiziki Ötürülmə) 📡

Şəbəkə kartın bu 0 və 1-ləri sənin istifadə etdiyin şəbəkə mediasının (daşıyıcısının) növünə görə fiziki siqnallara çevirir:

* Əgər Wi-Fi istifadə edirsənsə (Naqilsiz): Şəbəkə kartının antenası işə düşür. O, 1 gələndə radiodalğanın tezliyini və ya fazasını dəyişir, 0 gələndə fərqli bir dalğa yayır. Yəni rəqəmsal bitləri havada uçan radiodalğalara çevirir.
* Əgər Ethernet Kabeli istifadə edirsənsə (Mis naqil): Şəbəkə kartının portundan kabelə elektrik gərginliyi buraxılır. Məsələn: +5 Volt gərginlik 1 deməkdir, 0 Volt gərginlik isə 0 deməkdir. Datanı elektrik siqnallarına çevirir.
* Əgər Fiber-Optikdirsə (Şüşə kabel): Kart o bitləri işıq yanıb-sönmələrinə (lazer siqnallarına) çevirir (İşıq yandı = 1, işıq söndü = 0).
{% endstep %}
{% endstepper %}
