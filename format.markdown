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

* **`classes`** - array, která obsahuje jednotlivé třídy a seznam skupin, na které je daná třída rozdělená. Záznam vypadá nějak takto:

        {
          "name": "4.F",
          "groups": [array]
        }
        
    Pole `groups` pak bude obsahovat pole s jednotlivými kritériemy, podle kterých se pak skupina rozděluje.
    Celé pole `groups` může vypadat třeba takto:
    
        "groups": [
          {
            "name": "Jazyky",
            "options": [
              {
                "gid": 1,
                "name": "Skupina 1"
              },
              {
                "gid": 2,
                "name": "Skupina 2"
              }
            ]
          },
          {
            "name": "Odborné předměty",
            "options": [
              {
                "gid": 3,
                "name": "Skupina 1"
              },
              {
                "gid": 4,
                "name": "Skupina 2"
              }
            ]
          },
          {
            "name": "Cvičení z matematiky",
            "options": [
              {
                "gid": 5,
                "name": "Ano"
              },
              {
                "gid": 6,
                "name": "Ne"
              }
            ]
          }
        ]
        
    V tomto příkladě se třída dělí různě na jazyky a na ostatní předměty *(jeden žák může chodit do skupiny 1 na anglický jazyk a do skupiny 2 na ostatní předměty)* a někteří žáci chodí na Cvičení z matematiky.  
    Položka **`gid`** *(Group ID)* souží k jednoznačnému určení skupiny v rámci tohoto souboru a nikde se nezobrazuje. V rámci jedné třídy **musí** být hodnota `gid` unikátní a **nikdy** se nesmí rovnat nule (`0`). Není nutné položky číslovat od jedničky, **nikdy však nesmí dojít k přečíslování hodnot**, protože se jedná o jediný identifikátor skupin, které si aplikace ukládá *(tj. informace, že žák ze 4.E chodí do skupiny s `gid=4` nesmí za rok změnit svůj význam)*.  
    Pro ujasnění, v tomto příkladě si musí uživatel této aplikace vybrat tři skupiny, jednu z dvojice `[1, 2]`, druhou z `[3, 4]` a třetí z trojice `[5, 6]`. Proto musí být v tomto seznamu skupina s `gid=6`, i když se tato hodnota nikde nepoužívá, jinak by žák neměl způsob, jak zvolit, že nechodí na Cvičení z matematiky.
    
* **`rooms`** - seznam všech místností na škole
* **`teachers`** - seznam všech učitelů na škole
* **`times`** - informace o tom, kdy která hodina začíná a kdy končí. Každá hodina obsahuje tyto položky:

    * **`num`** - pořadové číslo hodiny
    * **`start`** - začátek hodiny ve formátu `hh:mm`
    * **`end`** - konec hodiny ve formátu `hh:mm`

* **`timetable`** - obsahuje záznamy o jednotlivých hodinách. Vzhledem k tomu, že se na tyto data lze dívat různě *(podle třídy, učitele, místnosti, ...)*, nejsou data nijak seřazena a představují jakousi databázi, kde jeden řádek odpovídá jedný vyučovací hodině. Nedochází tedy k duplicitě dat *(z rozvrhu všech tříd jde dopočítat rozvrhy všech učitelů)*. Jeden řádek obsahuje tyto záznamy *(sploupce)*:

    * **`teacher`** [int] - index do seznamu učitelů uvedený výše
    * **`subject`** [String] - zkratka předmětu, např. "ČJ"
    * **`day`** [int] - číslo dne v týdnu (1=pondělí, 5=pátek)
    * **`hour`** [int] - index do seznamu vyučovacích hodin uvedený výše
    * **`classId`** [int] - index do seznamu tříd uvedený výše
    * **`room`** [int] - index do seznamu místností uvedený výše
    * **`group`** [int] - pokud se tento předmět týká jen určitý skupiny, bude obsahovat `gid` této skupiny. V opačném případě bude nastaven na nulu (`0`), což znamená, že se třída na tento předmět na skupiny nědělí (má ho celá třída dohromady).
    * **`week`** [String] - zda se tento předmět vyučuje jen v sudé (`even`) nebo liché (`odd`) týdny. Pokud není předmět omezen na sudé nebo liché týdny, bude hodnota rovna `both`.

Položky souboru `supl.json`
---------------------------

Soubor je jeden velkej objekt s následujícími položkami:

* **`rozvrh_version`** - obsahuje číslo aktuální verze souboru `rozvrh.json`. Může jít o libovolný alfanumerický řetězec, který se změní vždy, když dojde ke změně souboru `rozvrh.json`. Může jít o datum jeho vytvoření, jeho hash, číslo, které se inkrementuje, nebo cokoliv jiného.
* **`current_week`** - zda 1. den, který je ve výpisu suplování uveden, je podle třídnice sudý (`even`) nebo lichý (`odd`).
* **`supl_first_day`** - unixový timestamp prvního dne, který je ve výpisu uveden
* **`changes`** - obsahuje záznamy o jednotlivých změnách ve stylu položky `timetable` v souboru `rozvrh.json`. Pokud dojde k odpadnutí předmětu, nastaví hodnota `subject` na prázdný řetězec (`""`), a hodnoty `teacher`, `room` a `group` na nulu (`0`).

Ukázka souborů
--------------

### rozvrh.json

    {
      "classes": [
        {
          "name": "4.D",
          "groups": [
            {
              "name": "Jazyky",
              "options": [
                {
                  "gid": 1,
                  "name": "Skupina 1"
                },
                {
                  "gid": 2,
                  "name": "Skupina 3"
                },
                {
                  "gid": 3,
                  "name": "Skupina 4"
                }
              ]
            },
            {
              "name": "Odborné předměty",
              "options": [
                {
                  "gid": 4,
                  "name": "Skupina 1"
                },
                {
                  "gid": 5,
                  "name": "Skupina 2"
                }
              ]
            },
            {
              "name": "Cvičení z matematiky",
              "options": [
                {
                  "gid": 6,
                  "name": "Ano"
                },
                {
                  "gid": 7,
                  "name": "Ne"
                }
              ]
            }
          ]
        },
        {
          "name": "4.E",
          "groups": [
            {
              "name": "Jazyky",
              "options": [
                {
                  "gid": 1,
                  "name": "Skupina 1"
                },
                {
                  "gid": 2,
                  "name": "Skupina 2"
                }
              ]
            },
            {
              "name": "Odborné předměty",
              "options": [
                {
                  "gid": 3,
                  "name": "Skupina 1"
                },
                {
                  "gid": 4,
                  "name": "Skupina 2"
                }
              ]
            },
            {
              "name": "Cvičení z matematiky",
              "options": [
                {
                  "gid": 5,
                  "name": "Ano"
                },
                {
                  "gid": 6,
                  "name": "Ne"
                }
              ]
            }
          ]
        },
        {
          "name": "4.F",
          "groups": [
            {
              "name": "Cvičení z matematiky",
              "options": [
                {
                  "gid": 1,
                  "name": "Ano"
                },
                {
                  "gid": 2,
                  "name": "Ne"
                }
              ]
            }
          ]
        }
      ],
      "rooms": [
        "B130",
        "C113",
        "D055",
        "B305",
        "TV"
      ],
      "teachers": [
        "Albrechtová Marie",
        "Ćernoch Milan",
        "Venzara Robert",
        "Forejtová Jaroslava",
        "Jelínek Radek",
        "Petera Martin",
        "Koucký Miroslav",
        "Přívratský Zdeněk",
        "Machačová Kateřina",
        "Kutálek Michal"
      ],
      "times": [
        {
          "num": 1,
          "start": "8:00",
          "end": "8:45"
        },
        {
          "num": 2,
          "start": "8:50",
          "end": "9:35"
        },
        {
          "num": 3,
          "start": "9:45",
          "end": "10:30"
        },
        {
          "num": 4,
          "start": "10:50",
          "end": "11:35"
        },
        {
          "num": 5,
          "start": "11:45",
          "end": "12:30"
        },
        {
          "num": 6,
          "start": "12:35",
          "end": "13:20"
        },
        {
          "num": 7,
          "start": "13:25",
          "end": "14:10"
        },
        {
          "num": 8,
          "start": "14:15",
          "end": "15:00"
        },
        {
          "num": 9,
          "start": "15:05",
          "end": "15:50"
        },
        {
          "num": 10,
          "start": "15:55",
          "end": "16:40"
        }
      ],
      "timetable": [
        {
          "teacher": 5,
          "subject": "MS",
          "day": 1,
          "hour": 0,
          "classId": 2,
          "room": 2,
          "group": 0,
          "week": "both"
        },
        {
          "teacher": 5,
          "subject": "MS",
          "day": 1,
          "hour": 1,
          "classId": 2,
          "room": 2,
          "group": 0,
          "week": "both"
        },
        {
          "teacher": 5,
          "subject": "MS",
          "day": 1,
          "hour": 2,
          "classId": 2,
          "room": 2,
          "group": 0,
          "week": "both"
        },
        {
          "teacher": 8,
          "subject": "SV",
          "day": 1,
          "hour": 3,
          "classId": 2,
          "room": 3,
          "group": 0,
          "week": "both"
        }
      ]
    }
    
### supl.json

    {
      "rozvrh_version": "123abc",
      "current_week": "even",
      "supl_first_day": 1421625600,
      "changes": [
        [
          {
            "hour": 3,
            "classId": 2,
            "subject": "",
            "teacher": 0,
            "room": 0,
            "group": 0
          }
        ],
        [
          {
            "hour": 0,
            "classId": 2,
            "subject": "PS",
            "teacher": 7,
            "room": 1,
            "group": 0
          },
          {
            "hour": 1,
            "classId": 2,
            "subject": "PS",
            "teacher": 7,
            "room": 1,
            "group": 0
          }
        ]
      ]
    }

Zpracování dat
--------------

Jeden ze způsobů jak zpracovat výše uvedená data, je uložit je do databáze. To umožní zobrazení těchto dat z různých pohledů *(třídy, učitele, místnosti,...)* bez nutnosti velkých změn v kódu *(stačí upravit část SQL dotazu za `WHERE`)*. Pokud se pro tuto variantu zpracování dat rozhodnete, bude struktura databáze vypadat následovně:

![Struktura dat][1]

Zkušební data
-------------

Protože ostrá data zatím nejsou k dispozici a času není nazbyt, přikládám odkazy, odkud je možné stáhnout falešná data pro testování aplikace. Tyto soubory jsou konstantí a obsahují totožná data jako výše uvedený přiklad.

    https://dl.dropboxusercontent.com/u/39997832/matpr/rozvrh.json
    https://dl.dropboxusercontent.com/u/39997832/matpr/rozvrh.json.gz
    https://dl.dropboxusercontent.com/u/39997832/matpr/supl.json
    https://dl.dropboxusercontent.com/u/39997832/matpr/supl.json.gz

[1]: data/db.png
