---
title: Vysvětlení pravidel na intranetu
---

Vysvětlení výpoču průměru s pravidly
====================================

Intranet nabízí pravidla typu *pokud máte pět jedniček, zlepší se vám známka o stupeň*. Tento dokument vám ukáže, jak vypočítat cekový průměr s využitím těchto pravidel. Předpokládá se, že víte, jak vypočítat vážený průměr.

Úvod
----

Informace o pravidlech jsou na intranetu uloženy v tomto formátu:

    [p=2a3][1=4a5][1=2u1]
    
kde každá závorka představuje jedno pravidlo. Pravidla se vykonávají tak, jak jsou za sebou, ale pravidla jejichž **4. znak je `a`, mají přednost!** Nevím, zda server pravidla vždy seřadí tak, aby tato podmínka platila, ale pokud ne, tak je nutné tuto situaci vyřešit.

Způsob výpočtu průměru
----------------------

Výpočet celkového průměru probíhá následovně:

1. Vypočítá se vážený průměr *(pro každou skupinu zvlášť)*
2. Aplikují se pravidla *(pro každou skupinu zvlášť)*
3. Vypočítá se vážený průměr z vážených průměrů vypočtených v kroku 1 včetně průměrů, které byly přidány pravidly.

Aplikace pravidel
-----------------

Každé pravidlo se skládá z pěti znaků, např. `p=2a3`, `1=4a5` nebo `1=2u1`. Každý znak má svůj vlastní význam. První 3 znaky představují podmínku, která musí nastat, aby došlo k aplikaci pravidla a zbylé 2 znaky určují, co se v případě splnění podmínky stane.

Význam jednolivých znaků je následující:

* **1. znak** určuje, co se bude testovat. Tento znak může nabývat hodnot:

    Hodnota | Význam
    --------|----------------------------------------------------
    `p`     | Průměr (bez pravidel) po zaokrouhlení na celá čísla
    `1`     | Počet jedniček
    `2`     | Počet dvojek
    `3`     | Počet trojek
    `4`     | Počet čtyřek
    `5`     | Počet pětek
    
* **2. znak** je vždy rovná se (`=`). Můžeme ho ignorovat.
* **3. znak** představuje drouhou stranu podmínky. Například `p=2` znamená *pokud průměr po zaokrouhlení na celá čísla je 2* a `1=4` znamená *pokud počet jedniček je 4*. Tento znak nabývá jedné z hodnot `1`, `2`, `3`, `4` a `5`.
* **4. znak** určuje, co se stane podle této tabulky:
    
    Hodnota | Význam
    --------|----------------------------------------------------
    `a`     | se přidá známka ... *(může i v rámci jednoho pravidla vícekrát)*
    `d`     | se sniží výsledná známka o ... *(směrem k horší známce)*
    `u`     | se zvyší výsledná známka o ... *(směrem k lepší známce)*

* **5. znak** Představuje tečky v tabulce výše. Tento znak nabývá jedné z hodnot `1`, `2`, `3`, `4` a `5`.

Vysvětlení *se přidá známka* a *se sníží/zvýší výsledná známka o*
-----------------------------------------------------------------

* ***Se přidá známka*** funguje tak, že se vytvoří nová virtuální skupina s váhou, jakou měla původní skupina a v ní bude jediná známka pode toho, jaká znáka se měla přidat.
* ***Se sníží/zvýší výsledná známka o*** funguje tak, jak by se dalo čekat. Nemá to však vliv na průměr skupiny, ale na až výslednou známku z předmětu. Také je zde logické omezení, že nejlíp můžete dostat jedničku a nejhůře pětku.

Zní to složitě, ale následující příklad to snad objasní.

Reprezentativní příklad
-----------------------

Máme dvě skupiny, `A` a `B`.

Skupina `A` má váhu `1` a tyto pravidla:

* Pokud je počet pětek roven 4, přidá se známka 5 (`5=4a5`).
* Pokud je počet jedniček roven 2, přidá se známka 1 (`1=2a1`).
* Pokud je průměr roven 3, zvýší se výsledná známka o 1 (`p=3u1`).

Skupina `B` má váhu `2` a tyto pravidla:

* Pokud je počet trojek roven 3, přidá se známka 3 (`3=3a3`).
* Pokud je počet čtyřek roven 2, přidá se známka 4 (`4=2a4`).
* Pokud je počet pětek roven 1, sníží se výsledná známka o 2 (`5=1d2`).
* Pokud je průměr roven 4, přidá se známka 4 (`p=4a4`).

Jednotlivé známky ve skupině mají stejnou váhu. Představme si, že máme následující známky:

(Pořadí)    | Skupina A | Skupina B
------------|-----------|------------
(1)         | 1         | 2
(2)         | 1         | 3
(3)         | 1         | 4
(4)         | 1         | 4
(5)         | 1         | 4
(6)         | 3         | 5
(7)         | 3         | 5
(8)         | 5         | 5
(9)         | 5         | 5
(10)        | 5         | 5
**Průměr:** | **2,60**  | **4,20**

Postup výpočtu průměru je náledující:

1. Vypočítá se vážený průměr pro každou skupinu. Nyní máme tyto průměry:

    Váha  | Hodnota
    ------|---------
    1     | 2.6
    2     | 4.2

2. Zkontroluje se pravidlo *pokud je počet pětek roven 4*. To je splněno, takže se do naší tabulky *přidá známka 5*:

    Váha  | Hodnota
    ------|---------
    1     | 2.6
    2     | 4.2
    **1** | **5**

3. Zkontroluje se pravidlo *pokud je počet jedniček roven 2*. To je splněno hned dvakrát, takže se do naší tabulky **dvakrát** *přidá známka 1*:
    
    Váha  | Hodnota
    ------|---------
    1     | 2.6
    2     | 4.2
    1     | 5
    **1** | **1**
    **1** | **1**
    
4. Nyní je na řadě pravidlo *pokud je průměr roven 3*. Na všechny řádky, které jsme přidali v bodech 2 a 3, se kašle a vezme se první průměr, co jsme vypočítali, tj. `2.60`. Ten zaokrouhlíme na celá čísla (to je `3`) a pravidlo je splněno. Budeme si pamatovat, že časem budeme výslednou známku **zvyšovat o 1**. Zatím ale neděláme nic.

5. A dostáváme se k prvnímu pravidlu u skupiny B. To splněno není, takže ani nebudeme nic dělat.
6. Pravidlo *pokud je počet čtyřek roven 2* už zase splněno je *(jednou)*, takže do naší tabulky *přidáme čtyřku*. Jsme ale ve skupině `B`, která má váhu `2`, takže přidaná známka bude mít v naší tabulce taky váhu `2`:
    
    Váha  | Hodnota
    ------|---------
    1     | 2.6
    2     | 4.2
    1     | 5
    1     | 1
    1     | 1
    **2** | **4**

7. Dále je na řadě pravidlo *pokud je počet pětek roven 1, sníží se výsledná známka o 2*. To je sice splněno pětkrát, ale opakování se týká jen pravidel, která *přidávají* známku (4. znak je `a`). Takže si zapamatujeme, že časem budeme výslednou známku **snižovat o 2**, ale jenom jednou.
8. Pravidlo *pokud je průměr roven 4* je rovněž splněno *(jenou, víckrát to nejde)*, takže se do naší tabulky *přidá se známka 4*:

    Váha  | Hodnota
    ------|---------
    1     | 2.6
    2     | 4.2
    1     | 5
    1     | 1
    1     | 1
    2     | 4
    **2** | **4**

9. Nyní již klasickým váženým průměrem vypočítáme, že výsledá známka je:

    > _(2.6 + 2 * 4.2 + 5 + 1 + 1 + 2 * 4 + 2 * 4) ÷ (1 + 2 + 1 + 1 + 1 + 2 + 2) = **3,40**_

10. To není vše, protože si z bodů 4 a 7 pamatuje, že výslednou známku budeme:

    1. zvyšovat o 1
    2. snižovat o 2
    
    To znamená, že ve výsledku známku **snížíme o 1** a definitivní průměr nám vyjde **4,40**, takže ještě nepropadáme.
    
Jak prosté, ne?
