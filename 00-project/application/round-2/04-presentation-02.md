---
project_outline: "[[00-project/01-project-outline]]"
---

> Ez az 1. félév második kötelező prezentációja. Bemutatja a specifikációt, a kezdeti technikai javaslatot és a 2. félév megvalósítási tervét. A kurzus szabványos Presented Markdown (PRMD) formátumában kell elkészíteni.

<!-- @slide Okos parkolóház-kezelő rendszer - Specifikáció és Terv -->
<!-- 5xl -->
# OKOS PARKOLÓHÁZ-KEZELŐ RENDSZER

<!-- @gap 3 -->

<!-- @set-align center -->
**Rendszerspecifikáció és megvalósítási terv**

Bata Marcell • WNJFN3 • 2026-LA-04

---

<!-- 2xl -->
[prmd.atom-forge.eu/docs](https://prmd.atom-forge.eu/docs)

---

<!-- @slide A specifikáció összefoglalása -->

# A specifikáció összefoglalása

<!-- @set-align center -->

Adatbázis-központú üzleti logika és biztonságos tranzakciókezelés.

- **Cél:** Relációs adatbázis (min. 12 tábla, 3NF) és ráépülő vékony webes felület.
- **Szereplők:** Ügyfél (keresés, foglalás, lemondás), Üzemeltető (statisztikák, áttekintés).
- **Kritikus pont:** Kettős foglalások megakadályozása konkurens (egyidejű) környezetben.
- **Megközelítés:** Minden üzleti szabály (díjszámítás, ütközésvizsgálat) szigorúan adatbázis szinten (tárolt eljárások, triggerek) valósul meg.

---

<!-- @slide Fő felhasználói utak és követelmények -->

# Fő felhasználói utak és követelmények

<!-- @set-align center -->

Az adatbázis megbízhatósága áll a felhasználói élmény hátterében.

<!-- @columns 2 5 -->

## Ügyfél útja
1. Időintervallum megadása.
2. Szabad helyek lekérdezése.
3. Foglalás indítása webes felületen.
4. **Ütközésvizsgálat** (tranzakció a háttérben).
5. Visszajelzés (Siker vagy Rollback).

<!-- @col-break -->

## Fő követelmények
- **Kötelező:** Szabad hely keresés, tranzakciós foglalás, saját foglalások kezelése.
- **Kötelező:** Üzemeltetői statisztikák és többtáblás aggregációk.
- **Kötelező:** 100 000+ parkolási tesztesemény generálása.
- **Mérhető:** Összetett lekérdezések futási idejének optimalizálása.

---

<!-- @slide Kezdeti technikai javaslat -->

# Kezdeti technikai javaslat

<!-- @set-align center -->

Robusztus adatbázis-kezelőre épülő, többrétegű architektúra.

- **Relációs adatbázis (RDBMS):** Oracle Database (PL/SQL) VAGY Microsoft SQL Server (T-SQL).
- **Adatbázis-logika:** Tárolt eljárások a foglaláshoz és díjszámításhoz, triggerek az állapotkövetéshez, megszorítások az integritáshoz.
- **Backend API (Szolgáltatásréteg):** Vékony közvetítőréteg (pl. Node.js, Python vagy Java), amely célzottan meghívja az adatbázis-eljárásokat.
- **Frontend:** Egyszerű, funkcionális webes UI a folyamatok demonstrálására, a fókusz elvesztése nélkül.

---

<!-- @slide A 2. félév megvalósítási terve -->

# A 2. félév megvalósítási terve

<!-- @set-align center -->

Iteratív fejlesztés az adatmodelltől a teljesítménymérésig.

1. **Adatbázis tervezés (M1):** 3NF E-R modell véglegesítése, táblák és kapcsolatok létrehozása.
2. **Üzleti logika (M2):** Tárolt eljárások, triggerek és funkciók implementálása.
3. **Tranzakciók és adatok (M3):** Ütközésvizsgálat tesztelése, 100 ezer tesztesemény generálása.
4. **Alkalmazásréteg (M4):** Backend végpontok és a demonstrációs webes felület kialakítása.
5. **Optimalizálás (M5):** Statisztikai lekérdezések végrehajtási tervének (EXPLAIN PLAN / Actual Execution Plan) mérése és indexelés.

---

<!-- @slide Nyitott kockázatok és következő lépések -->

# Nyitott kockázatok és következő lépések

<!-- @set-align center -->

A sikeres prototípushoz szükséges korai döntések.

<!-- @columns 2 5 -->

## Kockázatok
- **Konkurencia:** Deadlockok (holtpontok) elkerülése a nagy terhelésű ütközésvizsgálatnál.
- **Teljesítmény:** Az összetett üzemeltetői aggregációk lassulása.
- **Fókuszvesztés:** A webes felület (UI) túltervezése az adatbázis-logika rovására.

<!-- @col-break -->

## Következő lépések
- **Platformdöntés:** Az Oracle vagy az MS SQL Server végleges kiválasztása.
- **Zárolási tesztek:** Különböző tranzakciós izolációs szintek (pl. Read Committed vs. Serializable) korai tesztelése.
- **Prototípus:** Alapvető API kapcsolat tesztelése az adatbázissal.