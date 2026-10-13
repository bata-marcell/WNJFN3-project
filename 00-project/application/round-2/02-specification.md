---
project_outline: "[[semester-1-hu/01-project-outline]]"
---
# Okos parkolóház-kezelő rendszer

## 1. Szereplők és jogosultságok

| Szerep | Igény | Felelősség / hozzáférés |
| --- | --- | --- |
| Ügyfél | Parkolóhely keresése, foglalása és lemondása, saját foglalások megtekintése | Keres, foglal, lemond, megtekint a saját foglalások körében. |
| Üzemeltető | Parkolóház aktuális állapotának és statisztikáinak áttekintése | Megtekinti az aktuális foglaltságot, bevételi és kihasználtsági statisztikákat. |
| Menedzser | A rendszer szakszerű üzemelésének fenntartása | Hozzáfér és szükség esetén kezeli az adatokat, foglalásokat és elhárítja a fellépő hibákat. |

## 2. Use case-ek / user storyk

### Parkolóhely foglalása és ütközésvizsgálat

- **Szereplő:** Ügyfél
- **Előfeltétel:** A felhasználó be van lépve a webes felületre, és megadott egy kívánt időintervallumot.
- **Fő folyamat:**
  1. A szereplő elindít egy keresést a megadott időintervallumra.
  2. A rendszer lekérdezi az adatbázisból a szabad parkolóhelyeket, az adatbázis-oldali tárolt eljárás meghívásával.
  3. A szereplő kiválaszt egy szabad helyet, és foglalást kezdeményez.
  4. A rendszer egy tranzakció keretében ellenőrzi, hogy a hely még mindig szabad-e az adott időintervallumban (ütközésvizsgálat).
  5. A rendszer rögzíti a foglalást és kiszámítja a várható parkolási díjat az adatbázis-oldali üzleti logika alapján.
  6. A rendszer visszajelzést ad a sikeres foglalásról.
- **Alternatív / hibafolyamatok:** Ha a 4. lépésnél az derül ki, hogy a helyet időközben más már lefoglalta (konkurens hozzáférés), a tranzakció visszagurul (rollback), és a rendszer hibaüzenetet küld az ügyfélnek, felkínálva más szabad helyeket.
- **Utófeltétel:** A foglalás rögzítésre kerül az adatbázisban, és a parkolóhely státusza az adott időablakban foglaltra változik.

## 3. Funkcionális követelmények

| Követelmény | Elfogadási kritérium | Prioritás |
| --- | --- | --- |
| Az ügyfél kereshet szabad parkolóhelyeket adott időintervallumra. | A rendszer pontosan azokat a helyeket listázza ki, amelyek az adott időszakban nincsenek lefoglalva. | Kötelező |
| Az ügyfél létrehozhat és lemondhat foglalásokat. | Sikeres tranzakció esetén a foglalás rögzül vagy törlődik, az adatbázis konzisztens marad. | Kötelező |
| A rendszer megakadályozza a kettős foglalást (ütközésvizsgálat). | Konkurens foglalási kísérletek esetén a rendszer tranzakciókezeléssel biztosítja, hogy csak egy foglalás jöhessen létre ugyanarra a helyre és időszakra. | Kötelező |
| Az adatbázis tárolt eljárásokkal és megszorításokkal kezeli a be- és kihajtást, valamint a díjszámítást. | A webes felület csak meghívja ezeket az eljárásokat, az üzleti logika érvényesítése az adatbázis szintjén történik. | Kötelező |
| Az üzemeltető megtekintheti az aktuális foglaltságot és a statisztikákat. | A rendszer összetett SQL lekérdezésekkel (pl. aggregáció, időintervallum szerinti keresés) szolgáltatja az adatokat. | Kötelező |
| Reprodukálható tesztadat-generálás. | A rendszer képes legalább százezer foglalási és parkolási eseményt generálni tesztelés céljából. | Kötelező |
| Elektromosautó-töltőhelyek és töltési folyamatok kezelése. | A rendszer képes elektromos töltőkkel felszerelt helyeket és azok használatát is kezelni. | Lehetséges |

Prioritás: **Kötelező** = a végtermékhez szükséges; **Ajánlott** = fontos; **Lehetséges** = idő esetén opcionális (bővítési lehetőség).

## 4. Üzleti szabályok és korlátok

| Szabály vagy korlát | Indoklás |
| --- | --- |
| Az adatmodellt legalább harmadik normálformáig (3NF) kell tervezni. | Biztosítja az adatok konzisztenciáját és csökkenti a redundanciát. |
| Az üzleti logika (pl. ütközésvizsgálat, díjszámítás) az adatbázisban valósul meg tárolt eljárásokkal, függvényekkel és triggerekkel. | Ez a projekt elsődleges célja és fókusza; a webes felület nem helyettesítheti ezeket. |
| A tranzakciókezelést és a konkurens hozzáférést a választott RDBMS (Oracle vagy MS SQL Server) natív lehetőségeivel kell megoldani. | A rendszernek biztonságosan kell kezelnie az egyidejű kéréseket. |
| Ugyanazt a parkolóhelyet nem foglalhatja le több felhasználó átfedő időintervallumokra. | Ez az okos parkolóház-rendszer alapvető követelménye, megakadályozza az inkonzisztens állapotokat. |

## 5. Felhasználói felület és munkafolyamat

A webes felület két fő részre oszlik: ügyféli és üzemeltetői nézetre.
Az ügyfél felületén lehetőség van szabad hely keresésére (időintervallum megadásával), foglalás indítására, a saját aktív és múltbéli foglalások áttekintésére, valamint azok lemondására. A felület a háttérben adatbázis-oldali eljárásokat hív meg.
Az üzemeltetői nézet egy dashboard, amely megjeleníti az aktuális foglaltságot, a bevételi kimutatásokat és a kihasználtsági statisztikákat, amelyeket komplex SQL lekérdezések (aggregációk) biztosítanak.

## 6. Nem funkcionális követelmények

| Követelmény | Ellenőrzés módja |
| --- | --- |
| Az adatbázis képes kezelni a párhuzamos, egyidejű foglalási kéréseket inkonzisztencia nélkül. | Stresszteszt szimulált egyidejű tranzakciókkal, izolációs szintek ellenőrzése. |
| A kiválasztott, összetett lekérdezések (min. 5) végrehajtási ideje optimalizált. | Teljesítménymérés végrehajtási tervekkel (EXPLAIN PLAN, Actual Execution Plan) optimalizálás előtt és után számszerű összehasonlítással. |
| A webes felület (és a szolgáltatásréteg) nem kerüli meg az adatbázis-szintű megszorításokat. | Integrációs tesztek, amelyek megpróbálnak érvénytelen adatokat (pl. kettős foglalás) beszúrni közvetlenül az adatbázisba, és a webes felületen keresztül is. |

## 7. Nyitott kérdések és kockázatok

| Kérdés / kockázat | Hatás | Feloldás / döntés |
| --- | --- | --- |
| Melyik adatbázis-kezelő rendszert (Oracle vagy MS SQL Server) használjuk véglegesen? | Befolyásolja a felhasznált SQL dialektust (PL/SQL vs. T-SQL) és a teljesítményelemző eszközöket. | A döntést a tervezési szakasz elején meg kell hozni és dokumentálni. |
| Hogyan valósítsuk meg leghatékonyabban az ütközésvizsgálatot a magas konkurens terhelés mellett? | Befolyásolja a rendszer válaszidejét és a holtpontok (deadlock) kialakulásának esélyét. | Két különböző tranzakciós/zárolási megközelítés összehasonlítása és a jobb megoldás kiválasztása. |
| Mennyire legyen bonyolult a webes felület? | A webes frontend túlzott komplexitása elvonhatja az időt és a fókuszt az adatbázis-tervezésről. | A funkcionális terjedelem korlátozott marad, a hangsúly az adatbázis-funkciók demonstrálásán van. |

## 8. Kezdeti technikai javaslat

### Javasolt megoldás

A rendszer alapja egy harmadik normálformáig tervezett, legalább tizenkét táblát tartalmazó relációs adatbázis (Oracle vagy Microsoft SQL Server). Az üzleti logika (foglalás, ütközésvizsgálat, be/kihajtás, díjszámítás) adatbázis-oldali tárolt eljárásokban, függvényekben, triggerekben és megszorításokban valósul meg. Ezt egészíti ki egy tetszőleges korszerű technológiával (pl. Node.js/Express, Python/FastAPI vagy Java/Spring Boot backend, és egy egyszerű frontend keretrendszer) készült webes felület, amely egy vékony szolgáltatásrétegen keresztül kommunikál az adatbázissal, és meghívja annak eljárásait.

### Technológiai irány

| Terület | Jelölt technológia / megközelítés | Megfontolás oka | Nyitott kérdés / kockázat |
| --- | --- | --- | --- |
| Adatbázis | Oracle Database (SQL, PL/SQL) VAGY Microsoft SQL Server (SQL, T-SQL) | A kiírás kifejezetten ezen rendszerek egyikének használatát írja elő. | Melyik rendszer érhető el könnyebben a fejlesztéshez és a tesztkörnyezethez? |
| Backend / Szolgáltatásréteg | Tetszőleges korszerű szerveroldali technológia (pl. Node.js, Python, Java) | Közvetítő réteg a webes felület és az adatbázis között; csak az adatbázis-eljárásokat hívja meg. | A megfelelő adatbázis-driver és kapcsolat-kezelés (connection pooling) kiválasztása. |
| Frontend | Tetszőleges korszerű kliensoldali technológia (pl. React, Vue, vagy akár egyszerű HTML/JS) | Funkcionális, korlátozott terjedelmű UI a kötelező funkciók demonstrálására. | A UI nem válhat túl komplexszé, hogy ne vonja el a fókuszt az adatbázisról. |

### Kezdeti architektúravázlat

```mermaid
flowchart LR
  U[Ügyfél / Üzemeltető] --> F[Webes Frontend]
  F --> API[Backend API / Szolgáltatásréteg]
  API --> DB[(Relációs Adatbázis - RDBMS)]
  DB --> SP[Tárolt Eljárások & Triggerek]
