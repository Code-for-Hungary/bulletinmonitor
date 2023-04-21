# Közlönyfigyelő / Bulletin Monitor

Már többször felmerült, hogy fejlesszünk egy eszközt, ami rendszeresen, vagy legalább minden reggel automatikusan átfutja a közlönyt és kulcsszavak, törvények száma alapján küld egy üzenetet, ha van benne releváns tartalom. Ez sokat segít civil szervezeteknek abban, hogy időben tudjanak reagálni frissen megjelent, sokszor még nem hatályos jogszabályokra.

Ide kerülnek fel (PDF) a közlönyök:
https://magyarkozlony.hu/

- Kiemelve mindig a legfrissebb közlöny pdf formátumban, ezt kéne figyelnie az eszköznek:
![image](https://user-images.githubusercontent.com/25430950/232760741-b13f5c7b-498a-4f96-85b8-1c9b9075211d.png)

- Van, amikor naponta frissül, van, hogy több is megjelenik egy nap, néha kimarad 3-4 nap.

## Prioritás funkciók:
- felhasználók feliratkoznak "esemény"ekre, amiket paramétereznek kulcsszavakkal, kifejezésekkel (ez lehet akár egy már elfogadott törvénynek a száma is - ha éppen valamit módosítanak, és arról kell infó)
- amikor felkerül egy közlöny, ami ilyen kulcsszót, kifejezést, jogszabályt tartalmaz, kap az illető egy értesítő emailt.
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
### Scraper/esemény generáló
Bármilyen program lehet, bármit csinálhat, pl. Közlöny scrape, https://onlineszamla.nav.gov.hu/informatikai_valtozasok scrape stb. A Monitorral HTTP API-n keresztül tud kommunikálni. Az, hogy mit, hogyan, hol, mikor, milyen időközönként csinál, teljesen a saját dolga. Függetlenül, önállóan működik. Technikailag bárki bármilyen esemény generálót írhat, ha az szabványosan kommunikál a Monitorral.

### Monitor
Feladata a felíratkozások/leíratkozások kezelése, események fogadása a scraperektől és a felíratkozók értesítése. A backend API-t ad a feladataihoz. A felíratkozáshoz különálló frontendet kell fejleszteni.

## Műveletek, folyamatok
1. Feliratkozás "esemény"re: kiválasztható az esemény fajtája (ezek az ismert esemény generáló programok) és megadhatók paraméterek, egyelőre szöveg pl. pontosvesszővel elválasztva (igazából a scrapertől függ)

2. Értesítés küldése: a scraper (esemény generáló) API-n keresztül elkéri a neki szánt paramétereket, lefut velük és ha történt esemény, akkor API-n keresztül szól róla a Monitornak, aki értesíti a feliratkozókat.

3. Leíratkozás: a Monitor által küldött levében van leíratkozó link, azzal megoldható.

## Kérdések
- Hogyan kapcsolja össze a scraperből érkező adatokat az értesítésekkel?
HTTP API-n keresztül

- Milyen kulcsszavakra lehet feliratkozni? Szabadszavas? Szótövezés beépítése? Előre meghatározott?
Felíratkozáskor a Monitor szempontjából szabad szöveg adható meg, ami tartalmazhat az adott Scraper által hozott szabályokat (pl. kulcsszavak legyenek pontosvesszővel elválasztva)

- Nagyságrendileg hány felíratkozásra számítunk?

- Nagyságrendileg hány értesítő emailt kell kiküldenünk mondjuk Közlöny scraper eseményre mondjuk havonta?

- Hogyan küldjük ki az értesítő emaileket?
1. valami gmail vagy általunk hostolt email accountból
2. nagyobb mennyiség esetén pl. mailgun vagy hasonló szolgáltatással
