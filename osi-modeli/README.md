# OSI modeli

{% stepper %}
{% step %}
### 🏭 MƏRHƏLƏ 1: Sənin Kompüterinin Daxili (Qablaşdırma - Encapsulation)

Hələ ortada nə kabel var, nə Wi-Fi, nə də internet. Hər şey sənin kompüterinin yaddaşında (RAM) və prosessorunda baş verir. Data 7-ci qatdan 1-ci qata doğru enir.

Layer 7, 6, 5 (Proqram və Təhlükəsizlik): Brauzerin (Chrome) datanı götürür (L7). Sistem onu kənar şəxslər oxumasın deyə şifrələyir (L6). Google serveri ilə "danışıq seansı" təsdiqlənir (L5). Əlimizdə hələ xalis DATA var.

Layer 4 (Nəqliyyat): Kompüterin əməliyyat sistemi (Kernel) bu böyük datanı kiçik hissələrə bölür və hər birinə nömrə qoyur ki, yolda itsə bilsin. Adı olur: Segment.

Layer 3 (Şəbəkə): Sistem hər Seqmentin üzərinə virtual ünvanları yazır: Mənbə: Sənin IP-n (məs. 192.168.1.5) və Hədəf: Google-ın IP-si. Adı olur: Packet (Paket).

Layer 2 (Kanl): Sistem bu paketi Şəbəkə Kartına (NIC) ötürür. Kart onun üzərinə fiziki ünvanları möhürləyir: Mənbə: Sənin kompüterinin MAC-ı və Hədəf: Evindəki Routerin MAC-ı. Adı olur: Frame (Kadr).

VƏ NƏHAYƏT (Layer 1): Şəbəkə kartın bu tam hazır, qablaşdırılmış Frame-i 0 və 1-lərə (bitlərə) parçalayır. Əgər Wi-Fi-dırsa, antenan bunu radio dalğasına çevirib havaya fırladır.

Böyük Şəkildə Birinci Addım Bitdi: Data tam qablaşdırıldı və siqnal olaraq kompüteri tərk etdi!
{% endstep %}

{% step %}
### 🛣️ MƏRHƏLƏ 2: Evindəki Şəbəkə (Switch və Router)

Siqnal havada uçur və evindəki o ağ qutuya (Modem/Router/Access Point-ə) çatır.

Access Point (Layer 1): Havadan gələn o radio dalğalarını tutur və daxildəki naqillər üçün elektrik siqnalına çevirir.

Switch (Layer 2): Cihazın içindəki Switch gələn elektrik siqnallarını oxuyur və görür ki, bu bir Frame-dir. Dərhal onun üzərindəki MAC ünvanına baxır: "Aha, bu bizim daxili kompüterdən gəlir və Routerə getmək istəyir". Switch datanı eyni qutunun daxilindəki Router moduluna ötürür.

Router (Layer 3): Router gələn Frame-in xarici qabığını soyur və içindəki Paketə baxır. Hədəf IP-yə baxır: Google. Router deyir: "Bu mənim daxili şəbəkəmdə deyil, bunu internetə qlobal dünyaya buraxmalıyam". Bizim daxili IP-ni silir, özünün Xarici (Public) IP-sini yazır (NAT) və datanı provayderin (məs. Aztelekom, Baktelecom) kabelinə fırladır.
{% endstep %}

{% step %}
### 🌐 MƏRHƏLƏ 3: İnternet Okeanı (Qlobal Dünya)

İndi bizim paket evimizdən çıxdı. O, yolda yüzlərlə qlobal routerlərdən keçəcək.

Naqillər (Layer 1): Data fiber-optik kabellərin daxilində işıq sürəti ilə yerin altı və okeanların dibi ilə ölkədən ölkəyə qaçır.

Qlobal Routerlər (Layer 3): Yol üstündəki hər bir böyük provayder routeri paketi qəbul edir, hədəf IP-yə (Google-a) baxır, xəritəsinə baxıb onu ən qısa yolla növbəti ölkəyə ötürür. Paket real olaraq ölkələri aşıb gedir.
{% endstep %}

{% step %}
### 🏢 MƏRHƏLƏ 4: Google-ın Server Binası (Açılma - Decapsulation)

Nəhayət, paket minlərlə kilometr yol qət edərək ABŞ-da və ya Avropada yerləşən Google-ın nəhəng Data Mərkəzinə (Serverinə) çatır. İndi proses Aşağıdan Yuxarıya (1-dən 7-yə) doğru tərsinə işləyir:

Google-ın Şəbəkə Kartı (Layer 1): Kabeldən gələn işıq siqnallarını tutur və yenidən rəqəmsal 0 və 1-lərə çevirir.

Google-ın Giriş Cihazları (Layer 2): MAC ünvanını yoxlayır, hər şey doğrudursa, qabığı soyur.

Google-ın Routeri (Layer 3): Paketə baxır: "Bəli, hədəf IP mənim IP-mdir". IP qabığını da soyur və içindəki Seqmenti əməliyyat sisteminə verir.

Google-ın Əməliyyat Sistemi (Layer 4): Dağınıq gələn seqmentləri nömrələrinə görə ardıcıllıqla düzür, birləşdirir və tam bir DATA halına gətirir. Yoxlayır ki, yolda itən hissə olmadı ki? Hər şey tamdırsa, Layer 5 və 6-ya ötürür.

Şifrənin Açılması (Layer 6): Google öz gizli açarı (Private Key) ilə o gələn datanın şifrəsini açır. Məlumat artıq təmiz, oxunula bilən hala gəlir.

Google Drive Proqramı (Layer 7 - FİNAL): Google-ın serverində işləyən o bulud proqramı sənin göndərdiyin şəkli və ya yazıyı qəbul edir və öz yaddaşına yazır. Ekranda sənə "Yükləndi" bildirişi gəlir.
{% endstep %}
{% endstepper %}
