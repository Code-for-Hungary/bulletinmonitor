# Közlönyfigyelő / Bulletin Monitor

Már többször felmerült, hogy fejlesszünk egy eszközt, ami rendszeresen, vagy legalább minden reggel automatikusan átfutja a közlönyt és kulcsszavak, törvények száma alapján küld egy üzenetet, ha van benne releváns tartalom. Ez sokat segít civil szervezeteknek abban, hogy időben tudjanak reagálni frissen megjelent, sokszor még nem hatályos jogszabályokra.

Ide kerülnek fel (PDF) a közlönyök:
https://magyarkozlony.hu/

- Kiemelve mindig a legfrissebb közlöny pdf formátumban, ezt kéne figyelnie az eszköznek:
![image](https://user-images.githubusercontent.com/25430950/232760741-b13f5c7b-498a-4f96-85b8-1c9b9075211d.png)

- Van, amikor naponta frissül, van, hogy több is megjelenik egy nap, néha kimarad 3-4 nap.

## Prioritás funkciók:
- felhasználók feliratkoznak kulcsszavakra, kifejezésekre (ez lehet akár egy már elfogadott törvénynek a száma is - ha éppen valamit módosítanak, és arról kell infó), és amikor felkerül egy közlöny, ami ilyen kulcsszót, kifejezést, jogszabályt tartalmaz, kap az illető egy értesítő emailt.
- leiratkozás
- Mivel pdf-ekről van szó, valószínűleg az lenne a legkényelmesebb, ha egyből elküldené a rendszer azt a bekezdést / bekezdéseket, amiben szerepel a kifejezés. Ha mondjuk 3-nál többször szerepel, akkor az egész rendelet / határozat is mehetne és persze link a pdf-hez.
- Szótövezést kéne alkalmazni, hogy a ragozások, stb ne zavarjanak be.
- GDPR kompatibilitás (adatkezelésit kell írni)

## Hasznos funkciók:
- ha minden közlöny tartalomjegyzékére is fel lehetne irakozni, jogi geekeknek. Ez is sok könnyítés ahhoz képest, hogy fel kell menni mindig a közlöny honlapra, csekkolni, hogy van-e új és megnyitni a pdf-et.
- Jó lenne kideríteni, hogy mikor kerülnek fel a közlönyök, ha van konkrét napszak, akkor arra időzítve a scrapelést, amúgy mondjuk 1-2 óránként csekkolni.
- Az emailben lehetne egy link a közlöny eredeti honlapján lévő keresőre, ha valaki korábbi tartalmakra kíváncsi.
- Közlönyön túl további eseményekre is lehetne modulokat építeni: Bírósági határozatok, Ügyészség, NAV, Rendőrség anonimizált határozatok, Társadalmi egyeztetésre bocsátott doksik (kormany.hu), OGY-nek benyújtott irományok (pl. tv.javaslat)

## Felépítés
1. Scraper: első körben a Magyar Közlöny

2. Feliratkozás: moduláris felépítés, többfajta scraperből érkező adatokkal is össze lehessen kapcsolni 

3. Értesítés küldése

 
## Kérdések
- Hogyan kapcsolja össze a scraperből érkező adatokat az értesítésekkel?
- Milyen kulcsszavakra lehet feliratkozni? Szabadszavas? Szótövezés beépítése? Előre meghatározott?
