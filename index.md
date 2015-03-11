---
title: Úvodní stránka
---

Tato aplikace má za úkol zobrazit studentům [Střední průmyslové školy elektrotechnické v Pardubicích][1] jejich rozvrh, změny ve výuce a získané známky na chytrém mobilním telefonu s operačním systémem Android, iOS nebo Windows Phone. Tato aplikace je v současné době ve vývoji a bude mít za úkol posloužit jako maturitní projekt jejich tvůrců.

Popis aplikace
===============

Při prvním spuštění aplikace se žák přihlásí pomocí svého školního účtu na intranet. Aplikace rozpozná, do jaké chodí třídy a stáhne jeho rozvrh včetně jeho aktuálních změn, jeho známky a z těchto známek vypočítá průměr dle pravidel klasifikace. Po jejich stažení si aplikace tyto informace uloží do mezipaměti a umožní jejich pozdější zobrazení i bez internetového připojení. Pokud se nepřihlásí, aplikace načte rozvrhy všech tříd, učitelů a učeben a dá uživateli na výběr mezi všemi těmito rozvrhy. Aplikace stahuje rozvrhy a suplování ve formátu JSON a známky v serializovaném poli PHP.

Řešitelé museli odposlechnout komunikaci školního intranetu, běžícího na technologii Flash, se serverem; zjistit, že se jedná o serializované pole vytvořené v jazyce PHP a následně zparsovat přijatá data (knihovnou nebo vlastním parserem - dle platformy). Z těchto dat řešitelé získávají známky a počítají z nich vážený průměr a zobrazí je (řešení dle platformy).

Data rozvrhů a suplování získávají řešitelé od školy ve formátu určeným řešiteli (JSON, specifikace https://github.com/atepr/suplovanispse/blob/gh-pages/format.markdown). Tato data aplikace zpracuje a následně zobrazí (řešení dle platformy).


Funkce aplikace
===============

* Přizpůsobení rozvržení pro malý display telefonu.
* Uložení dat off-line pro jejich pozdější zobrazení i bez internetového připojení (první spuštění aplikace musí proběhnout on-line).
* Upozornění na změny v rozvrhu nebo na nové známky pomocí notifikací operačního systému telefonu.

Rozvrh & suplování
------------------

* Zobrazení rozvrhu pro libovolnou třídu, učitele nebo místnost (výchozí třídu lze do aplikace uložit).
* Přehledné vyznačení změn ve výuce v týdenním rozvrhu.
* Různé zobrazení rozvrhu na šířku i na výšku.

Intranet
--------

* Zobrazení známek ze školního intranetu, včetně detailů o jednotlivých známkách, jako jsou:
    * Datum získání známky
    * Váha známky
    * Procentuální minimum požadovaných známek v této kategorii
    * Kategorie
    * Látka
    * Poznámka
* Výpočet průměru z jednotlivých předmětů.
* Možnost zapamatování přihlašovacích údajů na intranet.
* Přihlašovací údaje na intranet zároveň slouží k určení třídy, kterou žák navštěvuje.

Minimální požadavky
===================

Aplikace poběží na mobilní telefonu s jedním z níže uvedených operačních systémů:

* Android verze 4.0 nebo vyšší
* iOS
* Windows Phone 8.0, 8.1

Závěr
=====

Předpokládáme, že tato aplikace zlepší studentům přístupnost k jejich klasifikaci, která byla, díky technologii Flash, používané na školním intranetu, nezobrazitelná na velké části mobilních telefonů a že aplikace usnadní žákům sledování změn ve výuce.

Autoři
======

* Petr Rusiňák (Android)
* Kryštof Mrózek (iOS)
* Richard Bureš (Windows Phone)

Dokumenty
=========

* [Návrh datového formátu pro suplování a rozvrh](format)
* [Vysvětlení pravidel na intranetu](intranet-pravidla)

[1]: http://www.spse.cz/
