# The journey of data (episode 2)

### 🛣️ MƏRHƏLƏ 2

{% stepper %}
{% step %}
#### 📡 Addım 1: Access Point / Switch qatında nə baş verir?

Evdəki o ağ qutu əslində tək bir cihaz deyil. Onun içində həm antenna (Access Point), həm daxili paylayıcı (Switch), həm də beynəlxalq yönləndirici (Router) var.

Layer 1 (Fiziki): Ağ qutunun antenası havada uçan o radiodalğaları tutur. Onları yenidən rəsmən rəqəmsal 0 və 1-lərə (Bitlərə) çevirir. Sonra bu bitləri birləşdirib Frame halına gətirir və bir qat yuxarıya ötürür.

Layer 2 (Data Link): Cihazın daxili Switch (Paylayıcı) mexanizmi işə düşür. O, gələn Frame-in üzərindəki Hədəf MAC ünvanına (Destination MAC) baxır:

"Aha, bu kompüter paketi mənə (yəni Routerə) göndərib. MAC ünvanı mənimki ilə eynidir. Deməli, qapını açıb paketi içəri buraxa bilərəm."

Switch paketin arxasındakı o quyruğu (FCS) yoxlayır. Əgər yolda havada parazit olubsa və data pozulubsa, Router paketi elə buradaca zibil qutusuna atır. Hər şey qaydasındadırsa, Layer 2 zirehini sökür və içindəki Paketi (Layer 3) Routerin "beyninə" ötürür.
{% endstep %}

{% step %}
#### 🧠 Addım 2: Router və Böyük NAT Sehrbazlığı (Layer 3)

Bax, bura şəbəkənin ən sehrli yeridir. Paket artıq Routerin Layer 3 (Network) qatındadır. Router paketin üzərindəki Hədəf IP-yə baxır: 142.250.185.78 (Google Drive).

Router dərhal öz beynindəki marşrut cədvəlinə (Routing Table) baxır: "Bu IP mənim evimin daxilində deyil. Mən bunu qlobal internetə, yəni mənim provayderimə (ISP) ötürməliyəm".

Amma Router paketi çölə buraxmazdan əvvəl çox kritik bir problem görür və sənin bayaq dediyin o məşhur NAT (Network Address Translation) prosesini icra edir:

🚨 Routerin daxili monoloqu: "Dayan! Bu paketin üzərində Göndərən IP olaraq 192.168.1.5 (sənin kompüterinin IP-si) yazılıb. Bu daxili (private) IP-dir. Qlobal internetdə bu IP ilə gəzmək qadağandır, çünki milyonlarla evdə eyni IP var. Google bu IP-yə birbaşa cavab qaytara bilməz. Mən bunu dəyişməliyəm!"

Router dərhal sehrli çubuğunu işə salır:

* Paketin üzərindəki 192.168.1.5 IP-sini silir.
* Yerınə provayderin (Aztelekom, CityNet və s.) ona verdiyi, dünyada unikal olan Xarici IP-ni (Public IP) yazır (Məsələn: 85.132.45.10).
* Öz yaddaşında (NAT Table) kiçik bir qeyd dəftəri açır: "Kompüter 192.168.1.5 port 52345 ilə Google-a getmək istədi. Mən onu 85.132.45.10 IP-si və tutaq ki, 61000 portu ilə maskalayıb çölə buraxdım. Google-dan cavab gələndə bu qeydə baxıb paketi yenidən daxildəki kompüterə qaytaracam".
{% endstep %}

{% step %}
#### 🚀 Addım 3: Evdən Qlobal Dünyaya Çıxış (Yenidən Aşağı Qatlara)

Router NAT əməliyyatını bitirdikdən sonra, əlində qlobal internetə çıxmağa hazır olan tam yeni bir Paket var. İndi bu paketi evdən çölə — küçədəki provayder xəttinə ötürmək lazımdır. Proses tərs qablaşdırma ilə yenidən aşağı enir:

Layer 2 (Data Link): Router paketin ətrafına yeni bir Frame zirehi çəkir. Bu dəfə Mənbə MAC ünvanı Routerin öz xarici portunun MAC ünvanı olur, Hədəf MAC ünvanı isə sənin binandakı və ya küçəndəki provayderin (ISP) daxili paylayıcı cihazının MAC ünvanı olur.

Layer 1 (Fiziki): Evindəki kabelin növünə görə (məsələn, əgər GPON/Fiber-Optik internetdirsə), router o bitləri işıq siqnallarına çevirir və optik kabel vasitəsilə inanılmaz bir sürətlə evdən çölə, yəni qlobal internet magistralına fırladır!
{% endstep %}
{% endstepper %}

### 🌐 MƏRHƏLƏ 3

#### Yoldakı Qlobal Səyahət (Layer 1, 2, 3)

Sənin paketin evdən çıxan kimi birbaşa Google-a uçmur. O, Bakı küçələrindən tutmuş okeanların dibindəki nəhəng fiber-optik kabellərə qədər minlərlə kilometr yol qət edir.

Yoldakı hər bir qlobal provayder cihazı (Böyük Routerlər) paketi qəbul edir (Layer 1), zirehinə baxır (Layer 2) və dərhal daxildəki IP-yə baxır (Layer 3): "Hədəf IP Google-dır. Mənim xəritəmə görə bu paket növbəti olaraq Almaniyadakı routerə getməlidir".

Router paketi növbəti xəttə ötürmək üçün hər dəfə Layer 2 zirehini yeniləyir (Mənbə və Hədəf MAC dəyişir), amma içindəki IP-lərə və dataya (Layer 3 və yuxarısına) toxunmur.

Data işıq sürəti ilə (Layer 1) ölkələri və qitələri aşır.

### 🏢 MƏRHƏLƏ 4

#### Google Serverinin Daxili (Aşağıdan Yuxarıya - 1-dən 7-yə)

Nəhayət, bizim paket sağ-salamat ABŞ-dakı və ya Avropadakı Google Data Mərkəzinə çatır və server binasından içəri daxil olur. İndi proses sənin kompüterindəkinin tamamilə tərsinə işləyir. Google serverinin daxilində qutular bir-bir açılır:

{% stepper %}
{% step %}
#### ⚡ Layer 1 (Physical / Fiziki Qat)

Google-ın nəhəng serverinin şəbəkə kartı kabeldən gələn o sürətli işıq və ya elektrik siqnallarını tutur. Onları rəqəmsal 0 və 1-lərə (Bitlərə) çevirir. Bitləri birləşdirib hazır bir Frame edir və yuxarı ötürür.
{% endstep %}

{% step %}
#### 🌉 Layer 2 (Data Link / Kanal Qatı)

Google-ın qapıçı sistemi Frame-in üzərindəki MAC ünvanına baxır: "Bəli, fiziki olaraq mənə gəlib". Arxasındakı riyazi quyruğu (FCS) hesablayır: "Yolda heç bir bit itməyib, data zədələnməyib". Layer 2 zirehini söküb atır. Əlimizdə qalır: Packet (Paket).
{% endstep %}

{% step %}
#### 🗺️ Layer 3 (Network / Şəbəkə Qatı)

Google-ın əməliyyat sistemi paketin üzərindəki Hədəf IP-yə baxır: "Bəli, bu mənim qlobal IP-mdir". Mənbə IP-yə baxır: "Aha, bu Azərbaycandan, 85.132.45.10 IP-li routerdən gəlir". IP qabığını da söküb atır. Əlimizdə qalır: Segment (Seqment).
{% endstep %}

{% step %}
#### 🚂 Layer 4 (Transport / Nəqliyyat Qatı)

Bax, bura həmin o vasvası konduktorun Google-dakı əkiz qardaşıdır. O, gələn seqmentlərin üzərindəki sıra nömrələrinə baxır (1, 2, 3...) və onları ardıcıllıqla düzür. Hədəf portuna baxır: 443. Müdirə (əməliyyat sisteminə) deyir: "Bu seqmentlər bizim serverdə işləyən HTTPS (şifrəli veb) xidməti üçün gəlib". Bütün seqmentləri birləşdirib tam, vahid bir kütlə halına salır. Nəqliyyat qatı vəzifəsini bitirir, seqment zirehi sökülür. Əlimizdə artıq xalis, amma hələ də şifrəli olan DATA var.
{% endstep %}

{% step %}
#### 📞 Layer 5 (Session / Seans Qatı)

Google-ın operatoru baxır ki, bu daxil olan data bayaq bizim Azərbaycandakı kompüterlə açdığımız o aktiv danışıq seansına aiddir. Seansın nömrəsini təsdiqləyir və datanı təhlükəsiz şəkildə qəbul otağına ötürür.
{% endstep %}

{% step %}
#### 🎭 Layer 6 (Presentation / Təqdimat Qatı)

Google serveri cibindən heç kəsə göstərmədiyi o gizli Private Key-ini (əsl açarını) çıxarır. Gələn o mənasız @#$!%^ simvollarından ibarət şifrəli datanı oxuyur və şaqqıltı ilə şifrəni açır (Decryption). Sonra daxildəki formata baxır: "Aha, bu UTF-8 dilində yazılmış bir HTTP sorğusudur və içində bir fayl var". Datanı tam oxunacaq, təmiz hala salır.
{% endstep %}

{% step %}
#### 👑 Layer 7 (Application / Tətbiq Qatı - FİNAL BOSS)

Nəhayət, Google-ın serverində işləyən o bulud proqramı (Google Drive arxa fon sistemi) Layer 7 interfeysi vasitəsilə təmiz HTTP sorğusunu qəbul edir:

"POST /upload ... \[Sənin göndərdiyin fayl]"

Google Drive proqramı dərhal anlayır ki, sən ona fayl yüklədin. Faylı götürür, öz disklərinə yazır və sənin kompüterinə eyni yolla tərsinə "Uğurla yükləndi!" cavab mesajını (HTTP 200 OK) göndərir. OYUN BİTDİ!
{% endstep %}
{% endstepper %}
