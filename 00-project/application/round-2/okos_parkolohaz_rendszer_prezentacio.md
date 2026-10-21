<!-- @slide Okos parkolóház-kezelő rendszer -->

# Okos parkolóház-kezelő rendszer

<!-- @gap 3 -->

<!-- @set-align center -->

**Rendszerspecifikáció és architekturális tervek**

Foglalás • ütközésvizsgálat • adatbázis-központú üzleti logika

RENDSZERTERV

---

<!-- @slide A rendszer célja -->

# A rendszer célja

<!-- @set-align center -->

Egy webes felület, amely az adatbázisra támaszkodva kezeli a parkolóhelyeket és a foglalásokat.

<!-- @columns 2 5 -->

## Ügyfél
- Szabad helyet keres
- Időintervallumra foglal
- Megnézi vagy lemondja saját foglalásait

<!-- @col-break -->

## Üzemeltető
- Aktuális foglaltságot lát
- Bevételi kimutatásokat néz
- Kihasználtsági statisztikákat elemez

<!-- @set-align center -->
**Központi elv:** Az üzleti szabályok az adatbázisban élnek. A webes réteg ezeket az eljárásokat hívja meg. Fókusz: konzisztens foglalások, megbízható tranzakciók és mérhető adatbázis-teljesítmény.

---

<!-- @slide Szereplők és jogosultságok -->

# Szereplők és jogosultságok

<!-- @set-align center -->

Három fő nézet, eltérő feladatkörökkel.

- **Ügyfél:** Szabad parkolóhelyek keresése, foglalás létrehozása és lemondása, saját aktív és korábbi foglalások megtekintése. A hozzáférés a saját foglalásaira korlátozódik.
- **Üzemeltető:** Aktuális foglaltság áttekintése, bevételi kimutatások megtekintése, kihasználtsági statisztikák elemzése. Dashboard jellegű kezelőfelület.
- **Menedzser:** Fellelhető hibák kezelése, az adatbázisban fellépett hibák elhárítása (emberi vagy szoftver eredetű). Hozzáférés az adatbázishoz.

---

<!-- @slide Foglalási folyamat -->

# Foglalási folyamat

<!-- @set-align center -->

A kiválasztott helyet a rendszer a rögzítés pillanatában ismét ellenőrzi.

1. **Keresés:** Időintervallum megadása.
2. **Lekérdezés:** Szabad helyek listázása.
3. **Kiválasztás:** Ügyfél helyet választ.
4. **Ütközés-vizsgálat:** Tranzakciós újraellenőrzés.
5. **Rögzítés:** Foglalás és díjszámítás.

*Ha a helyet közben más lefoglalta: rollback, hibaüzenet és alternatív szabad helyek felajánlása.*

---

<!-- @slide Rendszerarchitektúra -->

# Rendszerarchitektúra

<!-- @set-align center -->

A frontend nem tartalmazza és nem kerüli meg az üzleti szabályok érvényesítését.

- **Felhasználók:** Ügyfél / Üzemeltető
- **Frontend:** Webes felület
- **Backend API:** Szolgáltatásréteg (Foglalás, ütközésvizsgálat, be-/kihajtás, díjszámítás)
- **Relációs adatbázis:** Oracle / MS SQL Server. Legalább 3NF-es adatmodell, minimum 12 relációs táblával.
- **Adatbázis-oldali logika:** Tárolt eljárások, függvények, triggerek, megszorítások.

---

<!-- @slide Adatbázis-központú tervezés -->

# Adatbázis-központú tervezés

<!-- @set-align center -->

A projekt elsődleges szakmai fókusza az üzleti logika és az adatintegritás.

- **3NF adatmodell:** Csökkenti az adatredundanciát, segíti a konzisztens tárolást.
- **Tárolt eljárások:** Foglalás és díjszámítás, be- és kihajtási folyamatok.
- **Megszorítások, triggerek:** Szabályok kikényszerítése, érvénytelen állapotok tiltása.
- **Adatbázis-választás:** Oracle Database (PL/SQL) vagy Microsoft SQL Server (T-SQL). A konkrét RDBMS kiválasztása nyitott döntés; a terv mindkét lehetőséggel számol.

---

<!-- @slide Ütközésvizsgálat és tranzakciókezelés -->

# Ütközésvizsgálat és tranzakciókezelés

<!-- @set-align center -->

A kettős foglalás megakadályozása kötelező követelmény.

- **Kiinduló helyzet:** Két ügyfél közel egyszerre próbálja lefoglalni ugyanazt a helyet és időszakot.
- **Adatbázis ellenőriz:** Tranzakcióban újra ellenőrzi a hely elérhetőségét, és kezeli a konkurens kéréseket.
- **Biztonságos eredmény:** Csak egy foglalás jöhet létre. A másik kérés rollbacket és hibaüzenetet kap.

*Nyitott tervezési feladat: zárolási/tranzakciós megközelítések összehasonlítása, holtpontok és válaszidő vizsgálata.*

---

<!-- @slide Funkcionális követelmények -->

# Funkcionális követelmények

<!-- @set-align center -->

A végtermék kötelező funkciói és lehetséges bővítése.

- **Szabad helyek keresése:** Pontos találatok adott időintervallumra (KÖTELEZŐ)
- **Foglalás és lemondás:** Tranzakciós rögzítés/törlés (KÖTELEZŐ)
- **Kettős foglalás kizárása:** Konkurens kérések kezelése (KÖTELEZŐ)
- **Adatbázis üzleti logika:** Eljárások és megszorítások (KÖTELEZŐ)
- **Üzemeltetői statisztikák:** Foglaltság, bevétel, kihasználtság (KÖTELEZŐ)
- **Tesztadat-generálás:** Legalább 100 000 esemény (KÖTELEZŐ)

*Lehetséges bővítés: elektromosautó-töltőhelyek és töltési folyamatok kezelése.*

---

<!-- @slide Felhasználói felület és statisztikák -->

# Felhasználói felület és statisztikák

<!-- @set-align center -->

A webes felület két, célhoz igazított nézetet biztosít.

<!-- @columns 2 5 -->

## Ügyfél nézet
- Időintervallum megadása
- Szabad helyek megjelenítése
- Foglalás indítása
- Aktív és múltbéli foglalások
- Lemondás

<!-- @col-break -->

## Üzemeltetői dashboard (szemléltető)
- **FOGLALTSÁG:** 78%
- **NAPI BEVÉTEL:** 1852 €
- **KIHASZNÁLTSÁG:** 65%

<!-- @set-align center -->
Az üzemeltetői mutatókat összetett SQL-lekérdezések és aggregációk állítják elő.

---

<!-- @slide Nem funkcionális követelmények -->

# Nem funkcionális követelmények

<!-- @set-align center -->

Nem elég, hogy működik: terhelés alatt is helyesnek és mérhetően gyorsnak kell maradnia.

- **Párhuzamosság:** Stresszteszt egyidejű tranzakciókkal, izolációs szintek ellenőrzése.
- **Lekérdezési teljesítmény:** Legalább 5 összetett lekérdezés mérése, végrehajtási tervek összehasonlítása.
- **Integritás:** Érvénytelen adatok beszúrásának tesztelése, adatbázis-szintű védelem igazolása.

*Mérőeszközök: EXPLAIN PLAN (Oracle), illetve Actual Execution Plan (SQL Server).*

---

<!-- @slide Nyitott kérdések és kockázatok -->

# Nyitott kérdések és kockázatok

<!-- @set-align center -->

A tervezés korai szakaszában meghozandó döntések.

1. **Adatbázis kiválasztása:** Oracle vagy MS SQL Server? (Hatás: SQL-dialektus, fejlesztői környezet és teljesítménymérő eszközök.)
2. **Ütközésvizsgálat:** Melyik tranzakciós/zárolási stratégia működik jobban nagy konkurens terhelés mellett? (Vizsgálat: válaszidő és deadlock-kockázat.)
3. **Frontend terjedelme:** Mennyire legyen összetett a webes felület? (Irány: egyszerű, funkcionális UI, az adatbázis demonstrálására fókuszálva.)

---

<!-- @slide Összefoglalás és következő lépések -->

# Összefoglalás és következő lépések

<!-- @set-align center -->

A cél egy megbízható, adatbázis-központú parkolóház-kezelő rendszer.

- **Technológiai döntés:** Oracle vagy SQL Server
- **Adatmodell:** Legalább 12 tábla, 3NF
- **Üzleti logika:** Eljárások, triggerek, megszorítások
- **Webes felület:** Vékony szolgáltatásréteg
- **Tesztelés:** Konkurencia, teljesítmény, integritás

*A siker kulcsa: adatkonzisztencia, biztonságos tranzakciók és mérhető teljesítmény.*# Okos parkolóház-kezelő rendszer

## Rendszerspecifikáció és architekturális tervek

Foglalás • ütközésvizsgálat • adatbázis-központú üzleti logika

## 1. dia

Okos parkolóház-kezelő rendszer

Rendszerspecifikáció és architekturális tervek

Foglalás • ütközésvizsgálat • adatbázis-központú üzleti logika

RENDSZERTERV

01

---

## 2. dia

A rendszer célja

Egy webes felület, amely az adatbázisra támaszkodva kezeli a parkolóhelyeket és a foglalásokat.

02

Ügyfél

Szabad helyet keres
Időintervallumra foglal
Megnézi vagy lemondja saját foglalásait

Üzemeltető

Aktuális foglaltságot lát
Bevételi kimutatásokat néz
Kihasználtsági statisztikákat elemez

Központi elv

Az üzleti szabályok az adatbázisban élnek
A webes réteg ezeket az eljárásokat hívja meg

Fókusz: konzisztens foglalások, megbízható tranzakciók és mérhető adatbázis-teljesítmény.

---

## 3. dia

Szereplők és jogosultságok

Három fő nézet, eltérő feladatkörökkel.

03

Ügyfél

Szabad parkolóhelyek keresése
• Foglalás létrehozása és lemondása
• Saját aktív és korábbi foglalások megtekintése
A hozzáférés a saját foglalásaira korlátozódik.

Üzemeltető

Aktuális foglaltság áttekintése
• Bevételi kimutatások megtekintése
• Kihasználtsági statisztikák elemzése
Dashboard jellegű kezelőfelület.

Menedzser

Fellelhető hibák kezelése
Az adatbázisban fellépett hibák elhárítása (emberi vagy szoftver eredetű)
Hozzáférés az adatbázishoz

---

## 4. dia

Foglalási folyamat

A kiválasztott helyet a rendszer a rögzítés pillanatában ismét ellenőrzi.

04

Keresés

Időintervallum megadása

1

Lekérdezés

Szabad helyek listázása

2

Kiválasztás

Ügyfél helyet választ

3

Ütközés-vizsgálat

Tranzakciós újraellenőrzés

4

Rögzítés

Foglalás és díjszámítás

5

Ha a helyet közben más lefoglalta: rollback, hibaüzenet és alternatív szabad helyek felajánlása.

---

## 5. dia

Rendszerarchitektúra

A frontend nem tartalmazza és nem kerüli meg az üzleti szabályok érvényesítését.

05

Felhasználók

Ügyfél / Üzemeltető

Frontend

Webes felület

Backend API

Szolgáltatásréteg

Relációs adatbázis

Oracle / MS SQL Server

Adatbázis-oldali logika

Tárolt eljárások • függvények
Triggerek • megszorítások

Szolgáltatások

Foglalás • ütközésvizsgálat
Be-/kihajtás • díjszámítás

Legalább 3NF-es adatmodell, minimum 12 relációs táblával.

---

## 6. dia

Adatbázis-központú tervezés

A projekt elsődleges szakmai fókusza az üzleti logika és az adatintegritás.

06

3NF adatmodell

Csökkenti az adatredundanciát
Segíti a konzisztens tárolást

Tárolt eljárások

Foglalás és díjszámítás
Be- és kihajtási folyamatok

Megszorítások, triggerek

Szabályok kikényszerítése
Érvénytelen állapotok tiltása

Adatbázis-választás: Oracle Database (PL/SQL) vagy Microsoft SQL Server (T-SQL).

A konkrét RDBMS kiválasztása nyitott döntés; a terv mindkét lehetőséggel számol.

---

## 7. dia

Ütközésvizsgálat és tranzakciókezelés

A kettős foglalás megakadályozása kötelező követelmény.

07

Kiinduló helyzet

Két ügyfél közel egyszerre próbálja lefoglalni ugyanazt a helyet és időszakot.

Adatbázis ellenőriz

Tranzakcióban újra ellenőrzi a hely elérhetőségét, és kezeli a konkurens kéréseket.

Biztonságos eredmény

Csak egy foglalás jöhet létre. A másik kérés rollbacket és hibaüzenetet kap.

Nyitott tervezési feladat: zárolási/tranzakciós megközelítések összehasonlítása, holtpontok és válaszidő vizsgálata.

---

## 8. dia

Funkcionális követelmények

A végtermék kötelező funkciói és lehetséges bővítése.

08

Szabad helyek keresése

Pontos találatok adott időintervallumra

KÖTELEZŐ

Foglalás és lemondás

Tranzakciós rögzítés/törlés

KÖTELEZŐ

Kettős foglalás kizárása

Konkurens kérések kezelése

KÖTELEZŐ

Adatbázis üzleti logika

Eljárások és megszorítások

KÖTELEZŐ

Üzemeltetői statisztikák

Foglaltság, bevétel, kihasználtság

KÖTELEZŐ

Tesztadat-generálás

Legalább 100 000 esemény

KÖTELEZŐ

Lehetséges bővítés: elektromosautó-töltőhelyek és töltési folyamatok kezelése.

---

## 9. dia

Felhasználói felület és statisztikák

A webes felület két, célhoz igazított nézetet biztosít.

09

Ügyfél nézet

• Időintervallum megadása
• Szabad helyek megjelenítése
• Foglalás indítása
• Aktív és múltbéli foglalások
• Lemondás

Üzemeltetői dashboard – szemléltető

FOGLALTSÁG

78%

NAPI BEVÉTEL

1852 €

KIHASZNÁLTSÁG

65%

Időszakos kihasználtság (illusztráció)

Az üzemeltetői mutatókat összetett SQL-lekérdezések és aggregációk állítják elő.

---

## 10. dia

Nem funkcionális követelmények

Nem elég, hogy működik: terhelés alatt is helyesnek és mérhetően gyorsnak kell maradnia.

10

Párhuzamosság

Stresszteszt egyidejű tranzakciókkal
Izolációs szintek ellenőrzése

Lekérdezési teljesítmény

Legalább 5 összetett lekérdezés mérése
Végrehajtási tervek összehasonlítása

Integritás

Érvénytelen adatok beszúrásának tesztelése
Adatbázis-szintű védelem igazolása

Mérőeszközök: EXPLAIN PLAN (Oracle), illetve Actual Execution Plan (SQL Server).

---

## 11. dia

Nyitott kérdések és kockázatok

A tervezés korai szakaszában meghozandó döntések.

11

01  Adatbázis kiválasztása

Oracle vagy MS SQL Server?
Hatás: SQL-dialektus, fejlesztői környezet és teljesítménymérő eszközök.

02  Ütközésvizsgálat

Melyik tranzakciós/zárolási stratégia működik jobban nagy konkurens terhelés mellett?
Vizsgálat: válaszidő és deadlock-kockázat.

03  Frontend terjedelme

Mennyire legyen összetett a webes felület?
Irány: egyszerű, funkcionális UI, az adatbázis demonstrálására fókuszálva.

---

## 12. dia

Összefoglalás és következő lépések

A cél egy megbízható, adatbázis-központú parkolóház-kezelő rendszer.

12

1

Technológiai döntés

Oracle vagy SQL Server

2

Adatmodell

Legalább 12 tábla, 3NF

3

Üzleti logika

Eljárások, triggerek, megszorítások

4

Webes felület

Vékony szolgáltatásréteg

5

Tesztelés

Konkurencia, teljesítmény, integritás

A siker kulcsa: adatkonzisztencia, biztonságos tranzakciók és mérhető teljesítmény.

---
