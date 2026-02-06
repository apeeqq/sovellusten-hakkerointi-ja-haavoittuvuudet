# h4 Some Disassembly Required

*Tekijä: Aapo Tavio*

*Pohjana Tero Karvinen ja Lari Iso-Anttila 2026: Sovellusten hakkerointi ja haavoittuvuudet 2026 kevät, [Application hacking - 2026 Spring - English ICI012AS3AE-3001 and Finnish ICI012AS3A-3003](https://terokarvinen.com/application-hacking/#h4-some-disassembly-required-tero)*

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
  
  - Hardware Model: Standard PC _Q35 + ICH9, 2009_
  
  - Hardware Version: pc-q35-8.2
  
  - Firmware Version: 1.16.3-debian-1.16.3-2
  
  - Firmware Date: 2014-04-01

<br>

## x) Read/watch/listen and summarize. (In this x-subsection, you don't need to do tests on a computer; just reading or listening and a summary is enough. A few bullet points are sufficient for the summary.)

- Hammond 2022: [Ghidra for Reverse Engineering (PicoCTF 2022 #42 'bbbloat')](https://www.youtube.com/watch?v=oTD_ki86c9I) (Video, about 20 min)
- Optional: € Eagle and Nancy 2020: [The Ghidra Book: 2. Reversing And Disassembly Tools](https://learning.oreilly.com/library/view/the-ghidra-book/9781098125684/xhtml/ch02.xhtml#ch02lev29) (Often recommended book about Ghidra)

<br>

### Tiivistelmät

- Ghidra on käänteismallinnukseen (reverse engineering) tehty ohjelma
  
  - Ohjelmalla voi mm. löytää merkkijonoja binääritiedostosta ja tarkastella decompilerilla koodia

(Hammond. YouTube. URL: [GHIDRA for Reverse Engineering (PicoCTF 2022 #42 &#39;bbbloat&#39;) - YouTube](https://www.youtube.com/watch?v=oTD_ki86c9I))

<br>

- Monet sovellukset voivat tukea ja vahvistaa Ghidraa käänteismallinnuksessa

- File ohjelma
  
  - Näyttää tiedoston tyypin
  
  - Perustuu taikanumeroihin (magic numbers)

- Stripping
  
  - Stripping tarkoittaa symbolien poistamista binääritiedostoista
  
  - Strip on yksi kategorian ohjelmista
  
  - Ei vaikuta ohjelman ajamiseen vaikka tiedostokoko onkin pienempi toiminnan seurauksena

- Obfuskointi
  
  - Tarkoittaa todellisen tarkoituksen hämärtämistä/peittämistä
  
  - Binääritiedostojen yhteydessä tarkoittaa ohjelman todellisen toiminnan peittämistä

(Eagle & Nance 2020. Luku 2 Reversing and disassembly tools)

<br>

## a) Install Ghidra.

**4.2.2026 Klo 15:42**

Asensin Ghidran jo tehtävässä h3 No Strings Attached apt-repositoriosta Kalilla. En kokenut tarpeelliseksi selvittää uudestaan tässä asennusta.

<br>

## b) rever-C. Reverse engineer the packd binary to C language with Ghidra. Find the main program. Give variables descriptive names. Explain the program's operation. Solve the task from the binary, without the original source code. [ezbin-challenges.zip](https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip)

**4.2.2026 Klo 16:07**

Minulla oli valmiiksi jo edellisestä tehtävästä tarvittava tiedosto packd, joten lähdin avaamaan sitä ghidrassa. Deobfuskoin sen ensimmäiseksi, koska se oli pakattu upx:llä. Komentona oli

```bash
$ upx -d packd
```

![](/home/aapo/.config/marktext/images/2026-02-04-16-16-39-upx-d-packd.png)

**Kuva 1.** Tiedoston paketoinnin poistaminen

Toin tiedoston projektiini.

![](/home/aapo/.config/marktext/images/2026-02-04-16-20-49-packd-import.png)

**Kuva 2.** Ghidra tunnisti automaattisesti tiedoston tietoja

Tämän jälkeen avasin packd tiedoston koodiselaimessa (code browser), jonka annoin ghidran automaattisesti analysoida.

**5.2.2026 Klo 15:29**

Main funktio aukesi heti automaattisesti decompileriin. Kyseinen funktio on löydettävissä myös omalla main nimellään "symbol tree" valikosta.

![](/home/aapo/.config/marktext/images/2026-02-05-15-33-35-symbol-tree-main.png)

**Kuva 3.** Symbol tree valikon funktioita

Seuraavaksi lähdin selvittelemään koodia ja nimeämään mahdollisuuksien mukaan muuttujia uudelleen.

![](/home/aapo/.config/marktext/images/2026-02-05-15-37-01-packd-main-original.png)

**Kuva 4.** Main funktio alkuperäisessä muodossa

Muutin ensimmäiseksi muuttujan local_28 nimeksi passwd_input, jolla tarkoitan syöttäjän käyttämää salasanaa. Strcmp-funktio on merkkijonojen vertailuun tarkoitettu valmis kirjaston funktio. Funktion paluuarvo on 0, jos parametreissä olevat merkkijonot täsmäävät toisiaan.

(kartik 2025. GeeksforGeeks. URL: [C strcmp() - GeeksforGeeks](https://www.geeksforgeeks.org/c/strcmp-in-c/).)

Muutin seuraavaksi muuttujan iVar1 nimeksi result, koska se on kokonaisluku (integer), joka kertoo kokonaisluvulla salasanan oikeellisuuden.

![](/home/aapo/.config/marktext/images/2026-02-05-15-54-53-packd-var-edited.png)

**Kuva 5.** Main funktion muuttujat muutettuina ghidrassa

Ohjelmassa ensimmäiseksi määritellään muuttujat. Funktio puts on selkeästi näytölle tulostettava merkkijono. Funktiolla \__isoc99_scanf_ selkeästi tallennetaan käyttäjän syöte muuttujaan passwd_input. Tämän jälkeen käyttäjän syötettä verrataan oikeaan salasanaan. Muuttujan result ollessa 0, tulostetaan lippu, muutoin tulostetaan "Sorry, no bonus". Lopuksi ohjelma palauttaa joka tapauksessa kokonaisluvun 0.

<br>

## c) If backwards. Modify the passtr program's binary (without the original source code) so that it accepts all passwords except the correct one. Demonstrate with tests that the program works. [ezbin-challenges.zip](https://terokarvinen.com/loota/yctjx7/ezbin-challenges.zip)

**5.2.2026 Klo 16:17**

Lähdin avaamaan ja muuttamaan koodia ghidrassa, koska ajattelin muuten olevan lähes mahdotonta muokata binäärimuodossa olevaa koodia.

![](/home/aapo/.config/marktext/images/2026-02-05-16-21-54-passtr-main-original.png)

**Kuva 6.** Ohjelman passtr main funktio ilman muokkauksia ghidran decompilerissa

Ajattelin, että olisi kaikista helpointa vaihtaa ehtolausekkeeseen erilainen operaattori. Nyt siinä oli "==" ja laittaisin sen muotoon "!=", jotta ohjelma hyväksyisi kaikki muut salasanat paitsi "sala-hakkeri-321".

Huomasin kuitenkin, että operaattoria ei pystynyt muuttamaan ollenkaan decompilerissa. Etsin tietoa netistä, miten voisin muokata koodia vain binääritiedostolla. En löytänyt ilman tekoälyä kunnolla tietoa. Kysyin ChatGPT tekoälyltä "why i cant edit == operator in decompiler on ghidra?". Tekoäly vastasi, että decompiler koodi on vain ghidran tulkinta binääristä. Se ei ole siis aitoa koodia. Assembly näkymässä olisi vain aitoa koodia.

(ChatGPT. GPT5-mini.)

Testasin tässä kohdin vielä alkuperäistä koodia, jotta sain varmistuksen, että se hyväksyy vain salasanan "sala-hakkeri-321".

![](/home/aapo/.config/marktext/images/2026-02-05-17-05-04-passtr-test-original.png)

**Kuva 7.** Ohjelman ajaminen koodia muuttamatta

**5.2.2026 Klo 17:00**

Kysyin ChatGPT:ltä lisää neuvoja, koska aiemmassa vastauksessa puhuttiin "jump instruction" käsitteestä ja neuvottiin vaihtamaan JE muotoon JNE. Uusi syötteeni oli "I have TEST instruction there". Vastauksessa kerrottiin TEST ohjeen olevan testaamiseen käytetty ohjeistus, jonka jälkeen tulee kaksi parametriä, joita verrataan keskenään.

(ChatGPT. GPT5-mini.)

Lisäksi kysyin "what is jne". Vastauksessa kerrottiin JNE:n ja JNZ:n olevan ehtoja, jotka hyppäävät jos zero flag:ä ei ole asetettu. Puolestaan JE ja JZ hyppäisivät, jos zero flag olisi asetettu.

(ChatGPT. GPT5-mini.)

Oletin zero flag:n tarkoittavan muuttujan arvoa. Vastauksissaan tekoäly kertoi myös, että minun piti ensiksi valita assembly ikkunassa ohjeen kohdalla "Patch Instruction" muuttaakseni ohjeen. Tein näin ja muutin ohjeen muotoon JZ, koska alkuperäinen oli muodossa JNZ, jolloin se vastaisi vastakkaista muotoa. Toisinsanoen ajattelin JE:n mahdollisesti olevan väärä, koska se sopisi muodon puolesta paremmin JNE:n pariksi.

![](/home/aapo/.config/marktext/images/2026-02-05-17-33-53-JZ-in-passtr.png)

**Kuva 8.** Hyppyohjeen JNZ tilalle muutettu JZ

Tämän jälkeen ghidran file-valikosta painoin export program painiketta, jonka jälkeen valitsin tiedoston muodon alkuperäiseksi. Tiedoston ulosviemisenkin ohje oli ChatGPT:n aiemman syötteen vastauksessa.

Menin ajamaan ohjelman ja testaamaan toimiko se? Sehän toimi kuten pitikin!

![](/home/aapo/.config/marktext/images/2026-02-05-17-39-48-passtr-ran-edited.png)

**Kuva 9.** Muut syötteet toimivat ohjelmaan paitsi sala-hakkeri-321

<br>

## d) Nora CrackMe: Compile to binaries Tindall 2023: [NoraCodes / crackmes](https://github.com/NoraCodes/crackmes). Read [README.md](https://github.com/NoraCodes/crackmes/blob/master/README.md): don't look at the source code unless you need training wheels. In these tasks, binaries are reverse engineered. Binaries are not modified, because otherwise the solution to every task would be to change the return value to "return 0".

**5.2.2026 Klo 19:45**

Latasin repon zip-tiedostona sivulta [GitHub - NoraCodes/crackmes: Some CrackMe codes for Linux x86/x86_64](https://github.com/NoraCodes/crackmes#). Tämän jälkeen purin sen komennolla

```bash
$ unzip crackmes-master.zip
```

![](/home/aapo/.config/marktext/images/2026-02-05-19-50-43-downloads-crackmes.png)

**Kuva 10.** Tehtävän tiedostot purettuna

<br>

## e) Nora crackme01. Solve the binary.

**5.2.2026 Klo 19:51**

Aloitin muuttamalla compilerilla binääriksi tiedoston crackme01.c.

![](/home/aapo/.config/marktext/images/2026-02-05-20-09-38-gcc-krakki1.png)

**Kuva 11.** Tehtävätiedosto käännetty binääriksi

Katsoin ensin binääriä stringsin avulla komennolla

```bash
$ strings krakki1 | less
```

Heti ensimmäiseksi näin siellä rivin, joka viittaisi salasanaan.

![](/home/aapo/.config/marktext/images/2026-02-05-20-18-03-strings-krakki1.png)

**Kuva 12.** Merkkijono password1 löytyi binääristä

Menin ajamaan tiedoston tehtävän ohjeiden mukaan komennolla

```bash
$ ./krakki1 password1
```

![](/home/aapo/.config/marktext/images/2026-02-05-20-24-43-krakki1-solved.png)

**Kuva 13.** Salasana oli oikea

Katsoin tehtävässä hieman apua ohjelman oikeaoppiseen ajamiseen tehtävän virallisesta ohjeesta.

(Tindall 2017. URL: [An Intro to x86_64 Reverse Engineering | Nora Codes](https://nora.codes/tutorial/an-intro-to-x86_64-reverse-engineering/))

<br>

## e) Nora crackme01e. Solve the binary.

**5.2.2026 Klo 20:29**

Seuraavaksi oli samanlainen prosessi vuorossa tiedostolle crackme01e. Käänsin sen binääriksi ja ajoin.

![](/home/aapo/.config/marktext/images/2026-02-05-20-31-45-krakki2-ran.png)

**Kuva 14.** Tiedosto oli samankaltainen kuin aikaisempikin

Avasin sen stringsillä ja huomasin johtolangan salasanaan.

![](/home/aapo/.config/marktext/images/2026-02-05-20-33-39-strings-krakki2.png)

**Kuva 15.** Osa binääriä

Ajattelin merkkijonon "slm!paas.k" olevan melko varmasti obfuskoitu, mutta menin koittamaan sitä kuitenkin ensiksi.

![](/home/aapo/.config/marktext/images/2026-02-05-20-37-09-krakki2-yritys-1.png)

**Kuva 16.** Salasana ei ollut oikea

Salasanan loppuosa paas.k muutenkin ilmeisesti aiheutti ohjelman ajamisen omituisella tavalla. En löytänyt stringsin avulla muutakaan kiinnostavaa tietoa binääristä, joten avasin sen ghidrassa.

Menin main-funktioon ensimmäiseksi katsomaan, mitä sieltä löytyisi?

![](/home/aapo/.config/marktext/images/2026-02-05-20-46-23-krakki2-main.png)

**Kuva 17.** Lähtötilanne main-funktiossa

**5.2.2026 Klo 21:06**

Tutkittuani asiaa ja katsottuani edellisen tehtävän stringsillä, totesin tässä vaiheessa, että mahdollisesti koodia ei ole obfuskoitu ollenkaan. Päädyin tulokseen, koska tiedoston krakki1 avaaminen stringsillä näytti ulkomuodoltaan melko samanlaiselta kuin käsittelyssä oleva krakki2.

Nimesin hieman uudestaan muuttujia ghidrassa.

![](/home/aapo/.config/marktext/images/2026-02-05-21-29-20-krakki2-ghidra-renamed.png)

**Kuva 18.** Uudelleen nimetyt muuttujat alleviivattu punaisella

Huomasin, että komentohistoriassa minulla näkyi vain "slm" salasanan kohdalla, vaikka olin syöttänyt koko salasanan.

![](/home/aapo/.config/marktext/images/2026-02-05-21-33-39-only-slm.png)

**Kuva 19.** Tiedoston ajamisen komentohistoria

Näin ollen päättelin, että huutomerkki on ratkaiseva tekijä salasanassa, joka toimii jonkinlaisena haitallisena ohjautuvuutena komennossa. Markdownissa, ja tietääkseni muissakin syntakseissa, kenoviiva on merkki, jolla saa poistettua merkkien haitallisia toimintoja. Kokeilin siis laittaa kenoviivan ennen huutomerkkiä salasanaan ja sehän toimikin!

![](/home/aapo/.config/marktext/images/2026-02-05-21-39-46-krakki2-solved.png)

**Kuva 20.** Haasteen crackme01e ratkaisu

<br>

## f) Nora crackme02. Name the main program's variables from the reverse-engineered binary and explain the program's operation. Solve the binary.

**6.2.2026 Klo 11:36**

Aloitin tehtävän taas kääntämällä ensin tiedoston crackme02.c binääriksi, jonka jälkeen avasin sen ghidrassa analysoituna.

![](/home/aapo/.config/marktext/images/2026-02-06-11-40-36-krakki3-main-original.png)

**Kuva 21.** Ohjelman main-funktio ghidran decompilerissa

Nimesin local_c muuttujan ensiksi muotoon "counter". Tarkoitan sillä muuttujan alustamista for-silmukkaan. GeeksforGeeks:n artikkelinkin mukaan kyseistä muuttujaa käsitellään for-silmukassa silmukan hallinnointiin.

(Shokeen 2025. GeeksforGeeks. URL: [C for Loop - GeeksforGeeks](https://www.geeksforgeeks.org/c/c-for-loop/))

![](/home/aapo/.config/marktext/images/2026-02-06-12-09-24-krakki3-counter-edited.png)

**Kuva 22.** Muutetut muuttujat alleviivattu punaisella

Muuttuja uVar1 oli selkeästi funktion palautusarvo, joten nimesin sen muotoon "return_value".

![](/home/aapo/.config/marktext/images/2026-02-06-12-18-50-krakki3-edited-return-value.png)

**Kuva 23.** Muutetut muuttujat alleviivattu punaisella

Ohjelmassa kokonaisuudessaan nähdäkseni for-silmukka on vain lumetta, koska se toistuu aina vain kerran, jos salasana on väärin. For-silmukan sisällä olevassa if-ehtolausekkeessa paluuarvona annetaan aina 1, eikä muuttujaa return_value. Oikean salasanan saadessa ohjelma tulostaa onnistuneen ilmoituksen ja palauttaa muuttujan return_value arvon, joka asetetaan kokonaislukuun 0. Muussa tapauksessa, ts. ilman salasanaa, palautetaan arvo 4294967295 (heksadesimaaleina 0xffffffff).

<br>

### Binäärin selvittäminen

Menin tämän jälkeen ajamaan ohjelmaa ilman syötettä ja syötteen kanssa.

![](/home/aapo/.config/marktext/images/2026-02-06-12-33-27-krakki3-fail.png)

**Kuva 24.** Ohjelman ajaminen ilman syötettä ja väärän syötteen kanssa

Kokeilin tämän jälkeen ajaa koodissa esiintyneen salasanan "password1" kanssa. Sekään ei ollut oikein.

![](/home/aapo/.config/marktext/images/2026-02-06-12-35-44-krakki3-password1.png)

**Kuva 25.** Ghidrassa lukeva password1 ei ollut oikea syöte

**6.2.2026 Klo 12:37**

Ajattelin tarkastella for-silmukan sisällä olevaa ehtolauseketta, koska se oli olennainen mielestäni tehtävän kannalta. Ehdon ollessa epätosi, ohjelma olisi nähdäkseni ratkaistu.

![](/home/aapo/.config/marktext/images/2026-02-06-12-41-00-krakki3-conditional.png)

**Kuva 26.** For-silmukan sisällä oleva ehtolauseke

Ajattelin todennäköisesti kohdan "password1"[counter] olevan merkkijonosta indeksin poimimista, joten counterin ollessa 0, merkkijonon indeksin 0 kohdalla olisi p. Tämä ei kuitenkaan ollut oikea vastaus.

![](/home/aapo/.config/marktext/images/2026-02-06-13-11-40-krakki3-p-fail.png)

**Kuva 27.** Kirjain p ei ollut oikea vastaus

Minua kyllä ihmetytti suuresti syntaksi "password1"[counter] + -1, koska luulin ensin ratkaistavan indeksi, joka olisi p. Tämän jälkeen kokonaislukua ja merkkijonoa ei voisi käsitellä yhdessä, koska kirjain p olisi merkkijono.

Asiaa olisi todella työlästä lähteä selvittämään ilman tekoälyä, joten laitoin syötteeksi "what is if ("pass"[0] + -1) in c". Tekoäly vastasi "pass"[0] olevan tosiaan merkkijono "p", kuten aikaisemminkin totesin. Mutta tämän jälkeen vastauksessaan ChatGPT kertoi p:n olevan desimaaliarvoltansa 112. Tämän jälkeen olikin mahdollista erottaa kokonaisluvusta 112 luku 1. Tämän johdosta luku 111 olisi ASCII-muodossa kirjain "o".

(ChatGPT. GPT5-mini)

Näin ollen päättelin, että "o" voisi olla oikea vastaus. Näin olikin!

![](/home/aapo/.config/marktext/images/2026-02-06-13-33-05-krakki3-solved.png)

**Kuva 28.** Syöte tekoälyn vastauksen perusteella oli oikea

## g) Optional: And beyond. Crackme01 has multiple solutions. How many can you find? Why?

En tehnyt kyseistä tehtävää, koska minulla oli kulunut aikaa jo aikaisempiin tehtäviin kauan.

<br>

## h) Optional: Unsolicited. Crackme02 has two solutions. Can you find both?

En tehnyt kyseistä tehtävää, koska minulla oli kulunut aikaa jo aikaisempiin tehtäviin kauan.

<br>

## i) Optional, slightly more challenging: A ray. Nora crackme02e. Solve the binary.

En tehnyt kyseistä tehtävää, koska minulla oli kulunut aikaa jo aikaisempiin tehtäviin kauan.

<br>

<br>

## Lähteet

ChatGPT. Kielimalli: GPT5-mini. Syöte: I have TEST instruction there. Generoitu: 5.2.2026.

ChatGPT. Kielimalli: GPT5-mini. Syöte: what is if ("pass"[0] + -1) in c. Generoitu: 6.2.2026.

ChatGPT. Kielimalli: GPT5-mini. Syöte: what is jne. Generoitu: 5.2.2026.

ChatGPT. Kielimalli: GPT5-mini. Syöte: why i cant edit == operator in decompiler on ghidra?. Generoitu: 5.2.2026.

Eagle, C. & Nance, K. 2020. The Ghidra Book. Kolmas painos. E-kirja. Luettu: 4.2.2026.

Hammond, J. 27.4.2022. GHIDRA for Reverse Engineering (PicoCTF 2022 #42 'bbbloat'). Katsottavissa: [GHIDRA for Reverse Engineering (PicoCTF 2022 #42 'bbbloat') - YouTube](https://www.youtube.com/watch?v=oTD_ki86c9I). Katsottu: 4.2.2026.

kartik. 13.12.2025. GeeksforGeeks. C strcmp(). Luettavissa: [C strcmp() - GeeksforGeeks](https://www.geeksforgeeks.org/c/strcmp-in-c/). Luettu: 5.2.2026.

Shokeen, H. 8.10.2025. C for Loop. GeeksforGeeks. Luettavissa: [C for Loop - GeeksforGeeks](https://www.geeksforgeeks.org/c/c-for-loop/). Luettu: 6.2.2026.

Tindall, L. 16.11.2017. An Intro to x86_64 Reverse Engineering. Luettavissa: [An Intro to x86_64 Reverse Engineering | Nora Codes](https://nora.codes/tutorial/an-intro-to-x86_64-reverse-engineering/). Luettu: 5.2.2026.

<br>

<br>

<br>

<br>

<br>

<br>

*Tätä dokumenttia saa kopioida ja muokata GNU General Public License (versio 3 tai uudempi) mukaisesti. http://www.gnu.org/licenses/gpl.html*
