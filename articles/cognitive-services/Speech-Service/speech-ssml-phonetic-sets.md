---
title: Set fonetici vocali-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire il mapping dell'alfabeto fonetico al servizio riconoscimento vocale all'alfabeto fonetico internazionale (IPA) e quando usare il set.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78675336"
---
# <a name="speech-service-phonetic-sets"></a>Set fonetici del servizio vocale

Il servizio riconoscimento vocale definisce alfabeti fonetici ("set di telefono" per brevità), costituito da sette lingue; `en-US`, `fr-FR`, `de-DE`, `es-ES`, `ja-JP`, `zh-CN`e `zh-TW`. I set di telefoni del servizio voce vengono in genere mappati all' <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">alfabeto fonetico internazionale (IPA) <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. I set di telefoni del servizio di riconoscimento vocale vengono usati insieme al [linguaggio di markup sintesi vocale (SSML)](speech-synthesis-markup.md), come parte dell'offerta di servizi di sintesi vocale. In questo articolo si apprenderà come viene eseguito il mapping di questi set di telefono e quando usare il set di telefono.

# <a name="en-us"></a>[it-IT](#tab/en-US)

### <a name="english-suprasegmentals"></a>Suprasegmentals inglese

| Esempio 1 (inizio per consonante, parola iniziale per la vocale) | Esempio 2 (intervocale per consonante, parola mediana nucleo per vocale) | Esempio 3 (coda per consonante, parola finale per la vocale) | Commenti |
|--|--|--|--|
| Burger/b er **1** r-g AX r/ | Falafel/f ax-l AA **1** -f ax l/ | Guitar/g IH-t AA **1** r/ | Set di telefono del servizio di riconoscimento vocale-stress dopo la vocale della sillaba accentata |
| inopportuni/IH **2** -n AA-p AX r-t Uw 1 n/ | dissomiglianza/d IH-s IH **2**-m ax-l eh 1-r AX-t IY/ | forza lavoro/w er 1 r k-f Ao **2** r s/ | Set di telefono del servizio di riconoscimento vocale-stress dopo la vocale della sillaba sottoposta a sottolineatura |

### <a name="english-vowels"></a>Vocali inglesi

| `sapi` | `ipa` | Esempio 1     | Esempio 2 | Esempio 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| IY     | `i`   | t **EA**       | f**EE**l  | **EY**                  |
| IH     | `ɪ`   | **i**f        | f**i**  |                             |
| chiave     | `eɪ`  | **te**       | g**a**te  | d**ay**                     |
| eh     | `ɛ`   | **e**molto     | p**e**t   | m**eh** (parola rara) |
| ae     | `æ`   | **ctive**    | c**a**t   | n**Ah** (alla fine rare Word) |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**Ah** (alla fine rare Word) |
| Ao     | `ɔ`   | intervallo **o**    | c**au**se | **Ah** UT                    |
| Uh     | `ʊ`   | b**oO**k      |           |                             |
| ow     | `oʊ`  | **o**LD       | CL**o**ne | g**o**                      |
| UW     | `u`   | **U**BER      | b**oO**St | t**oO**                     |
| Ah     | `ʌ`   | **u**NCLE     | c**u**t   |                             |
| Ay     | `aɪ`  | **i**CE       | b**i**  | FL**y**                     |
| AW     | `aʊ`  | **unità organizzativa**t       | **unità organizzativa**s | c**ome**                     |
| Oy (     | `ɔɪ`  | **OI**l       | j**OI**n  | t**Oy**                     |
| UW y   | `ju`  | **Yu**ma      | h**u**Man | f**EW**                     |
| ax     | `ə`   | **Vai**       | WOM**a**n | sono**a**                    |

### <a name="english-r-colored-vowels"></a>Vocali in inglese R colorate

| `sapi` | `ipa` | Esempio 1    | Esempio 2      | Esempio 3  |
|--------|-------|--------------|----------------|------------|
| IH r   | `ɪɹ`  | **Ear**s     | t**IR**amis   | n**Ear**   |
| eh r   | `ɛɹ`  | **piano aereo** | ently**AR**app | SC**AR**e  |
| Sì r   | `ʊɹ`  |              |                | c**il**e   |
| Ay r   | `aɪɹ` | Land **ire**  | uisci**IR**f  | **oir** ch  |
| AW r   | `aʊɹ` | **ora**s    | p**ower**ful   | s**our**   |
| Ao r   | `ɔɹ`  | **o**Ange   | m**o**al      | **ramo** s   |
| r AA   | `ɑɹ`  | TIST **AR**   | St**AR**t      | c**AR**    |
| er r   | `ɝ`   | **orecchio**    | d**IR**       | f**Your**    |
| AX r   | `ɚ`   |              | tutte le**er**GY    | Supp**er** |

### <a name="english-semivowels"></a>Semivocali inglese

| `sapi` | `ipa` | Esempio 1           | Esempio 2  | Esempio 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**Ith, s**UE**de | al**w**AYS |           |
| y      | `j`   | **y**ARD, f**e**w   | on**i**  |           |

### <a name="english-aspirated-oral-stops"></a>Arresti orali aspirati (Inglese)

| `sapi` | `ipa` | Esempio 1 | Esempio 2   | Esempio 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**UT   | ha**PP**en  | **p** FLA   |
| b      | `b`   | **b**IG   | num**b**er  | CRA**b**   |
| t      | `t`   | **t**ALK  | capi**t**al | sough**t** |
| d      | `d`   | **d**IG   | esecuzione**d**om  | ro**d**    |
| k      | `k`   | **c**UT   | SLA**CK**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | DRA**g**   |

### <a name="english-nasal-stops"></a>Arresti nasali inglesi

| `sapi` | `ipa` | Esempio 1        | Esempio 2  | Esempio 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**at, Smash   | era CA**m** | Roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | pulcino**n** |
| ng     | `ŋ`   |                  | li**n**k   | **ing**    |

### <a name="english-fricatives"></a>Fricative inglese

| `sapi` | `ipa` | Esempio 1   | Esempio 2        | Esempio 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ORK    | le**f**t         | Hal**f**   |
| v      | `v`   | **v**alore   | e**v**ENT        | lo**v**e   |
| th     | `θ`   | **°**in    | APEM**°**y      | lun**°**  |
| DH     | `ð`   | **°**en    | mo**er**       | Smoo**th** |
| s      | `s`   | **s**it     | ri**s**k         | fact**s**  |
| z      | `z`   | AP **z**     | Bu**s**y         | figlio**s**   |
| sh     | `ʃ`   | **sh** e    | abbrevia**ti**on | **sh** ur   |
| zh     | `ʒ`   | **J**acques | Ure**s**     | gara**g**e |
| h      | `h`   | **h**ELP    | en**h**      | a-**h**a!  |

### <a name="english-affricates"></a>Affricate inglese

| `sapi` | `ipa` | Esempio 1 | Esempio 2    | Esempio 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**in  | stato**t**Ure   | **ch** atta |
| Jh     | `dʒ`  | **j**Oy   | ori**g**inale | Oran**g**e |

### <a name="english-approximants"></a>Approssimanti inglese

| `sapi` | `ipa` | Esempio 1          | Esempio 2  | Esempio 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**ID, g**l**ad  | PA**l**ACE | Chi**ll** |
| r      | `ɹ`   | **r**ed, b**r** | ome Bo**RR** | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Suprasegmentals francese

Il set di telefoni del servizio di riconoscimento vocale pone tuttavia stress dopo la vocale della sillaba accentata. il `fr-FR` set di telefono del servizio voce non supporta il sottostress IPA ' ˌ '. Se è necessaria la sottolineatura IPA, è consigliabile usare direttamente il pacchetto IPA.

### <a name="french-vowels"></a>Vocali francesi

| `sapi` | `ipa` | Esempio 1     | Esempio 2       | Esempio 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **rbre**     | p**a**TTE       | IR**a**   |
| aa     | `ɑ`   |               | p**â**te        | p**s**   |
| AA ~   | `ɑ̃`  | **en**fant    | ENF**en**t      | t**em**PS |
| ax     | `ə`   |               | p**e**Tite      | l**e**    |
| eh     | `ɛ`   | **e**lle      | RDU p**e**       | **ai**t |
| eu     | `ø`   | **œu**FS      | CR**eu**ser     | qu**eu**  |
| chiave     | `e`   | ému           | crétin          | ôté       |
| eh ~   | `ɛ̃`  | portant **im** | p**ein**tura    | Mat**in** |
| IY     | `i`   | **Dée**      | PET**i**te      | **i**   |
| OE     | `œ`   | **œu**f       | p**eu**r        |           |
| Oh     | `ɔ`   | **o**bstacle  | RPS c**o**       |           |
| Oh ~   | `ɔ̃`  | **in**ze      | r**su**deur     | b**on**   |
| ow     | `o`   | diteur **au**  | b**Eau**coup    | p**ô**    |
| OE ~   | `œ̃ ` | **non**        | l**annullamento**di       | BR**un**  |
| UW     | `u`   | trage **ou**   | intr**ou**in | **unità organizzativa**    |
| uy     | `y`   | **u**ne       | NIR p**u**       | él**u**   |

### <a name="french-consonants"></a>Consonanti francesi

| `sapi` | `ipa` | Esempio 1   | Esempio 2     | Esempio 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**Ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | Ron**d**EUR   | Chau**d**e                       |
| f      | `f`   | **f**Emme   | IXE su**FF**   | Bo**f**                          |
| g      | `g`   | **g**Auche  | è**g**ale     | **GU**BA e                        |
| ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)Park**ing** |
| HY     | `ɥ`   | h**u**Ile   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | **c**Aille   | **c**                          |
| l      | `l`   | **l**ONG    | è**l**ire     | BA**l**                          |
| m      | `m`   | **m**Adam  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **unità**organizzative    | te**n**IR     | Bo**nn**e                        |
| NJ     | `ɲ`   |             |               | Pei**GN**e                       |
| p      | `p`   | **p**atte   | ri**p**come     | CA**p**                          |
| r      | `ʁ`   | da **r**a     | Cha**r**   | inviato**r**                       |
| s      | `s`   | Ourir **s**  | **SS**EZ     | PA**SS**e                        |
| sh     | `ʃ`   | Anter **ch** | ine**ch**   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ENT    | in**v**invio  | Rê**v**e                         |
| w      | `w`   | **unità organizzativa**i     | **unità organizzativa**f    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | Marsa**Ille**                    |
| z      | `z`   | * * z * * éro   | Rai**s**onner | ro**s**e                         |
| zh     | `ʒ`   | **j**Ardin  | uomo**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | Quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *solo per alcune parole straniere.*

> [!TIP]
> Il `fr-FR` set di telefono del servizio voce non supporta i Liasions francesi `n‿`seguenti `t‿`,, `z‿`e. Se necessario, è consigliabile usare direttamente il pacchetto IPA.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Suprasegmentals tedesco

| Esempio 1 (inizio per consonante, parola iniziale per la vocale) | Esempio 2 (intervocale per consonante, parola mediana nucleo per vocale) | Esempio 3 (coda per consonante, parola finale per la vocale) | Commenti |
|--|--|--|--|
| Anders/a **1** n-d AX r s/ | Multiplikationszeichen/m Ah l-t iy-p l iy-k a-TS y ow **1** n s-TS ay-c n/ | Biologie/b iy-ow-l ow-g iy **1**/ | Set di telefono del servizio di riconoscimento vocale-stress dopo la vocale della sillaba accentata |
| Allgemeinwissen/a **2** l-g AX-m ay 1 n-v IH-s n/ | Abfallentsorgungsfirma/a 1 p-f a l-^ eh n t-z Oh **2** AX r-g. ng s-f IH AX r-m a/ | Computertomographie/k Oh m-p y Uw 1-t AX r-t ow-m ow-g r a-f iy **2**/ | Set di telefono del servizio di riconoscimento vocale-stress dopo la vocale della sillaba sottoposta a sottolineatura |

### <a name="german-vowels"></a>Vocali tedesche

| `sapi` | `ipa`     | Esempio 1                             | Esempio 2     | Esempio 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **Un**BER                              | Maßst**a**b   | Trama**a**                         |
| a      | `a`       | **Bfall**                            | B**a**ch      | Agath**a**                         |
| Oh     | `ɔ`       | **O**Sten                             | PF**o**Sten   |                                    |
| eh    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1</sup>](#de-v-1) Fascio**AE** |
| eh     | `ɛ`       | **ä**ndern                            | ProZ**e**NT   | Amygdal**AE**                      |
| ax     | `ə`       | [<sup>2</sup>](#de-v-2)' v**e**rstauen | Aach**e**n    | Frammenti**e**                          |
| IY     | `iː`      | **Ho**eseguito                              | ABB**IE**gt   | **IE** Relativitätstheor            |
| IH     | `ɪ`       | **Nnung**                            | s**i**Ngen    | Legno**y**                          |
| eu     | `øː`      | **Ö**Sen                              | ABL**ö**Sten  | Morigi**ö**                          |
| ow     | `o`, `oː` | **o**hnE                              | Non esitare**o**n    | Trept**ow**                        |
| OE     | `œ`       | **Ö**ffnung                           | pri**ö**rdern |                                    |
| chiave     | `e`, `eː` | **E**Berhard                          | ABF**e**gt    | b                                  |
| UW     | `uː`      | **U**do                               | H**u**t       | AKK**u**                           |
| Uh     | `ʊ`       | **U**nterschiedes                     | b**u**NT      |                                    |
| UE     | `yː`      | **Ü**bermut                           | PFL**ü**gt    | Uomini**ü**                           |
| uy     | `ʏ`       | **ü**ppig                             | Stem S**y**    |                                    |

<a id="de-v-1"></a>
**1** *solo in parole di origine esterna, ad esempio: fascio**AE**.*<br>
<a id="de-v-2"></a>
**2** parole *, in modo iniziale solo in parole di origine esterna, ad esempio **un**ppointment. Sillaba-inizialmente in:' v**e**rstauen.*

### <a name="german-diphthong"></a>Dittongo tedesco

| `sapi` | `ipa`       | Esempio 1    | Esempio 2          | Esempio 3 |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | NSAM **ei**   | Unabhängigk**ei**t | CA**ei** |
| AW     | `au`        | ßen **au**    | ABB**au**St        | St**au**  |
| Oy (     | `ɔy`, `ɔʏ̯` | Phorie **UE** | mt**äu**TR         | **UE** SCH |

### <a name="german-semivowels"></a>Semivocali tedesco

| `sapi` | `ipa` | Esempio 1 | Esempio 2    | Esempio 3  |
|--------|-------|-----------|--------------|------------|
| AX r   | `ɐ`   |           | abänd**er**n | blocca**er** |

### <a name="german-consonants"></a>Consonanti tedesche

| `sapi` | `ipa` | Esempio 1 | Esempio 2 | Esempio 3 |
|--|--|--|--|--|
| b | `b` | **B**. |  | [<sup>1</sup>](#de-c-1) Pu**b** |  |
| c | `ç` | Ernie **ch** | mögli**ch**St | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**Anken | [<sup>3</sup>](#de-c-3) Len**d**l | [<sup>4</sup>](#de-c-4) Clau**d**e |  |
| Jh | `ʤ` | **J**eff | Gemana**g**t | [<sup>5</sup>](#de-c-5) Chan**g**e |
| f | `f` | **F**ahrtdauer | slustig Angri**FF** | abbruchrei**f** |  |
| g | `g` | **g**UT |  | [<sup>6</sup>](#de-c-6) GRE**g** |  |
| h | `h` | Ausanbau **H** |  |  |  |
| y | `j` | **J**od | Reakt**i** | Hu**i** |  |
| k | `k` | OMA **K** | Aspe**k**t | FLEC**k** |  |
| l | `l` | **l**au | ähne**l**n | zuvie**l** |  |
| m | `m` | UT **M** | A**m**t | Leh**m** |  |
| n | `n` | **n**Annulla | u**n**d | OK**n** |  |
| ng | `ŋ` | [<sup>7</sup>](#de-c-7)**ng**Uyen | Schwa**NK** | R**ing** |  |
| p | `p` | **P**Artner | abru**p**t | Ti**p** |  |
| PF | `pf` | ERD di **PF** | Dam**PF**t | A**PF** |  |
| r | `ʀ`, `r`, `ʁ` | AItro **R** | KNU**RR**t | Haa**r** |  |
| s | `s` | taccato [<sup>8</sup>](#de-c-8)**S** | bi**s**t | mie**s** |  |
| sh | `ʃ` | Gola **SCH** | mi**SCH**t | Lappi**SCH** |  |
| t | `t` | **T**Raum | Raße S**t** | MU**t** |  |
| ts | `ts` | UG **Z** | AR**z**t | Wit**z** |  |
| ch | `tʃ` | Echien **tsch** | aufgepu**tsch**t | **tsch** bundesdeu |  |
| v | `v` | **w**INKEN | Domande**e**risposte | [<sup>9</sup>](#de-c-9) Gr**oO**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) Erach BA**ch** | Ma**ch**t mögli**ch**St | Schma**ch** ' i**ch** |
| z | `z` | uPer **s** |  |  |  |
| zh | `ʒ` | **G**ENRE | B**re**ezinski | EDVI**g**e |

<a id="de-c-1"></a>
**1** *solo in parole di origine esterna, ad esempio: PU**b**.*<br>
<a id="de-c-2"></a>
**2** *soft "ch" dopo "e" e "i"*<br>
<a id="de-c-3"></a>
**3** *solo in parole di origine esterna, ad esempio: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *solo in parole di origine esterna, ad esempio: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *solo in parole di origine esterna, ad esempio: Chan**g**e.*<br>
<a id="de-c-6"></a>
**6** *Word-Terminally solo in parole di origine esterna, ad esempio GRE**g**.*<br>
<a id="de-c-7"></a>
**7** *solo in parole di origine esterna, ad esempio: **ng**Uyen.*<br>
<a id="de-c-8"></a>
**8** *solo in parole di origine esterna, ad esempio: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *solo in parole di origine esterna, ad esempio: gr**oO**ve.*<br>
<a id="de-c-10"></a>
**10** *l'IPA `x` è un "ch" rigido dopo tutte le vocali non frontali (a, AA, Oh, ow, ehm, UW e dittongo aw).*<br>
<a id="de-c-11"></a>
**11** *l'IPA `ç` è una "ch" morbida dopo le vocali anteriori (IH, IY, eh, AE, uy, UE, OE, eu anche in dittonghi Ay, Oy) e le consonanti*<br>
<a id="de-c-12"></a>
**12** *parole: inizialmente solo in parole di origine esterna, ad esempio **J**uan. Sillaba: inizialmente anche in parole come: BA**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Consonanti orali tedesche

| `sapi` | `ipa` | Esempio 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich/b AX-^ a 1 x t-l IH c/ |

> [!NOTE]
> È necessario aggiungere un [GS\] Phone tra due vocali distinte, ad eccezione del fatto che le due vocali sono una dittongo genuina. Questa consonante orale è un arresto glottal. per altre informazioni, <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">vedere glottal <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>stop.

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Vocali spagnole

| `sapi` | `ipa` | Esempio 1    | Esempio 2     | Esempio 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **un**valore LTO     | c**a**ntar    | CA**a**     |
| i      | `i`   | **bérica**  | AV**i**Spa    | imposta**i**     |
| e      | `e`   | **e**lefante | in**e**nPer    | elefante**e** |
| o      | `o`   | **o**caso    | ENC**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**NTA     | **U** juanl   |

### <a name="spanish-consonants"></a>Consonanti spagnole

| `sapi` | `ipa`      | Esempio 1  | Esempio 2      | Esempio 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | Bao**b**AB     | Baoba**b**     |
| ch     | `tʃ`       | eque **ch** | Co**ch**e      | Marraque**ch** |
| d      | `d`        | **d**Edo   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | Verda**d**     |
| f      | `f`        | **f**ácil  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | a**g**UA       | tuare**g**     |
| j      | `j`        | **Odo**   | Cal**i**   | ri**y**        |
| JJ     | `j.j` `jj` |            | vi**a**      |                |
| k      | `k`        | **c**oche  | Bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | **l**a        | cavo**l**     |
| ll     | `ʎ`        | **ll**Ave  | desarro**o** |                |
| m      | `m`        | ordine **m** | a**m**AR       | álbu**m**      |
| n      | `n`        | **n**Ada   | CE**n**a       | rató**n**      |
| NJ     | `ɲ`        | **ñ**Aña   | Ara**ñ**azo    |                |
| p      | `p`        | oca **p**   | a**p**o       | **p**       |
| r      | `ɾ`        |            | CA**r**a       | Abri**r**      |
| RR     | `r`        | **r**Adio  | Co**RR**e      | unità di elaborazione**RR**       |
| s      | `s`        | **s**ACO   | **s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | **t**AR       | Disque**t**    |
| th     | `θ`        | Ebra **z**  | UL**z**       | lápi**z**      |
| w      | `w`        | h**u**ESO  | AG**u**a       | gua**u**       |
| x      | `x`        | **j**OTA   | a**j**o        | trasfe**j**      |

> [!TIP]
> Il `es-ES` set di telefono del servizio voce non supporta il seguente IPA `β`spagnolo `ð`,, `ɣ`e. Se necessario, è consigliabile usare direttamente il pacchetto IPA.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Il telefono del servizio di riconoscimento `zh-CN` vocale per è basato sul set <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">pinyin <span class="docon docon-navigate-external x-hidden-focus"></span> </a> del telefono nativo.

### <a name="tone"></a>Tono

| Tono pinyin | `sapi` | Esempio di carattere |
|-------------|--------|-------------------|
| Mā          | ma 1  | 妈                 |
| má          | ma 2  | 麻                 |
| mǎ          | ma 3  | 马                 |
| Mà          | ma 4  | 骂                 |
| ma          | ma 5  | 嘛                 |

#### <a name="example"></a>Esempio

| Carattere | Servizio Voce                |
|-----------|-------------------------------|
| 组织关系      | zu 3-Zhi 1-Guan 1-XI 5 |
| 累进        | Lei 3-Jin 4                 |
| 西宅巷       | XI 1-Zhai 2-Xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

Il telefono del servizio di riconoscimento `zh-TW` vocale per è basato sul set <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">di <span class="docon docon-navigate-external x-hidden-focus"></span> Bopomofo</a> del telefono nativo.

### <a name="tone"></a>Tono

| Tono del servizio vocale | Tono Bopomofo | Esempio (Word) | Telefoni per servizi vocali | Bopomofo | Pinyin (拼音) |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | empty         | 偵              | ˉ ㄓㄣ                   | ㄓㄣ       | zhēn        |
| ˊ                   | ˊ             | 察              | ˊ ㄔㄚ                   | ˊ ㄔㄚ      | Chá         |
| ˇ                   | ˇ             | 打              | ㄉㄚ                   | ㄉㄚ      | Dǎ          |
| ˋ                   | ˋ             | 望              | ㄨㄤˋ                   | ㄨㄤˋ      | 爱与希望        |
| ˙                   | ˙             | 影子             | 一ㄥ ㄗ ̇               | 一ㄥ ㄗ ̇  | Yǐng Zi    |

#### <a name="example"></a>Esempio

| Carattere | `sapi`   |
|-----------|----------|
| 狗         | ㄍㄡ      |
| 然后        | ㄖㄢ ˊ ㄏㄡ ˋ   |
| 剪掉        | ㄐㄧㄢ ㄉㄧㄠ ˋ |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Il telefono del servizio di riconoscimento `ja-JP` vocale per è basato sul set <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana <span class="docon docon-navigate-external x-hidden-focus"></span> </a> del telefono nativo.

### <a name="stress"></a>Stress

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`mainstress |
| `+`    | `ˌ`sottolineatura  |

#### <a name="example"></a>Esempio

| Carattere | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | ゴ'ウセ    | go ˈ wɯseji   |
| 所有者       | ショュ'ウ?ャ | ɕjojɯ ˈ wɯɕja |
| 最適化       | サィテキカ +  | ˌ sajitecika |

***