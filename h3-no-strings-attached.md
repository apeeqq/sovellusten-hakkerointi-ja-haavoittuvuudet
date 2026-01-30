# h3 No Strings Attached

*Tekijä: Aapo Tavio*

*Pohjana Tero Karvinen ja Lari Iso-Anttila 2026: Sovellusten hakkerointi ja haavoittuvuudet 2026 kevät, [Application hacking - 2026 Spring - English ICI012AS3AE-3001 and Finnish ICI012AS3A-3003](https://terokarvinen.com/application-hacking/#h3-no-strings-attached-tero)*

<br>

## Käytettävän ympäristön ominaisuudet

- Host
  
  - Host PC: HP Laptop 15s-eq3xxx
  
  - OS: Ubuntu 24.04.3 LTS
  
  - Processor: AMD Ryzen™ 7 5825U with Radeon™ Graphics × 16
  
  - Memory: 16.0 GiB
  
  - NIC: Realtek Semiconductor Co., 802.11ax Wireless
  
  - Architecture: x86_64
  
  - Firmware version: F.20
  
  - Kernel Version: Linux 6.14.0-37-generic

- Virtual Machine
  
  - OS: Kali GNU/Linux Rolling
  
  - Release: 2025.4
  
  - Kernel Version: Linux 6.18.5+kali-amd64
  
  - Architecture: x86-64
  
  - Hardware Vendor: Qemu
  
  - Hardware Model: Standard PC \_Q35 + ICH9, 2009_
  
  - Hardware Version: pc-q35-8.2
  
  - Firmware Version: 1.16.3-debian-1.16.3-2
  
  - Firmware Date: 2014-04-01

<br>

## a) Strings. Download [ezbin-challenges.zip](https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip). Run 'passtr'. Find the correct password using 'strings'. Also find the flag. (Preferably without looking at the source, if you can.)

**29.1.2026 Klo 14.57**

Latasin ensimmäiseksi zip-tiedoston, jonka jälkeen purin sen. Tämän jälkeen ajoin ohjelman "passtr".

![](/home/aapo/.config/marktext/images/2026-01-29-15-01-47-passtr-run.png)

**Kuva 1.** Passtr ajaminen hakemistossa challenges

Ohjelma kysyi salasanaa ajettaessa. Koitin salasanaa "admin", joka ei ollut oikein.

![](/home/aapo/.config/marktext/images/2026-01-29-15-05-18-whats-passwd.png)

**Kuva 2.** Ohjelma kokonaisuudessaan toistaiseksi

Lähdin hakemaan strings ohjelman avulla salasanaa binääristä hakemistossa /home/aapo/Desktop/challenges/passtr. Komentona

```bash
$ strings passtr | less #putkitin lessiin koska tavaraa oli paljon
```

Taisin löytää heti salasanan ja lipun.

![](/home/aapo/.config/marktext/images/2026-01-29-15-11-32-passwd-flag.png)

**Kuva 3.** Oletettu salasana oli sala-hakkeri-321

Menin kokeilemaan onnistuuko tuolla salasanalla. Oikea oli.

![](/home/aapo/.config/marktext/images/2026-01-29-15-13-48-right-passwd.png)

**Kuva 4.** Salasanalla sala-hakkeri-321 sai oikean vastauksen

<br>

## b) Make a new version of the passtr.c program where the password doesn't appear directly as-is in the binary. Demonstrate with a test that the password doesn't appear. (Obfuscation is sufficient.)

**29.1.2026 Klo 19.39**

Aloin etsimään tietoa, miten voisin obfuskoida tiedoston. Netissä mainittiin ohjelmasta UPX, joten ajattelin sen olevan varmaan itselleni sopiva.

(Stack Exchange Inc. URL: [obfuscation - Obfuscating C-based binaries to avoid decompilation - Stack Overflow](https://stackoverflow.com/questions/2273610/obfuscating-c-based-binaries-to-avoid-decompilation))

UPX perustuu UCL datan kompressointi algoritmiin. Ohjelma on ilmainen ja lähdekoodi on avoin.

(Wikipedia. URL: [UPX - Wikipedia](https://en.wikipedia.org/wiki/UPX))

C-ohjelmointikieli oli minulle sen verran vieras, että minun piti katsoa tietoa, miten C:n koodia ajetaan. Näköjään se pitää muuttaa kokoajalla (compiler) ensiksi binääriksi, jonka jälkeen sen voi ajaa.

(Stack Exchange Inc. URL: [How do I execute a .c file? - Stack Overflow](https://stackoverflow.com/questions/55330597/how-do-i-execute-a-c-file))

UPX ajetaan binääritiedostolle komentorivin virallisen manuaalin mukaan, joten minun pitää ensiksi siis koota lähdekoodi.

(Komento: man upx)

Kokosin lähdekoodin komennolla

```bash
$ gcc passtr.c -o passtr-uusi #gcc on compiler ohjelma, passtr.c on c-ohjelmointikielellä tehty ohjelma, -o valinta ja passtr-uusi määrittää uuden binääritiedoston nimen
```

(-o valinnan lähteenä gcc:n virallinen manuaali komentorivikehotteella. Komento: man gcc)

Katsoin vielä stringsillä, että näkyihän uudessa binäärissäkin samalla tavalla salasana ja lippu.

![](/home/aapo/.config/marktext/images/2026-01-29-20-22-46-passtr-uusi.png)

**Kuva 5.** Uudessa binäärissä näkyi samat tiedot

Sitten oli vuorossa upx:n käyttö.

![](/home/aapo/.config/marktext/images/2026-01-29-20-24-55-upx-passtr-uusi.png)

**Kuva 6.** Upx ohjelmalla uuden binääritiedoston pakkaus

Sain piilotettua osan koodista, mutta mielestäni vieläkin näkyi liikaa tietoja.

![](/home/aapo/.config/marktext/images/2026-01-29-20-27-46-upx-1.png)

**Kuva 7.** Ohjelma paketoituna upx:llä ja avattuna stringsillä

Näin upx:n GitHubin sivuilla maininnan, jonka mukaan valintojen --best ja --brute käyttäminen saavuttaisi parhaimman lopputuloksen.

(Oberhumer, Molnar & Reiser. URL: [GitHub - upx/upx: UPX - the Ultimate Packer for eXecutables](https://github.com/upx/upx))

Ajoin nykyisen tiedoston päälle vielä upx:n komennolla

```bash
$ upx --best passtr-uusi
```

![](/home/aapo/.config/marktext/images/2026-01-29-20-35-40-upx-passtr-uusi-best.png)

**Kuva 8.** Upx:n ajaminen valinnalla --best ja lopputulos

Avasin stringsillä taas binäärin.

![](/home/aapo/.config/marktext/images/2026-01-29-20-38-48-strings-passtr-uusi-2.png)

**Kuva 9.** Samanlainen tuloste tuli ulos kuin aiemmin

Valinta --best ei siis muuttanut mitään tiedostossa. Toki upx:llä paketoitu versio oli parempi kuin ilman paketoimista, mutta salasanakin on mielestäni liian helposti arvattavissa. Tietysti jos salasana olisi erilainen tai merkkijonon "What's the password?" muuttaisi johonkin toiseen muotoon, voisi tilanne olla taas paljonkin erilainen.

<br>

## c) Packd. Run 'packd' from the package [ezbin-challenges.zip](https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip). What is the password? What is the flag? (This task is slightly more challenging. Write down the approaches you tried and hypotheses you came up with. Hopefully you'll reach the goal yourself, but if not, the walkthrough will be revealed in class...)

**30.1.2026 Klo 10.36**

Lähdin ensiksi ajamaan ohjelman. Ohjelma oli samalla kaavalla toimiva kuin passtr ohjelma.

![](/home/aapo/.config/marktext/images/2026-01-30-10-39-06-packd-ran.png)

**Kuva 10.** Packd ohjelma kysyi myös salasanaa

Avasin stringsillä ohjelman.

![](/home/aapo/.config/marktext/images/2026-01-30-10-41-07-strings-packd.png)

**Kuva 11.** Packd ohjelmassa viitteitä salasanasta ja lipusta

Koska minulla oli vain binääritiedosto hallussa, lähdin etsimään tietoa netistä, miten voisin saada ohjelman selkokieliseksi. Löysin artikkelin, jonka mukaan Ghidra nimisellä ohjelmalla voisi saada tietoa binääristä käänteismallinnuksella.

(Khaishagi 2024. URL: [Ghidra Tutorial: Introduction](https://medium.com/@zaid960928/ghidra-tutorial-introduction-a3889e138434))

Katsoin Kalin sivuilta, että ghidran taitaa saada suoraan aptin repositoriosta, joten annoin komennot

```bash
$ sudo apt-get update #Päivittää paikallisen listan saatavilla olevista paketeista

$ sudo apt-get install ghidra #Asentaa ghidran apt-repositoriosta
```

<br>

### Ghidra

**30.1.2026 Klo 11.05**

Katsoin aiemmin mainitsemastani artikkelista (Khaishagi 2024) ohjeita ghidran käyttöön. Ensimmäiseksi avasin Ghidran ja tein uuden projektin kohdasta File > New Project. Valitsin kohdan "Non-Shared Project" ja projektin hakemistoksi /home/aapo, ts. kotihakemistoni.

Toin packd-ohjelman projektiini ja avasin sen koodiselaimella (Code Browser). Seuraavaksi Ghidra kysyi, haluanko analysoida packd:n, johon vastasin kyllä.

![](/home/aapo/.config/marktext/images/2026-01-30-11-16-24-packd-analyze.png)

**Kuva 12.** Ghidran tuloste ohjelman analysoimisesta

Minulla oli kaikki oletusvalinnat seuraavassa ikkunassa valittuna. Tämän jälkeen sivuni näytti alla olevan kuvan mukaiselta.

![](/home/aapo/.config/marktext/images/2026-01-30-11-20-03-packed-code-browser.png)

**Kuva 13.** Packd koodiselaimessa analysoituna

Löysin kohdan, jossa luki jotain salasanasta.

![](/home/aapo/.config/marktext/images/2026-01-30-11-31-41-ghidra-passwd.png)

**Kuva 14.** Salasana tai sen osa näkyi Ghidrassa

Kuitenkin kohdassa näkyi yhtä paljon salasanasta, kuin aikaisemmin stringsinkin kanssa.

Kokeilin salasanaa "piilos-An", mutta se oli väärä.

![](/home/aapo/.config/marktext/images/2026-01-30-11-35-43-piilos-An-failure.png)

**Kuva 15.** Ghidrassa ja stringsissä näkyvä salasana ei ollut oikea

**30.1.2026 Klo 12.28**

Klikkailin erilaisia kohtia Ghidran koodiselaimessa ja katsoin muutaman videon aiheesta.

(Hammond 2022. URL: [GHIDRA for Reverse Engineering - YouTube](https://www.youtube.com/watch?v=oTD_ki86c9I&t=260s))

(cyberryan 2025. URL:  [Reverse Engineering Made EASY with Ghidra! | Beginner Tutorial - YouTube](https://www.youtube.com/shorts/4DpV0zdJEk0))

Löysin koodista tekstin, josta sain viitteitä, että ohjelma olisi pakattu upx:llä.

![](/home/aapo/.config/marktext/images/2026-01-30-12-37-23-upx-clue-packd.png)

**Kuva 16.** Assembly kohdassa olevaa tekstiä

Ajattelin, että pystyisinköhän deobfuskoimaan binäärin upx:llä.

**30.1.2026 Klo 13.01**

Purin upx paketin -d valinnalla.

![](/home/aapo/.config/marktext/images/2026-01-30-13-06-52-upx-d-packd.png)

**Kuva 17.** Tuloste oli ainakin lupaavan näköinen pakettia purettaessa

Tämän jälkeen näkyikin jo stringsillä helposti erilaista tietoa kuin aiemmin.

![](/home/aapo/.config/marktext/images/2026-01-30-13-09-26-packd-unpacked-strings.png)

**Kuva 18.** Uusia tietoja stringsillä avatessa

Kokeilin tämän johdosta salasanaa "piilos-AnAnAs", joka oli oikea.

![](/home/aapo/.config/marktext/images/2026-01-30-13-12-11-piilos-ananas.png)

**Kuva 19.** Oikealla salasanalla tulostui lippu

<br>

## d) Optional bonus: Cryptopals. [Crypto Challenge Set 1](https://www.cryptopals.com/sets/1). This can be done as a bonus over several weeks. If you solve items 1 .. "4. Detect single-character XOR", you've already stepped into the world of cryptography.

### 1. Convert hex to base64

**30.1.2026 Klo 16.42**

Lähdin tekemään tehtävää ja ensimmäiseksi huomasin sivun alalaidassa olevan maininta, jonka mukaan aina pitäisi käsitellä raaka tavuja. Tehtävänannossa oli hexa merkkijono ja base64 merkkijono (Cryptopals. URL: [Challenge 1 Set 1 - The Cryptopals Crypto Challenges](https://www.cryptopals.com/sets/1/challenges/1)).

Lähdin selvittelemään asiaa ja ChatGPT antoi mielestäni selkeimmän vastauksen. Tai oikeastaan ainoan, josta sain selvää.

Selvitin raakatavujen, hexojen, binäärien ja base64 suhdetta toisiinsa syötteellä "tell me relation of raw bytes, hex, binaries and base64".

(ChatGPT. Kielimalli: GPT-5.2)

Vastauksessaan tekoäly sanoi raakatavujen olevan alinta ja "oikeaa" dataa. Tämän jälkeen tulivat binääri, jonka kantaluku on 2. Heksojen kantaluku on 16 ja base64 kantaluku on 64. Tein vastauksesta piirustuksen ja liitin sen tähän. Ehkä voi jotain toistakin joskus auttaa hahmottamisessa.

![](/home/aapo/.config/marktext/images/2026-01-30-16-54-35-data-formats-explained.drawio.png)

**Kuva 20.** Sama arvo esitetty neljässä eri muodossa

Lisäksi kysyin "what is 0x in raw bytes". Tekoäly vastasi sen olevan vain helpottamaan raakatavun tulkintaa ihmiselle. Etuliitteestä 0x näkee, että kyseessä on raakatavu.

Kysyin vielä kolmannen kysymyksen, joka oli "how can i convert raw bytes to base64". ChatGPT vastasi, että minun oli muutettava ensin heksat binääriksi, jonka jälkeen ne olisi jaoteltava kuuden bitin paloiksi, koska base64 ottaisi binäärit tässä muodossa. Lisäksi AI kertoi linuxilla olevan xxd niminen ohjelma, jolla voisi kääntää raakatavut, ja heksat, base64-muotoon.

Löysin lisää ohjeita xxd-ohjelmasta.

(Stack Exchange Inc. URL: [encoding - How can I convert from hex to base64? - Super User](https://superuser.com/questions/158142/how-can-i-convert-from-hex-to-base64))

Sain ainakin suoraan heksoista base64 merkkijonon, joka oli tehtävänannossakin.

![](/home/aapo/.config/marktext/images/2026-01-30-17-23-11-xxd-hex-to-base64.png)

**Kuva 21.** Heksat käännetty base64-muotoon

Valinta -r tarkoittaa käänteistä (revert), eli hexat käännetään binääriksi. Valinta -p puolestaan tarkoittaa "plain" tekstiä.

(xxd virallinen manuaali Kali Linuxilla. Komento: man xxd)

En löytänyt oikein tietoa, miten saisin käännettyä raakatavut base64-muotoon. Ajattelin ehkä tajunneeni asian vain väärin. Sinänsähän hexat, joita käsittelin, olivat raakatavuja. Merkkijono olisi pitänyt vain tavuttaa kahden heksan osiin ja lisätä 0x eteen. Ehkä tämä oli tapa, jolla tehtävä kuuluikin tehdä.

<br>

<br>

## Lähteet

ChatGPT. Kielimalli: GPT-5.2. Syöte: how can i convert raw bytes to base64. Generoitu: 30.1.2026.

ChatGPT. Kielimalli: GPT-5.2. Syöte: tell me relation of raw bytes, hex, binaries and base64. Generoitu: 30.1.2026.

ChatGPT. Kielimalli: GPT-5.2. Syöte: what is 0x in raw bytes. Generoitu: 30.1.2026.

Cryptopals. Convert hex to base64. Luettavissa: [Challenge 1 Set 1 - The Cryptopals Crypto Challenges](https://www.cryptopals.com/sets/1/challenges/1). Luettu: 30.1.2026.

Cyberryan 31.8.2025. YouTube. Reverse Engineering Made EASY with Ghidra! | Beginner Tutorial. Katsottavissa: [Reverse Engineering Made EASY with Ghidra! | Beginner Tutorial](https://www.youtube.com/shorts/4DpV0zdJEk0). Katsottu: 30.1.2026.

Gcc ohjelman virallinen manuaali komentorivikehotteella. Linux Kali. Komento: man gcc. Luettu: 29.1.2026.

Hammond, J. 27.4.2022. YouTube. GHIDRA for Reverse Engineering (PicoCTF 2022 #42 'bbbloat'). Katsottavissa: [GHIDRA for Reverse Engineering - YouTube](https://www.youtube.com/watch?v=oTD_ki86c9I&t=260s). Katsottu: 30.1.2026.

Khaishagi, Z. 2.1.2024. Ghidra Tutorial: Introduction. Luettavissa: [Ghidra Tutorial: Introduction](https://medium.com/@zaid960928/ghidra-tutorial-introduction-a3889e138434). Luettu: 30.1.2026.

Oberhumer, Molnar & Reiser. upx. Luettavissa: [GitHub - upx/upx: UPX - the Ultimate Packer for eXecutables](https://github.com/upx/upx). Luettu: 29.1.2026.

Stack Exchange Inc. How can I convert from hex to base64?. Luettavissa: [encoding - How can I convert from hex to base64? - Super User](https://superuser.com/questions/158142/how-can-i-convert-from-hex-to-base64). Luettu: 30.1.2026.

Stack Exchange Inc. How do I execute a .c file?. Luettavissa: [How do I execute a .c file?](https://stackoverflow.com/questions/55330597/how-do-i-execute-a-c-file). Luettu: 29.1.2026.

Stack Exchange Inc. Obfuscating C-based binaries to avoid decompilation. Luettavissa: [obfuscation - Obfuscating C-based binaries to avoid decompilation - Stack Overflow](https://stackoverflow.com/questions/2273610/obfuscating-c-based-binaries-to-avoid-decompilation). Luettu: 29.1.2026.

UPX virallinen manuaali komentorivikehotteella. Linux Kali. Komento: man upx. Luettu: 29.1.2026.

Wikipedia 24.1.2026. UPX. Luettavissa: [UPX - Wikipedia](https://en.wikipedia.org/wiki/UPX). Luettu: 29.1.2026.

Xxd virallinen manuaali. Kali Linux. Komento: man xxd. Luettu: 30.1.2026.

<br>

<br>

<br>

<br>

<br>

<br>

*Tätä dokumenttia saa kopioida ja muokata GNU General Public License (versio 3 tai uudempi) mukaisesti. http://www.gnu.org/licenses/gpl.html*
