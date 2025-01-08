# Domaca uloha na PCV, IoT Siet variant A

## Co som mal za ulohu
Mal som vytvorit IoT siet so serverom, ktory by spravoval samotne IoT zariadenia aj s
overovania pomocou RADIUS serveru. (10+ IoT zariadeni)

## Konecny produkt (trocha strucnejsie pisane)
Vytvoril som obvod kde boli 2 Wi-Fi routre (jeden pre IoT zariadenia (WRT300N) a druhy pre pouzivatelov (HomeRouter-PT-AC) (pre pouzivatelov som si zvolil Wi-Fi kvoli jednoduchosti pre samotnych pouzivatelov, aby to bolo "dochodca-friendly")). Potom som zakomponoval Server-PT, ktory ma zapnute Services AAA (pre RADIUS) a IoT (umoznuje registraciu/login do uctov IoT zariadeni). Routre a server su spojene Switchom (2960-24TT), ktory im umoznuje komunikovat. Vsetci pouzivatelia su pripojeni cez Wi-Fi na pouzivatelsku siet, a IoT zariadenia su pripojene na IoT router, tiez bezdratovo cez Wi-Fi. Jedina vynimka je PC, ktory je pripojeny cez ethernet. Pouzivatelia sa vedia prihlasit na stranku IoT servera (adresa 192.168.0.10), kde sa musia prihlasit a potom vedia ovladat rozne zariadenia domacnosti. Do IoT zariadeni som dal hlavne osvetlenie, ale aj par dveri, inych veci ako ovlahovaca travnika a kavovaru, a taktiez maly obvod s termostatom, radiatorom a klimatizaciou. Pouzivatelia vedia bud manualne zapnut radiator/klimu, alebo vedia nastavit termostat na urcitu min. a max. teplotu, rezim a potom termostat vdaka IoT kablom dokaze regulovat teplotu automaticky cez radiator/klimu. Instrukcie ako sa prihlasit do Wi-Fi, IoT uctu alebo do RADIUS-a (pre instalaciu novych zariadeni...) su v notesoch v projekte.
### Zariadenia
* 1x Server-PT (Services AAA a IoT) <sub>(Spravuje authorizaciu IoT zariadeni a vstupu do nich)</sub>
* 1x Switch-2960-24TT <sub>(Prepaja server a routre)</sub>
* 1x WRT300N (Router) <sub>(Umoznuje bezkablove pripojenie IoT zariadeni a automatickymi IP adresami s DHCP, pouziva skrytu SSID aby sa tam nedalo lahko napojit a sifrovanie WPA2-ENTERPRISE (nutne na RADIUS))</sub>
* 1x HomeRouter-PT-AC <sub>(Umoznuje jednoduche pripojenie pouzivatelov, tiez s automatickymi IP adresami za pomoci DHCP a sifrovanim WPA2-PERSONAL)</sub>
* 4x Zariadenia pouzivatelov <sub>(Sluzia na ovladanie IoT zariadeni)</sub>
  * 2x Laptop-PT (vymenene sietove karty na WPC300N kvoli bezdrotovemu pripojeniu)
  * 1x Smartphone-PT
  * 1x TabletPC-PT
  * 1x PC-PT
* 13x IoT zariadenia (vsetky maju zmeneny network adapter na PT-IOT-NM-1W-AC (inak nefungovalo ovladanie cez Server))
 * 4x Light <sub>(Daju sa vypnut/zapnut s 2 urovnami intenzity)</sub>
 * 2x Door <sub>(Daju sa zamknut, interface IoT vypise, ci su otvorene)</sub>
 * 1x GarageDoor <sub>(Da sa otvorit/zavriet)</sub>
 * 1x Fan <sub>(Da sa vypnut/zapnut s 2 rychlostiami)</sub>
 * 1x Lawn Sprinkler <sub>(Da sa vypnut/zapnut)</sub>
 * 1x Thermostat <sub>(Ukazuje nameranu teplotu, selekcia 4 rezimov (Off, Cooling, Heating, Auto), umoznuje nastavit min. teplotu a max. teplotu domacnosti)</sub>
   * 1x Air Conditioner <sub>(Da sa vypnut/zapnut manualne, alebo vdaka IoT kabloveho prepojenia s termostatom je ovladane aj automaticky)</sub>
   * 1x Furnace <sub>(Da sa vypnut/zapnut manualne, alebo vdaka IoT kabloveho prepojenia s termostatom je ovladane aj automaticky)</sub>

## Postup prace
### Elementy
Najprv som si pridal samotne zariadenia. Vedel som, ze bude potrebny server na RADIUS, tak som zacal s nim. Potom som pridal WRT router pre IoT zariadenia (aby sa mali kam pripojit), HomeRouter pre pouzivatelov (aby sa aj oni mali kam pripojit). Este som sice pripojenie neriesil, ale pridal som medzi tieto 3 zariadenia Switch, aby sa neskor dali prepojit a vedeli komunikovat. Napokon som zacal pridavat IoT zariadenia. Vsetky okrem Furnace a Thermostatu som pridal hned na zaciatku, tieto dve som pridal neskor ked som sa o nich dozvedel. Ako posledne som pridal Laptopy, smartphone, tabletPC a PC.
### Kablaz
Po pridani zariadeni som prepojil Routre a Server cez switch. Viacej som riesit ani nemusel, okrem IoT zariadeni (Thermostat, AC a radiator) neskor, a jedineho PC cez Copper Straight-Through, kedze pouzivatelia a ostatne zariadenia sa budu pripajat bezkablovo.
### Setup Servera
Serveru som najprv nastavil staticku IP adresu, aby sa s nim dalo komunikovat a aby aj IoT zariadenia vedeli ktory to je. Zvolil som si adresu 192.168.0.10, pretoze to je jednoducha adresa na zapamatanie, a zacina este pred DHCP rozsahmi routerov. Napokon som zapol sluzbu AAA, kde som pridal 1 klienta <sub>(Name: SmartHome, IP 192.168.0.1, Key: VerySecureSecret1234)</sub> (Toto sa neskor pouzije pri sifrovani IoT routera). Po zhotoveni Client uctu som urobil User Setup pre vsetky IoT zariadenia. Napokon som zapol IoT sluzbu, a to je vsetko ohladom servera.
### Setup Routerov
Setupy som robil ciste cez rozhranie GUI.
* WRT300N -> Nastavil som mu adresu 192.168.0.1 a uistil sa, ze DHCP je zapnute a nemalo by konfliktovat s RADIUS serverovou IP adresou a ani zariadeniami (rozsah je 192.168.0.11-192.168.0.70). Napokon som isiel do Wireless tab-u GUI, kde som mu nastavil SSID TheSmartestOfHomes69 a zaroven ju nastavil ako skrytu. Potom som presiel do sub-tab-u Wireless Security, kde som nastavil sifrovanie na WPA2 Enterprise <sub>(RADIUS Server: IP servera pre RADIUS (192.168.0.10), Shared Secret: Secret Klientskeho uctu RADIUS-a (VerySecureSecret12334))</sub>. Viacej som nemenil.
* HomeRouter-PT-AC -> V tomto Routeri bolo trocha neprijemnejsie pracovat, kedze tam bolo viac nastaveni a nie vsetky boli jasne. Najprv som mu nechal adresu 192.168.0.1 a nechal som vypnutu DHCP (tvorilo to iba same problemy, pripojenie to vzdy iba znemoznilo a zariadenia aj bez toho od niekadial beru DHCP adresy). Potom som presiel do okna Wireless, kde som vypol siet 5GHz-2 a nastavil SSID zostavajucich sieti na Home-[frekvencia]. V podpaneli Wireless security som vsetkym sietam nastavil Sifrovanie WPA2 Personal s heslom "welcomehome123". Ine som nechal nezmenene.
### Setup pouzivatelskych zariadeni
Postup pripojenia na Wi-Fi zalezi podla zariadenia. Laptopy a PC staci pripojit cez rozhranie [PC Wireless], zatial co telefony a tabletPC treba nastavit manualne cez [Config]>[Wireless0] (neviem z akej ery tam simuluju telefony, ked uz napr. stare HTC telefony mali na to Android interface...). Ethernetove PC-PT nastavovat netreba, kedze sa pripaja priamo do GigabitEthernetu.
### Registracia IoT pouzivatelskeho uctu (musi byt zhotovena pred setupom IoT zariadeni)
Otvoril som rozhranie PC-PT, kde som v okne [Desktop] otvoril webovy prehliadac. Cez neho som sa nasledne pripojil na IP adresu IoT servera <sub>(192.168.0.10)</sub>, kde som klikol na Hypertext "Sign up now". To ma zobralo do okna registracie IoT uctu, kde som nastavil User na "Domov" a Pass na "samdoma123". Tieto udaje som si zapisal, pretoze budu pouzite pri vsetkych IoT zariadeniach.
### Setup IoT zariadeni
Postup pripojenia na Wi-Fi bol rovnaky pre vsetky zariadenia. Najprv som si zapol Advanced mod nastaveni, kde som v okne [I/O Config] zmenil network adapter (na PT-IOT-NM-1W-AC) a vo vacsine pripadov vypol Bluetooth. Potom som presiel do okna [Config], a tam podokna [Wireless0]. Tam som najprv zmenil SSID na siet IoT routera <sub>(TheSmartestOfHomes69)</sub> a potom v [Authentication] som nastavil rezim na WPA2 a nastavil User ID a Password <sub>(Podla Pouzivatelskych uctov v AAA rozhrani RADIUS servera)</sub>. Nakoniec som presiel do okna [Settings], a tam som nastavil IoT Server na rezim Remote Server a nastavil hodnoty podla servera RADIUS <sub>(IP: 192.168.0.10, User: Domov (z IoT sluzby, nedavno tvoreneho uctu), Pass: samdoma123 (tiez z IoT sluzby))</sub> a pripojil ich.
## Ako sa siet pouziva (pre pouzivatelov)
Aby dokazal pouzivatel komunikovat so zariadeniami IoT siete, potrebuje byt napojeny na domovsku siet. To moze zrealizovat bud cez Wi-Fi, alebo ethernet. Postup je mierne rozlicny podla zariadenia...(v bode [Setup pouzivatelskych zariadeni])... Potom staci aby si pouzivatel otvoril webovy prehliadac a napojil sa na adresu [192.168.0.10]. Tam sa prihlasi na vytvoreny IoT ucet, alebo moze zaregistrovat novy, ale to by mu bolo zbytocne kedze by nan neboli pripojene ziadne IoTcka). Po uspesnom prihlaseni sa otvori pouzivatelske rozhranie, kde pouzivatel vidi vsetky zariadenia aj s nazvami a seriovymi cislami. Odtialto vie rozlicne zariadenia ovladat, v resp. vidiet urcite hodnoty, ak nejake meraju (thermostat...).
## Ako sa siet pouziva (pri pridavani noveho IoT zariadenia)
Pri instalacii novych IoT zariadeni je postup celkom jednoduchy. Ako prve je potrebne pridat samotne zariadenie do siete. Po pridani (netreba zapajat, kedze to vyuzije Wi-Fi) musime otvorit rozhranie servera. Tam otvorime okno [Services] a podokno [AAA]. Pojdeme do oddelenia [User Setup], kde si vymyslime vhodny nazov a heslo pre toto zariadenie. Napokon ho ulozime a ideme naspat na zariadenie. Otvorime si Advanced rezim okna, potom okno [I/O Config] a zmenime Network Adapter na [PT-IOT-NM-1W-AC]. Potom pokracujeme dalej do okna [Config], kde otvorime okno [Wireless0]. Tam nastavime SSID routera pre IoT zariadenia <sub>(TheSmartestOfHomes69)</sub> a zmenime typ autentikacie na WPA2. User ID a Password nastavime na novo-nastaveny AAA ucet. Nakoniec sa vraciame do podokna [Settings], kde v oddeleni [IoT Server] menime rezim na Remote Server a nastavujeme ho na hodnoty RADIUS servera <sub>(IP: 192.168.0.10, User: Domov, Pass: samdoma123)</sub>. Voala, zariadenie by malo byt spravne nastavene a malo by sa zjavit vo web rozhrani RADIUS IoT stranky.
## Vyhody/Nevyhody zo stranky bezpecnosti
Podla mna je vyuzitie RADIUS servera celkom dobry napad, kedze to vyzaduje autentikaciu na pripojenie do IoT Routera, a taktiez aj samotneho portalu IoT. Jedine problemy, ktore mna napadaju, su v pripade naburania do siete (Brute-Force hesla, Ethernetom, do Switchu...), kde by sa mohol napadnik dostat do zariadeni. 

## Problemy pri praci
Praca bohuzial bezproblemova nebola, ale problemy nastastie neboli kriticke a dali sa vyriesit restartovanim a modlenim. Jedine problemy boli bud s automatickou kablazou (zapajala kable do nevhodnych portov, hlavne pri pripajani PC-PT do pouzivatelskeho Routera), alebo s Wi-Fi pripojenim (IoT zariadenia sa jednoducho nechceli propojit a bolo treba restartovat simulaciu) alebo so samotnym pripojenim IoT zariadeni ku RADIUS serveru (v okne [Config] sa jednoducho nechceli pripojit, aj ked boli uspesne pripojene na Router.)



Richard, 2024
