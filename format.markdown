---
title: Datový formát pro suplování a rozvrh
---

Formát pro suplování a rozvrh
=============================

Tento dokument má ukázat, jak si představuju, že bude fungovat formát, ve kterém bude mobilní aplikace dostávat aktuální data rozvrhu a suplování.

Soubory
-------

Aplikace bude získávat data ve dvou souborech *(na které se bude přistupovat dvěma HTTP požadavky)*. Na jejich názvu nezáleží, ale v tomto dokumentu jim budeme říkat `rozvrh.json` a `supl.json`.
Oba soubory budou vždy obsahovat data pro celou školu, **nebude** se tedy používat nic jako `?trida=4.F`.

* **`rozvrh.json`:** obsahuje seznam jednotlivých učitelů, tříd, místností atd. a především data s rozvrhy. Tento soubor se bude měnit jen když dojde k nějaké změně v rozvrhu (jednou za půl roku); může být na straně serveru cache-ovaný. Soubor se stahuje jenom, když dojde k jeho změně.
* **`supl.json`:** obsahuje aktuální změny rozvrhu (suplování); mění se každou chvíli a **nesmí** být cache-ovaný *(někteří lidé jsou schopní o velké přestávce každou minutu mačkat refresh a aplikace, která jim ukáže hodinu stará data, je pro ně k ničemu; pokud ale budou na data čekat o pár milisekund déle, nikdo to nepozná)*. Také obsahuje informaci o tom, jestli došlo ke změně souboru `supl.json`.

Aby se ušetřil přenos dat, budou tyto soubory k dispozici i v zagzipované verzi (každý vzlášť), takže budou celkem k dispozici 4 soubory: `rozvrh.json`, `rozvrh.json.gz`, `supl.json` a `supl.json.gz`.

Poznámka k `null`
-----------------

Některé programovací jazyky neumožňují nastavit hodnotu `null` do všech typů proměnných *(např. v Javě nejde napsat `int i = null;`)* a některé parsery JSONu mají problém zpracovat data, co obsahují hodnotu `null`.
Aby jsme se vyhnuli nepříjemnostem, **nebudeme** hodnotu `null` vůbec používat a pokud to bude nutné, určíme jinou hodnotu se speciálním významem (např. `0` nebo *prázdný řetězec*).

Položky souboru `rozvrh.json`
-----------------------------

Soubor je jeden velkej objekt s následujícími položkami:

* **`times`** - informace o tom, kdy která hodina začíná a kdy končí. Každá hodina obsahuje tyto položky:

    * **`num`** - pořadové číslo hodiny
    * **`start`** - začátek hodiny ve formátu `hh:mm`
    * **`end`** - konec hodiny ve formátu `hh:mm`

* **`timetable`** - obsahuje záznamy o jednotlivých hodinách. Vzhledem k tomu, že se na tyto data lze dívat různě *(podle třídy, učitele, místnosti, ...)*, nejsou data nijak seřazena a představují jakousi databázi, kde jeden řádek odpovídá jedný vyučovací hodině. Nedochází tedy k duplicitě dat *(z rozvrhu všech tříd jde dopočítat rozvrhy všech učitelů)*. Jeden řádek obsahuje tyto záznamy *(sploupce)*:

    * **`teacher`** [String] - jméno učitele
    * **`subject`** [String] - zkratka předmětu, např. `ČJ`
    * **`day`** [int] - číslo dne v týdnu (1=pondělí, 5=pátek)
    * **`hour`** [int] - index do seznamu vyučovacích hodin uvedený výše
    * **`class_name`** [String] - jméno třídy, např. `4.F`. Mezi tečkou a písmenem není žádná mezera.
    * **`room`** [String] - jméno místnosti, např. `B130`
    * **`group`** [String] - pokud je tento předmět rozdělen na skupiny, bude zde identifikátor skupiny, které se předmět týká. V opačném případě bude nastaven na *prázdný řetězec* (`""`), což znamená, že se třída na tento předmět na skupiny nědělí (má ho celá třída dohromady). Identifikátory skupin nemusí být unikátní.
    * **`week`** [String] - zda se tento předmět vyučuje jen v sudé (`even`) nebo liché (`odd`) týdny. Pokud není předmět omezen na sudé nebo liché týdny, bude hodnota rovna `both`.

Položky souboru `supl.json`
---------------------------

Soubor je jeden velkej objekt s následujícími položkami:

* **`timetable_version`** - obsahuje číslo aktuální verze souboru `rozvrh.json`. Může jít o libovolný alfanumerický řetězec, který se změní vždy, když dojde ke změně souboru `rozvrh.json`. Může jít o datum jeho vytvoření, jeho hash, číslo, které se inkrementuje, nebo cokoliv jiného.
* **`changes`** - objekt, který obsahuje změny v rovrhu pro jednotlivé dny. Formát klíče je `1234567890W`, kde `1234567890` je unixový timestamp dne, kterého se změny týkají a znak `W` označuje, zda se jedná o sudý (`E`) nebo lichý (`O`) týden. Hodnoty objektu jsou podobné jako v položce `timetable` v souboru `rozvrh.json`. Pokud dojde k odpadnutí předmětu, nastaví hodnoty `subject`, `teacher`, `room` na prázdný řetězec (`""`). Pokud dojde k zveřejnění rozvrhu a celá škola v daný den nemá žádnou změnu, bude hodnotou objektu pro tento den prázdné pole (`[]`).

Ukázka souborů
--------------

### rozvrh.json

```json
{
  "times": [
    {
      "num": "0",
      "start": "7:10",
      "end": "7:55"
    },
    {
      "num": "1",
      "start": "8:00",
      "end": "8:45"
    },
    {
      "num": "2",
      "start": "8:50",
      "end": "9:35"
    },
    {
      "num": "3",
      "start": "9:45",
      "end": "10:30"
    },
    {
      "num": "4",
      "start": "10:50",
      "end": "11:35"
    },
    {
      "num": "5",
      "start": "11:45",
      "end": "12:30"
    },
    {
      "num": "6",
      "start": "12:35",
      "end": "13:20"
    },
    {
      "num": "7",
      "start": "13:25",
      "end": "14:10"
    },
    {
      "num": "8",
      "start": "14:15",
      "end": "15:00"
    },
    {
      "num": "9",
      "start": "15:05",
      "end": "15:50"
    },
    {
      "num": "10",
      "start": "15:55",
      "end": "16:40"
    }
  ],
  "timetable": [
    {
      "teacher": "Martin Petera",
      "subject": "MS",
      "day": 1,
      "hour": 1,
      "class_name": "4.F",
      "room": "D055",
      "group": "",
      "week": "both"
    },
    {
      "teacher": "Martin Petera",
      "subject": "MS",
      "day": 1,
      "hour": 2,
      "class_name": "4.F",
      "room": "D055",
      "group": "",
      "week": "both"
    },
    {
      "teacher": "Martin Petera",
      "subject": "MS",
      "day": 1,
      "hour": 3,
      "class_name": "4.F",
      "room": "D055",
      "group": "",
      "week": "both"
    },
    {
      "teacher": "Sobolová Zdeňka",
      "subject": "SV",
      "day": 1,
      "hour": 4,
      "class_name": "4.F",
      "room": "B305",
      "group": "",
      "week": "both"
    }
  ]
}
```
    
### supl.json

```json
{
  "timetable_version": "123abc",
  "changes": {
    "1425340800E": [
      {
        "hour": 1,
        "class_name": "4.F",
        "subject": "PS",
        "teacher": "Přívratský Zdeněk",
        "room": "C113",
        "group": ""
      }
    ],
    "1425427200E": [
      {
        "hour": 1,
        "class_name": "4.F",
        "subject": "",
        "teacher": "",
        "room": "",
        "group": ""
      },
      {
        "hour": 2,
        "class_name": "4.F",
        "subject": "AJ",
        "teacher": "Albrechtová Marie",
        "room": "B130",
        "group": ""
      }
    ],
    "1425513600E": []
  }
}
```

Zkušební data
-------------

Protože ostrá data zatím nejsou k dispozici a času není nazbyt, přikládám odkazy, odkud je možné stáhnout falešná data pro testování aplikace. Tyto soubory jsou konstantí a obsahují podobná data jako výše uvedený přiklad.

    https://dl.dropboxusercontent.com/u/39997832/matpr/rozvrh.v2.json
    https://dl.dropboxusercontent.com/u/39997832/matpr/rozvrh.v2.json.gz
    https://dl.dropboxusercontent.com/u/39997832/matpr/supl.v2.json
    https://dl.dropboxusercontent.com/u/39997832/matpr/supl.v2.json.gz

[1]: data/db.png
