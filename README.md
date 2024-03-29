Ebben a repóban nem folyik fejlesztés. A konkrét project itt található: https://github.com/Code-for-Hungary/bmm-frontend





# Közlönyfigyelő / Bulletin Monitor

Már többször felmerült, hogy fejlesszünk egy eszközt, ami rendszeresen, vagy legalább minden reggel automatikusan átfutja a közlönyt és kulcsszavak, törvények száma alapján küld egy üzenetet, ha van benne releváns tartalom. Ez sokat segít civil szervezeteknek abban, hogy időben tudjanak reagálni frissen megjelent, sokszor még nem hatályos jogszabályokra.

Ide kerülnek fel (PDF) a közlönyök:
https://magyarkozlony.hu/

- Kiemelve mindig a legfrissebb közlöny pdf formátumban, ezt kéne figyelnie az eszköznek:
![image](https://user-images.githubusercontent.com/25430950/232760741-b13f5c7b-498a-4f96-85b8-1c9b9075211d.png)

- Van, amikor naponta frissül, van, hogy több is megjelenik egy nap, néha kimarad 3-4 nap.

## Figyelésre érdemes oldalak:
- Közbeszerzési értesítő: https://www.kozbeszerzes.hu/ertesito/ letölthető pdf nagyjából minden hétköznap
- Magyar közlöny: https://magyarkozlony.hu/ pdf
- Anonimizált bírósági határozatok: https://eakta.birosag.hu/anonimizalt-hatarozatok docx, lehet évre szűrni és év szerint csökkenő/növekvő sorrendbe rendezni; a lista JSON-ban érkezik [repo](https://github.com/Code-for-Hungary/casescraper)
- MNV szerződések: https://mnv.hu/gazdalkodas/kozbeszerzesek/szerzodeskereso JSON, van benne dátum, lehet lapozni

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
Keresést alapvetően nem kell végeznie, azt a monitor végzi.

### Monitor
Feladata a feliratkozások/leíratkozások kezelése, események/adatok fogadása a scraperektől, keresés az adatokban és a feliratkozók értesítése. A backend API-t ad a feladataihoz.

## Műveletek, folyamatok
1. _Feliratkozás "esemény"re:_ kiválasztható az esemény fajtája (ezek az ismert esemény generáló programok) és megadhatók paraméterek, egyelőre szöveg pl. pontosvesszővel elválasztva (igazából a scrapertől függ)

2.a. _Értesítés küldése:_ a Scraper (esemény generáló) API-n keresztül elkéri a neki szánt paramétereket, lefut velük és ha történt esemény, akkor API-n keresztül szól róla a Monitornak, aki értesíti a feliratkozókat.

2.b. _Új adat feltöltése a monitorba:_ a Scraper új adatot talált, szöveges formára alakítja és beküldi a Monitornak. A Monitor az új tartalomban keresi a felíratkozott kulcsszavakat és találat esetén értesíti a felíratkozókat.

3. _Leiratkozás:_ a Monitor által küldött levében van leíratkozó link, azzal megoldható.

## Kérdések
- _Hogyan kapcsolja össze a scraperből érkező adatokat az értesítésekkel?_

HTTP API-n keresztül

- _Milyen kulcsszavakra lehet feliratkozni? Szabadszavas? Szótövezés beépítése? Előre meghatározott?_

Feliratkozáskor a Monitor szempontjából szabad szöveg adható meg, ami tartalmazhat az adott Scraper által hozott szabályokat (pl. kulcsszavak legyenek pontosvesszővel elválasztva)

- _Nagyságrendileg hány feliratkozásra számítunk?_

Kezdetben 5-150. Igényfelmérő kérdőívet 20-an töltötték ki.

- _Nagyságrendileg hány értesítő emailt kell kiküldenünk mondjuk Közlöny scraper eseményre mondjuk?_

Naponta 0-200. Egy felhasználó akár több kulcsszóra is feliratkozhat, viszont simán előfordulhat, hogy hetekig, hónapokig nem jön olyan tartalom, ami releváns lenne.

- _Hogyan küldjük ki az értesítő emaileket?_
1. valami gmail vagy általunk hostolt email accountból
2. nagyobb mennyiség esetén pl. mailgun vagy hasonló szolgáltatással

- _Létezik-e más (külföldi) hasonló alkalmazás?_


## Architecture/resources
GH Monitor backend: https://github.com/Code-for-Hungary/bmm-backend

GH Monitor frontend: https://github.com/Code-for-Hungary/bmm-frontend

Monitor frontend: http://bmmfrontend.billy.hu/

GH Protoscraper: https://github.com/Code-for-Hungary/bmm-protoscraper

Endpoint #1 (bulletin): http://bmmbackend.billy.hu/api/events/bygenerator/994966da-4cd6-420a-9597-ded3dfb85fc1

Endpoint #2 (protoscraper): http://bmmbackend.billy.hu/api/events/bygenerator/994966da-53ea-4797-a01d-7926c0b8053d



Bulletin scraper: https://github.com/zolizoli/bulletinMonotorScraper (needs sync with bmm API)


