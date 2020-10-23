# Bash käsikirja

Tuomas Toivonen
22.10.2020

Käsitteet, syntaksi, yleisimmät komennot, esimerkkejä ja käytäntöjä... Tiivistetty [Greg's BashGuide][gregs] -oppaan pohjalta.

```sh
$ # Sisältää koodiesimerkkejä
```

## Käsitteet
| Käsite | Selite |
| ------ | ------ |
| Komentotulkki (shell) | Ohjelma, joka lukee ja tulkitsee syötettyjä komentoja, käynnistää ja yhdistelee erillisiä prosesseja, ja tarjoaa prosesseille yhtenäisen ajoympäristön. Käyttöjärjestelmä, graafinen käyttöliittymä tai terminaali ei ole komentotulkki. Komentotulkkia kuitenkin usein suoritetaan terminaalissa. |
| Komento | Välilyönnein eroteltu merkkijono (komento + argumentit), jonka komentotulkki tulkitsee suoritettavaksi komennoksi (word splitting) |
| Skripti | Komentotulkin suorittama tekstitiedosto, jossa on sekvenssi shell-komentoja ja alussa rivi **#!/usr/bin/env bash** (shebang), joka kertoo, että kyseessä on bash-komentotulkilla suoritettava shell-skripti |
| Word Splitting | Komentotulkki pilkkoo syötteensä komennoksi ja argumenteiksi välimerkkien (normaalisti välilyönti, kts. IFS) kohdalta. Siksi kehittäjältä vaaditaan erityistä tarkkuutta käsittelemään hallitusti tilanteet, jossa tiedostonimet, muuttujat tai muu komennossa käytettävä data sisältää välilyöntejä. Toiminto vaanii pahaa aavistamattomia tulokkaita, myös veteraaneja. Paras puolustus on jokerimerkkien ja lainausmerkkien ahkera käyttö.

## Komentojen tyypit
| Tyyppi | Selite |
| ------ | ------ |
| executable | Ulkoinen komento |
| builtin | Sisäänrakennettu komento |
| keyword | Syntaktinen avainsana |
| function | Funktio |
| alias | Vaihtoehtoinen nimitys komennolle |

## Varatut erikoismerkit
| Merkki | Selite |
| ------ | ------ |
| ``` ``` | Word splitting |
| ```$``` | Muuttujien yms. laajennus |
| ```'``` | Quotaus, literaalinen |
| ```"``` | Quotaus, muuttujilla |
| ```\ ``` | Escape |
| ```#``` | Kommentti |
| ```=``` | Sijoitus, vertailu jne. |
| ```[ ]``` | Testit, globit, indeksointi jne. |
| ```[[ ]]``` | Testit |
| ```!``` | Historia |
| ```>``` | Uudelleenohjaus, vertailu |
| ```>>``` | Uudelleenohjaus |
| ```<``` | Uudelleenohjaus, vertailu |
| ```\|``` | Putki |
| ```;``` | Erotinmerkki |
| ```{ }``` | Komentoryhmä, ekspansio jne.  |
| ```( )``` | Subshell |
| ```(( ))``` | Aritmeettinen evaluointi |
| ```*``` | Jokerimerkki |
| ```?``` | Jokerimerkki |
| ```~``` | Kotihakemisto |
| ```&``` | Tausta-ajo yms. |

### Parametrit
| Tyyppi | Lähde |
| ------ | ------ |
| Ympäristömuuttujat | Suoritusympäristö |
| Erikoisparametrit | Komentotulkki |
| Järjestysparametrit | Kutsuja |

## Parametrien tyypit
| Tyyppi | Selite |
| ------ | ------ |
| String | Merkkijono |
| Integer | Kokonaisluku |
| Array | Taulukko (harva ja avainnettava) |

## Erikoisparametrit
| Parametri | Selite |
| ------ | ------ |
| ```$0``` | Skriptin nimi tai polku (epäluotettava) |
| ```$1..{n}``` | Järjestysparametrin n arvo |
| ```"$*"``` | Järjestysparametrit merkkijonossa, IFS:llä erotettuna |
| ```"$@"``` | Järjestysparametrit taulukossa |
| ```$#``` | Järjestysparametrien lukumäärä |
| ```$?``` | Viimeisimmän etualalla suoritetun komennon paluuarvo |
| ```$$``` | Komentotulkin PID |
| ```$!``` | Viimeisimmän taustalla suoritetun komennon PID |
| ```$_``` | Viimeisimmän suoritetun komennon viimeinen argumentti |

Komentotulkki tarjoaa joukon erikoisparametreja, jotka ovat aina luettavissa.

## Ympäristömuuttujat
| Parametri | Selite |
| ------ | ------ |
| ```HOSTNAME``` | Koneen nimi |
| ```PWD``` | Työhakemisto |
| ```RANDOM``` | Satunnaisluku 0 - 32767 (heikko) |
| ```UID``` | Käyttäjän id |
| ```HOME``` | Käyttäjän kotihakemisto |
| ```PATH``` | Komentroriviohjelmien sijainnit |
|  |  |

Suoritusympäristö ja/tai komentotulkki tarjoaa joukon ympäristömuuttujia, jotka ovat aina luettavissa.

## Parametriekspansio
| Ekspansio | Selite | taulukkomuoto
| ------ | ------ | ------ |
| ```${parameter}``` | käytä arvoa |  |
| ```${parameter:-word}``` | käytä oletusarvoa arvon puuttuessa |  |
| ```${parameter:=word}``` | sijoita ja käytä oletusarvoa arvon puuttuessa |  |
| ```${parameter:+word}``` | käytä toista arvoa kun arvo on asetettu |  |
| ```${parameter:offset:length}``` | alimerkkijono | alitaulukko |
| ```${#parameter}``` | merkkijono pituus | taulukon elementtien lukumäärä |
| ```${parameter#(#)pattern}``` | poista lyhyin (pisin) alkuun ankkuroitu osuma | tee kaikille elementeille |
| ```${parameter%(%)pattern}```  | poista lyhyin (pisin) loppuun ankkuroitu osuma | tee kaikille elementeille |
| ```${parameter/(/)pattern/string}``` | korvaa ensimmäinen (kaikki) ankkuroimaton osuma merkkijonolla | tee kaikille elementeille |
| ```${parameter/#pattern/string}``` | korvaa alkuun ankkuroitu osuma merkkijonolla. lisää etuliite tyhjällä patternilla | tee kaikille elementeille |
| ```${parameter/%pattern/string}``` | korvaa loppuun ankkuroitu osuma merkkijonolla. lisää loppuliite tyhjällä patternilla | tee kaikille elementeille |

Näillä voi manipuloida muuttujien sisältöä ilman ulkoisia komentoja. Suosi näitä ulkoisten komentojen sijaan, kun asianmukaista.

## Jokerimerkit (globit)
| Parametri | Selite |
| ------ | ------ |
| ```*``` | Matchaa mikä tahansa merkkijono |
| ```?``` | Matchaa mikä tahansa yksittäinen merkki |
| ```[A-Za-z]``` | Matchaa jokin merkeistä |

Jokerimerkit on implisiittisesti ankkuroitu tiedostonimien alkuun ja loppuun. Jokerimerkki ei koskaan matchaa kauttamerkkiin (/), eli toisessa hakemistossa sijaitsevaan tiedostoon.

Esimerkki - Tekstitiedostojen listaus:
```sh
$ ls *.txt
```

## Regex ja pattern matching
TODO

## Aaltosulkuekspansio:
| Muoto | Selite |
| ------ | ------ |
| ```{a,b}``` | yhdistelmät |
| ```{0..9}``` | jonot |

Hyödyllinen toiminto generoimaan samankaltaisia argumentteja tai jonoja.

Esimerkki - Backup-tiedostojen luonti:
```sh
$ for file in *; do cp "$file"{,.bak}; done
```

## Ehdolliset operaattorit
| Operaattori | Selite |
| ------ | ------ |
| ```&&``` | ehdollinen ja |
| ```\|\|``` | ehdollinen tai |

Komennolla on paluuarvo 0 (true) - 255 (false)

## Valintarakenteet
| Rakenne | Selite |
| ------ | ------ |
| ```if-elif-else``` | if-rakenne |
| ```[[ ]] \|\| { }``` | vaihtoehto if-rakenteelle |
| ```case x in foo) ;; bar) ;; *) ;; esac``` | case-rakenne. caset tukee globeja. ;;& -syntaksilla sallitaan fall-through. |

Esimerkki - If-rakenne:
```sh
$ TODO
```

Esimerkki - Case-rakenne:
```sh
$ TODO
```

## Silmukat
| Rakenne | Selite |
| ------ | ------ |
| ```while``` | toista kun |
| ```until``` | toista kunnes |
| ```for-in``` | iteroi sanat |
| ```for-loop``` | iteroi kunnes |
| ```select```  | menu-tyylinen valinta |

| Komento | Selite |
| ------ | ------ |
| ```break``` | poistu silmukasta |
| ```continue``` | hyppää silmukan alkuun |

Esimerkki - Argumenttien iterointi:
```sh
$ TODO (shorthand)
```

## Testit
| Testi | Selite |
| ------ | ------ |
| ```!``` | negaatio |
| ```&&``` | ja |
| ```\|\|``` | tai |
| ```()``` | ryhmittely |
| ```-e``` | tiedosto olemassa |
| ```-s``` | tiedosto olemassa ja sisältöä |
| ```-f``` | normaali tiedosto |
| ```-d``` | hakemisto |
| ```-h``` | symbolinen linkki |
| ```-nt``` | uudempi |
| ```-ot``` | vanhempi |
| ```-r``` | lukuoikeus |
| ```-w``` | kirjoitusoikeus |
| ```-x``` | suoritusoikeus |
| ```-O``` | omistajakäyttäjä |
| ```-G``` | omistajaryhmä |
| ```-z``` | tyhjä string |
| ```-n``` | epätyhjä string |
| ```=```, ```!=``` | samat/eri stringit (sallii globit) |
| ```=~``` | matchaa regexiin |
| ```<```, ```>``` | järjestyksen vertailu |
| ```-eq``` | samat luvut |
| ```-ne``` | eri luvut |
| ```-lt```, ```-le``` | vähemmän tai sama luku |
| ```-gt```, ```-ge``` | enemmän tai sama luku |
| ```-t``` | tiedostokahva avattu |
| ```-p``` | putki olemassa |

```[[``` -komento suorittaa testejä.

## Taulukot
| Syntaksi | Selite |
| ------ | ------ |
| ```a=(x y)``` | taulukkoliteraali |
| ```a+=(x y)``` | lisää elementtejä |
| ```a=(*)``` | kaikki tiedostonimet taulukkoon |
| ```a=([i]=x [i2]=y)``` | harva taulukko |
| ```"${a[@]}"``` | levitä taulukko sanoiksi. lainausmerkit quotaavat jokaisen elementin |
| ```"${a[*]}"``` | kokoa taulukko IFS:llä pilkotuksi merkkijonoksi. lainausmerkit quotaavat jokaisen elementin |
| ```"${!a[@]}"``` | indeksit tai avaimet |
| ```${#a[@]}``` | taulukon koko |
| ```unset 'a[i]'``` | poista taulukon alkio. vaatii lainausmerkit, ettei sekaannu globiin |
| ```a=("${a[@]}")``` | uudelleenindeksoi harva taulukko |
| ```declare -A a``` | assosiatiivinen taulukko |

Pidä merkkijonot taulukossa niin kauan kuin mahdollista, jotta välilyöntien semantiikka säilyy.

Indekoidun taulukon alaindeksi muodostaa aritmeettisen kontekstin.

Ainoa luotettava tapa erottaa merkkijonot toisistaan on pitää ne taulukossa, tai käyttää IFS:n NUL-arvoa.

## I/O
Prosessin syöte (input) ja tuloste (output) voivat kohdistua järjestysparametreihin, ympäristömuuttujiin tai tiedostokahvoihin (tiedostot, putket, laitteet, soketit jne.)

### Syöte
| Komento | Selite |
| ------ | ------ |
| ```shift``` | hylkää järjestysparametreja |
| ```VAR=val command``` | ympäristömuuttujan asettaminen komennolle |
| ```export VAR=val``` | ylentää muuttujan ympäristömuuttujaksi |
| ```read``` | lue standardisyötteestä muuttujaan |
| ```Ctrl-d``` | EoF, stdin suljettu |

### Tuloste
| Komento | Selite |
| ------ | ------ |
| ```>&2``` | tulosta virheet standardivirheeseen |

### Tiedostokahvat
Tiedostokahvoilla viitataan tiedostoihin, putkiin, laitteisiin, terminaaleihin, soketteihin jne. Tiedostokahvaan viitataan numerolla.

### Standardivirrat
| Nimi | Numero | Suomeksi |
| ------ | ------ | ------ |
| ```stdin``` | 0 | Standardisyöte |
| ```stdout``` | 1 | Standardituloste |
| ```stderr``` | 2 | Standardivirhe |

Uudella prosessilla on käytössään aina yllä luetellut kolme standardivirtaa. Standardivirrat on oletuksena yhdistetty terminaaliin ja uudelleenohjattavissa uudelleenohjauskomennoilla.

### Uudelleenohjaus
| Syntaksi | Selite |
| ------ | ------ |
| ```>``` | Standardituloste tiedoston päälle |
| ```>>``` | Standardituloste tiedoston loppuun |
| ```2>``` | Standardivirhe tiedoston päälle |
| ```2>>``` | Standardivirhe tiedoston loppuun |
| ```&>``` | Standardituloste ja -virhe tiedostoon |
| ```2>&1``` | Standardivirran duplikointi. Ohjaa standardivirhe sinne minne standardituloste osoittaa (järjestyksellä väliä!) |
| ```<``` | Standardisyötteen ohjaus |
| ```{ ... } < >``` | Standardisyötteen ja -tulosteen ohjaus komentoryhmälle |
| ```while/for/if... < >```| Standardisyötteen ja -tulosteen ohjaus ohjausrakenteelle |
| ```2> /dev/null``` | Bittiämpäri |

Katha tulostevirtaa **EI SAA** ohjata samaan tiedostoon itsenäisesti, muuten ne astuvat toistensa varpaille. Käytä duplikointioperaattoria >&.

### Heredoc/herestring
| Syntaksi | Selite |
| ------ | ------ |
| ```<<EOF``` | Pieni setti monirivistä tekstiä, joka voi sisältää muuttujia. Ei sisennystä. |
| ```<<'EOF'``` | Ei käsittele muuttujia. |
| ```<<-EOF``` | Sallii tab-sisennyksen |
| ```<<<""``` | Merkkijono, joka voi sisältää muuttujia. |

Suosi herestringiä echon putkituksen sijaan.
Heredocia voi seurata muitakin uudelleenohjauskia.

TODO: esimerkki

### Putket
| Syntaksi | Selite |
| ------ | ------ |
| ```\|``` | Ohjaa komennon standarditulosteen toisen komennon standardisyötteeseen. Putken jokainen komento suoritetaan subshellissä. |

### Process substitution
| Syntaksi | Selite |
| ------ | ------ |
| ```<()``` | Ajaa komennon, joka kirjoittaa shellin luomaan FIFO:oon. Korvaantuu FIFO:n tiedostopolulla. |
| ```>()``` | Ajaa komennon, joka lukee syötteen kutsuvan komennon tulosteesta. |

## Yhdistelmäkomennot
Valinta- ja toistorakenteet, ehtolauseet, aritmeettiset kontekstit, subshellit ja komentoryhmät ovat IO:n kannalta yhdistelmäkomentoja.

Tiedostot avataan yhdistelmäkomennolle, uudelleenohjaus pätee jokaiseen komentoon, ja tiedosto pidetään auki koko suorituksen ajan.

## Subshell ##
Subshell perii parent-ympäristön, muttei voi muuttaa sitä.

## Aritmeettinen evaluointi
| Syntaksi | Selite |
| ------ | ------ |
| ```let a='4 + 5'``` | Aritmeettinen lause |
| ```((a = b + c))``` | Aritmeettinen evaluointi |
| ```$((a * b))``` | Aritmeettinen lauseke |

C:n kaltaiset operaatiot.

## Funktiot
| Syntaksi | Selite |
| ------ | ------ |
| ```nimi() {}``` | Funktiomäärittely |
| ```nimi arg1 arg2``` | Funktiokutsu |
| ```local x``` | Paikallinen muuttuja |
| ```command``` | Vältä rekursio |

Funktiot voivat olla rekursiivisia

## Aliakset
| Syntaksi | Selite |
| ------ | ------ |
| ```alias x=''``` | Luo alias |

Alias muistuttaa funktiota, mutta on kuin pikanäppäinkomento. Se ei toimi skripteissä, ei ota argumentteja, ei omista paikallisia muuttujia tai käyttäydy rekursiivisesti.

## Rakenteiden poistaminen
| Syntaksi | Selite |
| ------ | ------ |
| ```unset``` | Poista funktio (-f) tai muuttuja (-v) |
| ```unalias``` | Poista alias |

## Sourcing
| Syntaksi | Selite |
| ------ | ------ |
| ```.``` | Pistekomento (source) suorittaa skriptin nykyisessä shell-ympäristössä. |

## Job Control

### Signaalit
| Näppäinyhdistelmä | Signaali | Selite |
| ------ | ------ | ------ |
| ```Ctrl-z``` | SIGTSTP | Suspend |
| ```Ctrl-c``` | SIGINT | Terminate |
| ```Ctrl-\ ``` | SIQQUIT | Abort |

### Komennot
| Komento | Selite |
| ------ | ------ |
| ```fg``` | jatka jobia etualalla |
| ```bg``` | jatka keskeytettyä jobia taustalla |
| ```suspend``` | keskeytä jobi |
| ```jobs``` | näytä jobit |
| ```kill``` | lopeta jobi |
| ```disown``` | unohda jobi |

Jobit on toteutettu prosessiryhminä (process group). Jokaisella TTY:llä (terminaalilla) on etualan prosessiryhmä (foreground process group). Muut TTY:n prosessiryhmät ovat taustajobeja (background jobs) Running- tai Suspended -tilassa. Jobiin voidaan viitata **jobspec** -muodossa.

## Sudenkuopat (älä ikinä...)
* Parsi ```ls```-komennon tulostetta (käytä globeja)
* Testaa tiedostopäätteitä ```grep```-komennolla (käytä globeja)
* putkita ```cat```-komentoa (käytä uudelleenohjausta tai tiedostonimeä)
* Lue tiedoston rivejä ```for```-silmukalla (käytä ```while read``` -silmukkaa)
* Käytä ```seq```-komentoa laskemiseen (käytä ```for```-silmukkaa tai ```{}```-ekspansiota)
* Käytä ```expr```-komentoa (käytä aritmeettista evaluointia)
* Aseta ```IFS```:ää globaalisti (aseta komentokohtaisesti)

## Debuggaus
| Ratkaisu | Selite |
| ------ | ------ |
| ```bash -x``` | Bash tulostaa jokaisen (laajennetun) komennon ennen ajamista. |
| ```declare -p``` | Muuttujan tyyppi ja sisältö |
| ```DEBUG trap``` | Askellus |
| Bash debugger | Työkalu |

## Hyödyllisiä komentoja
| Komento | Selite |
| ------ | ------- |
| ```cd -``` | Viimeisin hakemisto |
| ```pushd```, ```popd```  | Muista hakemisto |
| ```!!``` | Viimeisin komento |
| ```tmux``` | Rinnakkaisia terminaaleja |
| ```nohup``` | Tausta-ajoon |
|  |  |
|  |  |
|  |  |

## Näppäinyhdistelmiä
| Komento | Selite |
| ------ | ------ |
| Tab | Täydennys |
| Arrow | Aiemmat komennot |
| Ctrl-l | Tyhjennä konsoli |
| Ctrl-a | Rivin alkuun |
| Ctrl-e | Rivin loppuun |
| Ctrl-w | Leikkaa sanan alkuun |
| Alt-d | Leikkaa sanan loppuun |
| Ctrl-u | Leikkaa rivin alkuun |
| Ctrl-k | Leikkaa rivin loppuun |
| Ctrl/Shift-Ins | Kopioi/Liitä |
| Ctrl-r | Käänteinen haku |
| Alt-. | Viimeisin argumentti |
| Shift-Pg | Vieritys tekstipohjaisessa terminaalissa |
| Ctrl-s/q | Lopeta/jatka tulostusta |

## Bang-komennot (historia)
| Komento | Selite |
| ------ | ------ |
| ```!!``` | Viimeisin komento (```:p```) |
| ```!$``` | Viimeisimmän komennon viimeinen argumentti (```:p```) |
| ```!:n``` | Viimeisimmän komennon n argumentti (```:p```) |
| ```!*``` | Viimeisimmän komennon kaikki argumentit (```:p```) |
| ```^old^new``` | Viimeisin komento editoituna |
|  |  |
| ```!string``` | Viimeisin string-alkuinen komento (```:p```) |
| ```!string:$``` | Viimeisimmän string-alkuisen komennon viimeinen argumentti (```:p```) |
| ```!string:n``` | Viimeisimmän string-alkuisen komennon n argumentti (```:p```) |
| ```!string:\*``` | Viimeisimmän string-alkuisen komennon kaikki argumentit (```:p```) |
| ```!string:s^old^new``` | Viimeisin string-alkuinen komento editoituna |
|  |  |
| ```!?string``` | Viimeisin string-sanan sisältävä komento, esim. ```cat string.txt``` |
|  |  |
| ```!n``` | n komento historiassa (alusta) |
| ```!-n``` | n komento historiassa (lopusta) |

## Komentoja
| Komento | Selite |
| ------ | ------ |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)


   [gregs]: <https://mywiki.wooledge.org/BashGuide>
