# Regulární výrazy

Dnes si budeme povídat o zdánlivě složitých a nesmyslných zápisech, jako je například tento:

```
r"^\w+([\.-]?\w)+@\w+([\.]?\w)+(\.[a-zA-Z]{2,3})+$"
```

## Obecná definice

Regulární výraz (*regular expression*, *regex* či *regexp*) je řetězec, který s pomocí znaků se speciálním významem definuje vzor pro vyhledávání v textu.

Obecně řečeno pochází regulární výrazy z oblasti teoretické informatiky a teorie formálních jazyků. Teorie a formální definice jsou k nalezení na [české](https://cs.wikipedia.org/wiki/Regul%C3%A1rn%C3%AD_v%C3%BDraz#Form%C3%A1ln%C3%AD_definice) i [anglické Wikipedii](https://en.wikipedia.org/wiki/Regular_expression#Formal_language_theory) a obsahují spoustu odkazů pro zvídavé. Je to zajímavé a velmi obsáhlé téma.

Regulární výrazy se při práci s textem nejčastěji používají v těchto případech:
* zjištění, zda nějaký text vyhovuje regulárnímu výrazu (validace vstupů)
* hledání v textu – zjištění, kde se v textu nachází hledaný podřetězec
* záměna podřetězce v řetězci (najít a nahradit)
* extrakce všech výskytů podřetězce

## Zápis regulárních výrazů

Regulární výrazy se v Pythonu zapisují do tzv. raw stringu `r""`, který zajistí, že speciální sekvence jako např. `\n` nebudou mít svůj speciální význam.

```pycon
>>> print("hello\nworld")
hello
world
>>> print(r"hello\nworld")
hello\nworld
```

Stejně tak to funguje i v případě, že se Python marně snaží dopídit speciálního významu nějaké sekvence.

```pycon
>>> print("hello\xworld")
  File "<stdin>", line 1
SyntaxError: (unicode error) 'unicodeescape' codec can't decode bytes in position 5-6: truncated \xXX escape
>>> print(r"hello\xworld")
hello\xworld
```

Raw stringy jsou obyčejné řetězce jen s jinou formou zápisu. Regulární výrazy můžeme psát i jako obyčejné řetězce,
ale budeme si pak muset sami pohlídat interpretaci speciálních sekvencí, což nám regulární výraz často zbytečně zkomplikuje
řadou zdvojených lomítek.

```pycon
>>> print("\\x\\n\\x")
\x\n\x
>>> print(r"\x\n\x")
\x\n\x
>>> "\\x\\n\\x" == r"\x\n\x"
True
>>> type("\\x\\n\\x") == type(r"\x\n\x")
True
```

## Znaky se speciálním významem

Následující znaky mají při zápisu regulárních výrazů speciální význam. To mimo jiné znamená, že pokud je chceme použít bez jejich speciálního významu, je nutné dát před znak `\` (tzv. je escapovat).

Například `.` je zástupným znakem pro jakýkoli jiný znak. Regulární výraz pro libovolné dva znaky, mezi kterými je tečka, by tedy
vypadal následovně: `.\..`. Podobně `\\` znamená jedno zpětné lomítko.

> [note]
> Výčet není kompletní, může se lišit v různých implementacích a programovacích jazycích,
> může záviset na nastavení systému a užití znaků z Unicode.
> Je to zkrátka komplexní technologie.
> Pro co nejlepší přehled samozřejmě doporučujeme [dokumentaci](https://docs.python.org/3/library/re.html).

### Kvantifikátory

Kvantifikátor je znak, který nám určuje, kolikrát se v řetězci může opakovat znak, který je před kvantifikátorem.

Kvantifikátory máme následující:
* `?` znamená žádný nebo 1 výskyt
* `*` znamená žádný a více výskytů
* `+` znamená 1 a více výskytů
* `{cislo}` znamená přesný počet výskytů
* `{min,max}` znamená počet výskytů mezi horní a dolní mezí (včetně obou mezí samotných)

#### Příklady

* Zápisu `ab?c` odpovídají jen řetězce `ac` a `abc`
* Zápisu `ab*c` odpovídají řetězce `ac`, `abc`, `abbc`, `abbbc` …
* Zápisu `ab+c` odpovídají řetězce `abc`, `abbc`, `abbbc` …
* Zápisu `ab{2}c` odpovídá jen jeden řetězec `abbc`
* Zápisu `ab{2,4}c` odpovídají jen tyto řetězce `abbc`, `abbbc` a `abbbbc`

### Zástupné znaky

* `.` je zástupným znakem pro jakýkoli jiný znak
* `^` označuje začátek řetězce (případně řádku)
* `$` označuje konec řetězce (případně řádku)

#### Příklady

* Zápisu `a.c` odpovídá mnoho řetězců, např.: `a c`, `a!c`, `a/c`, `a0c`, `a1c`, `a2c`, `a3c`, `aAc`,`aBc`, `aCc`, `aac`, `abc`, `acc`, `azc`
* Zápisu `^abc.*` bude odpovídat jen řetězec, který začíná na `abc`, tedy `abccc` ano, ale `abbcc` už ne

### Skupiny znaků

Skupiny znaků v regulárních výrazech lze zadat několika způsoby:

* výčtem – např. `[abc]`
* rozsahem – např. `[a-z]`
* kombinací – např. `[abcx-z1-3]`
* sekvencí zastupující celou skupinu znaků – těch je hned několik:
  * `\d` označuje číslice – ekvivalent pro `[0-9]` v ASCII a řadu dalších znaků z Unicode (např. `٩`)
  * `\D` označuje vše kromě číslic (množina obsahující všechny znaky kromě těch z předchozí skupiny)
  * `\s` označuje tzv. bílé znaky (whitespace) – mezeru, tabulátor atp.
  * `\S` označuje vše kromě bílých znaků (množina obsahující všechny znaky kromě těch z předchozí skupiny)
  * `\w` označuje všechny alfanumerické znaky a podtržítko – ekvivalent s `[a-zA-Z0-9_]` z ASCII a obrovskou spoustu dalších znaků z Unicode včetně českých znaků s diakritikou
  * `\W` označuje vše kromě znaků z `\w` (množina obsahující všechny znaky kromě těch z předchozí skupiny)

Pokud je v hranatých závorkách jako první znak `^`, musí na daném místě být znak, ale nesmí to být žádný z těch uvedených v závorkách.
Tedy například zápis `[^567]` odpovídá jakémukoli znaku kromě pětky, šestky a sedmičky.

Další možnost označení skupiny představují kulaté závorky. Obsah takto označené skupiny je
posléze možné získat samostatně jako výsledek vyhledávání přip. použít v kombinaci s kvantifikátory.
Skupiny jsou v rámci regulárního výrazu automaticky číslované od jedničky, takže je můžeme použít vícekrát
pomocí `\cislo` bez nutnosti se opakovat.

Například zápisu `(a{3}) \1` odpovídá jen jeden řetězec `aaa aaa`, protože `\1` je odkaz na skupinu se třemi výskyty znaku "a".

Snad posledním znakem často používaným v kombinaci se skupinou v kulatých závorkách je `|` (svislá čára). Ta
umožňuje od sebe oddělit několik regulárních výrazů nebo jejich částí, přičemž může platit kterýkoli z nich.

Například zápisu `aaa|bbb` odpovídají řetězce `aaa` nebo `bbb`. Složitějšímu zápisu `([ab]{2}|z)k` odpovídají buď
dva znaky z výčtu `[ab]` následované znakem `k` nebo znak `z` následovaný znakem `k` – celkový výčet je tedy následující
`aak`, `abk`, `bak`, `bbk` a `zk`.

## Použití regulárních výrazů

Pojďme teď teorii přetavit v praxi a ukázat si použití regulárních výrazů s modulem `re` ze standardní knihovny Pythonu.

### Validace

Validace je jedno ze základních použití regulárních výrazů. V podstatě se ptáme, jestli řetězec odpovídá zadanému předpisu.

V různých organizacích mají uživatelská jména často přesně danou strukturu. Na VŠB v Ostravě je uživatelské jméno studentů a zaměstnanců složeno
ze tří malých písmen bez diakritiky a dvou až čtyř číslic. Jeho validace by tedy mohla vypadat následovně:

```pycon
>>> import re
>>> re.match(r"[a-z]{3}[0-9]{2,4}", "cap096")
<re.Match object; span=(0, 6), match='cap096'>
>>> re.match(r"[a-z]{3}[0-9]{2,4}", "cape096")
>>> re.match(r"[a-z]{3}[0-9]{2,4}", "ca096")
>>> re.match(r"[a-z]{3}[0-9]{2,4}", "cap0096")
<re.Match object; span=(0, 7), match='cap0096'>
```

Na příkladu je vidět, že pokud `match` najde shodu, vrátí speciální objekt, v opačném případě vrátí `None`.
`Match` objekt má spoustu užitečných metod, které umožňují pracovat s nalezenou shodou. Mimo jiné můžeme s jeho pomocí získat
jednotlivé skupiny označené v regulárním výrazu kulatými závorkami metodou `groups`, metodami `start` a `end` získat indexy
prvního a posledního znaku shody či metodou `span` oba indexy najednou.

```pycon
>>> match = re.match(r"([a-z]{3})([0-9]{2,4})", "cap0096")
>>> match.span()
(0, 7)
>>> match.start()
0
>>> match.groups()
('cap', '0096')
```

Samotné kulaté závorky nám regulární výraz nijak nezmění, ale rozdělí jej do skupin pro snadnější zpracování.

Problém nastane, když bude porovnávaný řetězec delší než dovoluje náš regulární výraz:

```pycon
>>> re.match(r"[a-z]{3}[0-9]{2,4}", "cap00096")
<re.Match object; span=(0, 7), match='cap0009'>
```

V tomto případě je rovněž nalezena shoda, ale jen na prvních sedmi znacích. Vypořádat se s tímto problémem
nám pomůže kotva označující konec řádku `$` na konci regulárního výrazu. Tím označíme, že za poslední číslicí už nesmí řetězec pokračovat dále
a vyřešíme tím náš problém.

```pycon
>>> re.match(r"[a-z]{3}[0-9]{2,4}$", "cap00096")
>>>
```

> [note]
> Funkce `match` kontroluje shodu s regulárním výrazem od začátku řetězce, a proto nepotřebujeme
> zadat i `^` jako označení začátku.
> Funkce `search` naproti tomu hledá shodu kdekoli v řetězci a jak už její název napovídá,
> hodí se více pro vyhledávání než validaci.

### Vyhledávání

Vyhledávat můžeme buď první část řetězce, která odpovídá regulárnímu výrazu, pomocí `search`, všechny
odpovídající podřetězce pomocí `findall` nebo jednotlivé shody pomocí `finditer`.

```pycon
>>> re.search(r".y", "Naše staré hodiny bijí čtyři hodiny.")
<re.Match object; span=(15, 17), match='ny'>
>>> re.findall(r".y", "Naše staré hodiny bijí čtyři hodiny.")
['ny', 'ty', 'ny']
>>> list(re.finditer(r".y", "Naše staré hodiny bijí čtyři hodiny."))
[<re.Match object; span=(15, 17), match='ny'>, <re.Match object; span=(24, 26), match='ty'>, <re.Match object; span=(33, 35), match='ny'>]
```

Je třeba mít se na pozoru, pokud by se části našeho řetězce, které odpovídají regulárnímu výrazu, navzájem překrývaly.
U funkce `search` to není problém, protože ta vrací jen první nalezený výskyt. Funkce `findall` a `finditer` však
ignorují znaky, které už vyhodnotily jako součást předešlé shody při průchodu řetězcem zleva doprava.

```pycon
>>> re.findall(r".y", "yyy yyy")
['yy', ' y', 'yy']
```

### Nahrazování

Nahrazení funkcí `sub` funguje velmi podobně jako řetězcová metoda `replace`. Samozřejmě s tím rozdílem, že nahrazená část musí
odpovídat regulárnímu výrazu.

```pycon
>>> re.sub(r"\s+and\s+", " & ", "Bread  and butter and    ham  and  spam")
'Bread & butter & ham & spam'
>>> re.sub(r"\s+and\s+", " & ", "Bread  and butter and    ham  and  spam", count=1)
'Bread & butter and    ham  and  spam'
```

V příkladu výše nahrazujeme slovo `and` obklopené jedním nebo více bílými znaky z obou stran znakem `&` s jednou mezerou na každé straně. Parametrem `count` můžeme nastavit maximální počet nahrazení.

## Nastavení chování (flags)

Jedním ze základních způsobů, jak ovlivnit chování funkcí v modulu `re`, jsou tzv. flagy. Jedná se o různá nastavení,
která je možné v případě potřeby zapnout.

* `re.DEBUG` zobrazí ladící hlášky týkající se zpracování regulárního výrazu
* `re.IGNORECASE` (zkráceně `re.I`) způsobí, že regulární výraz nebude brát ohled na velikost písmen
* `re.MULTILINE` (zkráceně `re.M`) umožní zpracování víceřádkových řetězců. `^` a `$` začnou označovat začátek a konec každého řádku místo začátku a konce celého řetězce.
* `re.DOTALL` (zkráceně `re.S`) způsobí, že se tečka stane zástupným znakem i za nový řádek `\n`, což v základním nastavení neplatí.
* … a další k nalezení v dokumentaci.

Nejlepší je předávat flagy jako pojmenovaný argument výše zmíněným funkcím. Vyhneme se tím chybám vzniklým kvůli různým počtům pozičních argumentů funkcí v modulu `re`.
Pokud jich potřebujeme předat více najednou, nakombinujeme je pomocí operátoru `|`.

```pycon
>>> re.findall(r".y", "Naše staré hodiny bijí čtyři HODINY.")
['ny', 'ty']
>>> re.findall(r".y", "Naše staré hodiny bijí čtyři HODINY.", flags=re.IGNORECASE)
['ny', 'ty', 'NY']
>>> re.findall(r".y", "Naše staré hodiny bijí čtyři HODINY.", flags=re.IGNORECASE|re.DEBUG)
ANY None
LITERAL 121

0. INFO 4 0b0 2 2 (to 5)
5: ANY
6. LITERAL_UNI_IGNORE 0x79 ('y')
8. SUCCESS
['ny', 'ty', 'NY']
```

## Optimalizace

Jednou z možností optimalizace fungování regulárních výrazů je jejich kompilace. Nejdříve si regulární výraz zkompilujete a pak místo
funkcí z modulu `re` použijete stejnojmenné metody tohoto nového objektu.

```pycon
>>> regex = re.compile(r".y", flags=re.IGNORECASE)
>>> regex.findall("Naše staré hodiny bijí čtyři HODINY.")
['ny', 'ty', 'NY']
```

Kompilace se ale děje i automaticky na pozadí při používání funkcí, takže pokud používáte jen několik stále se opakujících
regulárních výrazů, není třeba si je předem ručně kompilovat.

## Příklad z úvodu lekce

Teď se můžeme společně podívat, jestli se nám podaří rozšifrovat kryptický zápis z úvodu této lekce. V ideálním případě bychom k tomu měli znát vše potřebné.

```
r"^\w+([\.-]?\w)+@\w+([\.]?\w)+(\.[a-zA-Z]{2,3})+$"
```

Vezmeme to postupně:
* `^` označení začátku hledaného řetězce
* `\w+` 1 nebo více alfanumerických znaků
* `([\.-]?\w)+` skupina alfanumerických znaků, která může začínat tečkou nebo spojovníkem a může se jednou nebo vícekrát opakovat
* `@` znak zavináče – nemá žádný speciální význam
* `\w+` 1 nebo více alfanumerických znaků
* `([\.]?\w)+` skupina alfanumerických znaků, která může začínat tečkou a může se jednou nebo vícekrát opakovat
* `(\.[a-zA-Z]{2,3})+` skupina obsahující dvě nebo tři malá/velká písmena anglické abecedy nebo číslice, která musí začínat tečkou a může se jednou nebo vícekrát opakovat
* `$` tady by měl validovaný řetězec skončit a neměl by obsahovat nic navíc

Pokud to ještě nedává smysl, jedná se o regulární výraz pro validaci emailových adres. Není ovšem bez chyby, protože nám nedovolí použít před zavináčem znak `+`, který je možnou validní součástí emailové adresy. Napsat regulární výraz pro dokonalou validaci emailové adresy není možné. Více info poskytne [emailregex.com](https://emailregex.com/).

Pokud by nás z emailu zajímala jen jedna jeho část (např. název schránky před zavináčem), můžeme si tu část označit
kulatými závorkami a pak ji ve výsledné shodě po úspěné validaci najít jako jednu ze skupin – v tomto případě jako úplně první.

```pycon
>>> m = re.match(r"(^\w+([\.-]?\w)+)@\w+([\.]?\w)+(\.[a-zA-Z]{2,3})+$", "example.user@example.com")
>>> m.groups()
('example.user', 'r', 'e', '.com')
```

## Online pomocníci

Pokud si budete s regulárními výrazy hrát, doporučuji aplikace jako [regex101.com](https://regex101.com/) nebo [regextester.com](https://www.regextester.com/), které umí nejen otestovat regulární výraz a obarvit řetězce, které mu vyhovují (včetně různých barev pro různé skupiny), ale také zvládnou celý regulární výraz rozložit na jednotlivé části a každou zvlášť vysvětlit.
