# Nauč se Python

Otevřené materiály pro výuku Pythonu – jak na organizovaných kurzech,
tak pro samouky.

Dostupné na [naucse.python.cz](https://naucse.python.cz).

## Návod pro přispěvatele do kurzu datové analýzy

### Základní pravidla

* Materiály píšeme česky ve formě Jupyter notebooků
* Čtenáři tykáme (`… vyzkoušej si to!`) případně mluvíme
ke skupině (`… můžeme použít několik metod.`).
* Jedna lekce by se měla dát odučit do dvou hodin celkového času
* Snažíme se, aby na sebe jednotlivé lekce příliš nenavazovaly, protože
nikdy není předem známo, jak bude ten či onen výsledný kurz nakonec vypadat. Samozřejmostí je, že u pokročilejších lekcí počítáme se
základními znalostmi, případně můžeme na některé materiály odkázat.


### Obsah lekcí a návrhy na nová témata

* Stávající osnova kurzu je [zde](https://docs.google.com/document/d/1ILN-opBfgAyLvxno4_Kdcgbhy4JHatwI2Aj4BmPfHeQ/edit),
* Je však možné tvořit i jakékoli materiály navíc, které se pak dají použít na workshopech či bonusových hodinách pro nadšené zájemce. 
* Autoři jednotlivých lekcí i zájemci o nerozebraná a nová témata založí Issue pojmenované "Osnova - 'Téma'" a přiloží odkaz na Google dokument s přibližnou osnovou (s právy přidávat komentáře)
* Odkazy na zajímavé materiály, které můžeme použít pro tvorbu kurzu nebo jen inspiraci, vkládáme do [souvisejícího Issue](https://github.com/PyDataCZ/naucse.python.cz/issues/4)

### Struktura

Ve složce `lessons/pydata` bude ve výsledku existovat složka pro každou lekci.
Tato složka bude obsahovat soubor `index.ipynb` coby hlavní soubor lekce
a soubor `info.yml` s následujícím obsahem:

```
title: <Název lekce>
style: ipynb
attribution: Pro PyData napsal(a) <jméno a příjmení>, 2019
license: cc-by-sa-40
```

Pokud bude lekce potřebovat nějaké statické soubory, vloží se do složky
`pydata/<lekce>/static`.

Aby byla lekce vidět v seznamu materiálů pro kurz datové analýzy, je
třeba ji ještě přidat do souboru `courses/pydata/info.yml`.

### Datové sady

Datové sady pro jednotlivé lekce volíme tak, aby se na nich dala dobře
prezentovat náplň dané lekce. Pokud je to záměrem (především pro lekce EDA),
mohou datové sady obsahovat chyby či být jinak nedokonalé.

Pokud je datová sada stažená z internetu, je důležité zkontrolovat její licenci
a do materiálů doplnit odkaz na zdroj (příp. další věci vyžadované licencí).

Pokud je datová sada vytvořená nějakým generátorem či skriptem, je dobré k ní
dodat i zdrojový skript (nejlépe ve formě notebooku s vysvětlením
procesu generování).

## Instalace a spuštění

Chceš-li server spustit na svém počítači, např. proto, že se chceš zapojit
do vývoje, je potřeba ho nejdřív nainstalovat:

* (nepovinné) Vytvoř a aktivuj si [virtuální prostředí](https://naucse.python.cz/lessons/beginners/install/) v Pythonu 3.6.
* Přepni se do adresáře s kódem projektu.
* Nainstaluj závislosti:

  * Linux/Mac:

    ```console
    $ python3 -m pip install pipenv
    $ pipenv install
    ```

  * Windows:

    ```doscon
    > py -3 -m pip install pipenv
    > pipenv install
    ```

Nainstalovanou aplikaci spustíš následovně:

* (nepovinné) Aktivuj si virtuální prostředí, máš-li ho vytvořené.
* Spusť vývojový server:
  ```console
  $ pipenv run serve
  ```
* Program vypíše adresu (např. `http://0.0.0.0:8003/`); tu navštiv v prohlížeči.

Pokud chceš místo vývojového spuštění vygenerovat statické HTML soubory (např. pro nahrání na statický hosting):

* Spusť freeze. Parametr `--serve` provede spuštění webserveru, pomocí kterého si lze vygenerované soubory prohlédnout:
  ```console
  $ PYTHONPATH=. pipenv run freeze --serve
  ```
* HTML stránky jsou v adresáři `naucse/_build`.
  Program vypíše adresu (např. `http://0.0.0.0:8000/`); tu navštiv v prohlížeči.

## Externí kurzy

Na naucse.python.cz jsou k dispozici i *externí* kurzy, které spravují více
či méně důvěryhodní lidé.
Proces vykreslování obsahu těchto kurzů jim dává velkou volnost: můžou převzít
plnou kontrolu nad počítačem, na kterém `naucse` běží.
Kvůli bezpečnosti je proto `naucse` ve výchozím nastavení neukazuje.


## Licence

Kód je k dispozici pod licencí MIT, viz soubor [LICENSE.MIT].

Obsah kurzů má vlastní licenci, která je uvedena v metadatech.
Používáme pouze [licence pro otevřený obsah][free content licenses].
Všechen obsah musí mít uvedenou licenci.

---

The code is licensed under the terms of the MIT license, see [LICENSE.MIT] file
for full text. By contributing code to this repository, you agree to have it
licensed under the same license.

Content has its own license specified in the appropriate matadata.
Only [free content licenses] are used. By contributing to an already licensed
document, you agree to have it licensed under the same license.
(And feel free to add yourself to the authors list in its metadata.)
When contributing new document(s) a license must be specified in the metadata.

[LICENSE.MIT]: https://github.com/pyvec/naucse.python.cz/blob/master/LICENSE.MIT
[free content licenses]: https://en.wikipedia.org/wiki/List_of_free_content_licenses
