# CPython İmplementation

## İmplementation elementləri

Bir dil yükləyərkən yüklədiyimiz əsas proqram **İmplementation** adlanır.

Dedik ki, bir implementation içində təkcə interpreter olmur. İndi həm digər elementləri öyrənmək, həm də ardıcıllığı bilmək üçün bir nümunə götürək: **CPython**.

Tutaq ki, sən bu proqramı yazırsan:

```python
x = 5
y = 10
print(x + y)
```

İndi görək CPython-un daxilində nə baş verir.

{% stepper %}
{% step %}
### Mətn faylı

Əvvəlcə sənin proqramın sadəcə bir mətn faylıdır: `main.py`.

İçində isə:

```python
x = 5
y = 10
print(x + y)
```

Bu hələ kompüter üçün sadəcə hərflərdir. Heç bir mənası yoxdur:

`x` ; `=` ; `5` ; `p` ; `r` ; `i`
{% endstep %}

{% step %}
### Lexer (Tokenizer)

İlk real addım budur. Lexer kodu hissələrə ayırır.

`x = 5` çevrilir:

```
IDENTIFIER x
ASSIGN =
NUMBER 5
```

Bu hissələrə **Token** deyilir.

```
Kod
↓
Token-lər
```

Bu mərhələdə hələ proqramın mənası başa düşülmür. Sadəcə sözlər ayrılır.
{% endstep %}

{% step %}
### Parser

İndi parser həmin token-lərə baxır.

Məsələn:

```
x
=
5
```

Parser deyir:

> "Aha, bu Assignment-dir."

Və belə bir struktur yaradır:

```
Assignment
  /     \
 x         5
```

Başqa misal, `print(x+y)` çevrilir:

```
Call
 │
print
 │
 +
/ \
x  y
```

Bu ağaca **AST (Abstract Syntax Tree)** deyilir.

Yəni parser artıq proqramın mənasını və strukturunu anlayır.
{% endstep %}

{% step %}
### Compiler

Burada çox adam yanılır. Python-da da compiler var. Amma o maşın kodu yaratmır. O, bunu yaradır:

```
AST
↓
Python Bytecode
```

Bytecode belə görünə bilər:

```
LOAD_CONST 5
STORE_NAME x
LOAD_CONST 10
STORE_NAME y
LOAD_NAME x
LOAD_NAME y
BINARY_ADD
PRINT
```

Compiler bu bytecode əmrlərini sadəcə havada saxlamır. O, onları Python-un daxili **Code Object (`types.CodeType`)** adlanan xüsusi strukturunda paketləyir.

Code Object özündə bu məlumatları saxlayır:

* `co_code`: Xam bytecode baytları sequence-i.
* `co_consts`: Kodda istifadə olunan bütün sabitlərin siyahısı (`(5, 10, None)`).
* `co_names`: Kodda istifadə olunan bütün adların siyahısı (`('x', 'y', 'print')`).
* `co_filename`, `co_firstlineno`: Xətaların harada olduğunu göstərmək üçün fayl adı və sətir nömrələri.

Compiler-in çıxışda verdiyi Bytecode əslində kompyuterin CPU-sunun (Intel, ARM) başa düşdüyü maşın kodu (`010101`) deyil. Bu, **Python Virtual Machine (PVM)** adlanan proqram təminatı üçün nəzərdə tutulmuş xüsusi virtual əmrlər toplusudur.

Bu artıq kompüterə daha yaxın dildir.
{% endstep %}

{% step %}
### Python Virtual Machine (PVM)

Ən vacib hissə budur. Python Virtual Machine bytecode-u oxuyur.

* `LOAD_CONST 5` deyəndə: “5-i yaddaşa qoy.”
* `STORE_NAME x` deyəndə: “Adı x olsun.”
* `BINARY_ADD` deyəndə: “İki ədədi topla.”

Belə-belə bütün proqram icra olunur.

```
Bytecode
↓
Python Virtual Machine
↓
Nəticə
```
{% endstep %}

{% step %}
### Reference Counting and Garbage Collector

RAM-ın dolmaması üçün istifadə olunmayan obyektlər silinməlidir. CPython yaddaşı təmizləmək üçün iki fərqli mexanizmdən paralel istifadə edir.

#### a) Reference Counting

Bir obyekti göstərən dəyişən sayı `0` olan kimi həmin obyekt instantly (ani olaraq) RAM-dan silinir.

Reference Counting çox yaxşı işləyir, amma onun böyük bir zəifliyi var: **Dövri İstinadlar (Cyclic References)**.

#### b) Garbage Collector (GC)

Təsəvvür et iki obyekt bir-birinə bağlandı. Dəyişənləri `a` və `b` olsun.

`a` və `b` silsək, indi xarici olaraq bu listləri tutan heç bir dəyişən yoxdur. Amma listlər bir-birinin daxilində saxlandığı üçün hər birinin `ob_refcnt`-i hələ də `1`-dir.

Reference Counting bunu silə bilmir və bu yaddaş sızmasına (Memory Leak) səbəb olur.

Bunu həll etmək üçün CPython daxilində xüsusi **Garbage Collector (GC)** modulu çalışır.
{% endstep %}

{% step %}
### Standard Library

Python icmasında məşhur bir deyim var:

> "Batteries Included" (Bütün batareyalar daxildədir).

Bu o deməkdir ki, siz Python-u kompüterinizə quraşdıran zaman CPython sizə sadəcə dili — Lexer, Parser, PVM — vermir, hətta gündəlik işlərdə lazım olacaq yüzlərlə hazır modulu da gətirir.

Standart kitabxanaya daxil olan modullar iki fərqli formada reallaşdırılır:

#### Pure Python Modulları (Xalis Python Kodu)

Bu modullar tamamilə `.py` faylları kimi yazılıb. Siz onların kodunu açıb oxuya bilərsiniz.

Nümunələr:

* `json`
* `random`
* `pathlib`
* `http.server`
* `collections`

**Necə işləyir?**

Siz `import json` yazdıqda, CPython həmin modulun `.py` (və ya keşlənmiş `.pyc`) faylını tapır və əvvəl baxdığımız 1–4-cü mərhələlərdən keçirərək PVM-də icra edir.

#### Built-in C Modulları (Daxili C Kodları)

Bəzi modullar var ki, onlar maksimum sürət və ya əməliyyat sistemi (OS) ilə aşağı səviyyəli (low-level) əlaqə tələb edir.

Onlar Python dili ilə yox, başa-baş C dilində yazılaraq CPython-un öz mənbə koduna inteqrasiya edilib.

Nümunələr:

* `math`
* `sys`
* `os`
* `time`
* `gc`
* `itertools`

**Necə işləyir?**

Siz `import math` yazanda CPython heç bir `.py` faylı axtarmır. O, C dilində yazılmış və artıq compile olunmuş xüsusi funksiyaları dəvət edir.

Məsələn, `math.sqrt()` funksiyası PVM-də virtual bytecode kimi yox, birbaşa prosessorun (CPU) riyazi təlimatları daxilində işləyir.
{% endstep %}

{% step %}
### C API

Təsəvvür et NumPy yazan adam deyir:

> “Mən bəzi hissələri Python yox, C dilində yazmaq istəyirəm.”

Bunun üçün CPython ona xüsusi interfeys verir. Bu interfeysə **Python C API** deyilir.

NumPy-nin sürətli olmasının səbəblərindən biri də budur.
{% endstep %}
{% endstepper %}

## Bütün Arxitekturanın Xülasəsi

İndi öyrəndiyimiz 8 mərhələni tək bir şəkildə birləşdirək:

1. **Source Code (`main.py`)**: Sənin yazdığın xam mətn.
2. **Lexer**: Mətni Token-lərə bölür.
3. **Parser**: Qrammatikanı yoxlayır, AST (Ağac) yaradır.
4. **Compiler**: AST-dən PVM üçün Bytecode və Code Object istehsal edir.
5. **PVM (Interpreter)**: Bytecode-u Stack-based icra edir, Standard Library və C API ilə əlaqə qurur.
6. **Memory Management & GC**: PyMalloc ilə yer ayırır, Reference Counting və Generational GC ilə yaddaşı təmiz saxlayır.
