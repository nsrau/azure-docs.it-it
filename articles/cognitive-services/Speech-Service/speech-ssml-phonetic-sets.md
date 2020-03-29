---
title: Set fonetici vocali - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come l'alfabeto fonetico del servizio di riconoscimento vocale esegue il mapping all'alfabeto fonetico internazionale (IPA) e quando usare il set.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675336"
---
# <a name="speech-service-phonetic-sets"></a>Set fonetici del servizio di riconoscimento vocale

Il servizio di riconoscimento vocale definisce gli alfabeti fonetici ("set telefonici" in breve), costituiti da sette lingue; `en-US`, `fr-FR` `de-DE`, `es-ES` `ja-JP`, `zh-CN`, `zh-TW`, e . Il telefono del servizio di riconoscimento vocale imposta in genere il mapping <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">all'alfabeto fonetico internazionale (IPA, International Phonetic Alphabet) <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. I set di telefoni del servizio di riconoscimento vocale vengono utilizzati insieme al linguaggio [SSML (Speech Synthesis Markup Language),](speech-synthesis-markup.md)come parte dell'offerta del servizio di sintesi vocale. In questo articolo verrà illustrato come vengono mappati questi set di telefoni e quando usare l'impostazione del telefono.

# <a name="en-us"></a>[en-US](#tab/en-US)

### <a name="english-suprasegmentals"></a>Sovrasegmentali inglesi

| Esempio 1 (insetto per consonante, parola iniziale per vocale) | Esempio 2 (Intervocalic per consonante, parola mediale nucleus per vocale) | Esempio 3 (Coda per consonante, parola finale per vocale) | Commenti |
|--|--|--|--|
| burger /b er **1** r - g ax r/ | falafel /f ax - l aa **1** - f ax l/ | chitarra /g ih - t aa **1** r/ | Servizio vocale telefono impostare lo stress dopo la vocale della sillaba stressrata |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | dissimilarità /d ih - s ih **2**- m ax - l eh 1 - r ax - t iy/ | forza lavoro /w er 1 r k - f ao **2** r s/ | Servizio vocale telefono impostare lo stress dopo la vocale della sillaba sotto-stress |

### <a name="english-vowels"></a>Vocali inglesi

| `sapi` | `ipa` | Esempio 1     | Esempio 2 | Esempio 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| Iy     | `i`   | **ea**t       | f**ee**l  | vall**ey**                  |
| Ih     | `ɪ`   | **i**f        | f**i**ll  |                             |
| Ey     | `eɪ`  | **a**te       | g**a**te  | d**ay**                     |
| eh     | `ɛ`   | **e**molto     | p**e**t   | m**eh** (parola rara finalmente) |
| ae     | `æ`   | **un**ctive    | c**a**t   | n**ah** (parola rara finalmente) |
| aa     | `ɑ`   | **o bstinate** | p**o**ppy | r**ah** (parola rara finalmente) |
| Ao     | `ɔ`   | **o**gamma    | c**au**se | Ut**ah**                    |
| Uh     | `ʊ`   | b**oo**k      |           |                             |
| Ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| Uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| Ah     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| Ay     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| Aw     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| Oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**uomo | f**ew**                     |
| Ax     | `ə`   | **un**andare       | wom**a**n | sono**un**                    |

### <a name="english-r-colored-vowels"></a>Vocali inglesi color R

| `sapi` | `ipa` | Esempio 1    | Esempio 2      | Esempio 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **orecchio**s     | t**ir**amisu   | n**orecchio**   |
| eh r   | `ɛɹ`  | **aereo aereo** | app**ar**ently | sc**ar**e  |
| uh r   | `ʊɹ`  |              |                | c**il tuo**e   |
| ay r   | `aɪɹ` | **Terra d'irlanda**  | f**ir**eplace  | ch**oir**  |
| aw r   | `aʊɹ` | **ora**s    | p**ower**ful   | s**il nostro**   |
| ao r   | `ɔɹ`  | **o**ange   | m**o**al      | s**remo**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **orecchio**th    | b**ir**d       | f**il vostro**    |
| ax r   | `ɚ`   |              | tutti**er**gy    | supp**er** |

### <a name="english-semivowels"></a>Inglese Semivowels

| `sapi` | `ipa` | Esempio 1           | Esempio 2  | Esempio 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue**de | al**w**ays |           |
| y      | `j`   | **y**ard, f**e**w   | su**i**su  |           |

### <a name="english-aspirated-oral-stops"></a>Fermate orali aspirate in inglese

| `sapi` | `ipa` | Esempio 1 | Esempio 2   | Esempio 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | corse**d**om  | ro**d**    |
| k      | `k`   | **c**ut   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>Fermate nasali inglesi

| `sapi` | `ipa` | Esempio 1        | Esempio 2  | Esempio 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**a, distruggere   | ca**m**era | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | pulcino**n** |
| Ng     | `ŋ`   |                  | li**n**k   | s**ing**    |

### <a name="english-fricatives"></a>Fricatiinglesi

| `sapi` | `ipa` | Esempio 1   | Esempio 2        | Esempio 3  |
|--------|-------|-------------|------------------|------------|
| f      | `f`   | **f**ork    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| th     | `θ`   | **th**in    | empa**th**y      | mon**th**  |
| Dh     | `ð`   | **it**it    | mo**th**er       | smoo**th** |
| s      | `s`   | **s**it     | ri**s**k         | fatto**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | kid**s**   |
| sh     | `ʃ`   | **sh** e    | abbrevia**ti**su | ru**sh**   |
| zh     | `ʒ`   | **J**acque | plea**s**ure     | gara**g**e |
| h      | `h`   | **h**elp    | it**h**ance      | **a-h**a!  |

### <a name="english-affricates"></a>L'inglese affricats

| `sapi` | `ipa` | Esempio 1 | Esempio 2    | Esempio 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**in  | fu**t**ure   | atta**ch** |
| Jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g**e |

### <a name="english-approximants"></a>Approssimazioni inglesi

| `sapi` | `ipa` | Esempio 1          | Esempio 2  | Esempio 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**annuncio  | pa**l**asso | chi**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Sovrasegmentali francesi

Il set di telefono del servizio di riconoscimento vocale mette lo stress dopo la vocale della sillaba stressata, tuttavia; l'impostazione `fr-FR` del telefono del servizio di riconoscimento vocale non supporta la sottostress IPA ''.'. Se la sottostress IPA è necessaria, è necessario utilizzare direttamente l'IPA.

### <a name="french-vowels"></a>Vocali francesi

| `sapi` | `ipa` | Esempio 1     | Esempio 2       | Esempio 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **un**rbre     | p**un**t       | ir**a**   |
| aa     | `ɑ`   |               | p**te**        | p**a**s   |
| aa   | `ɑ̃`  | **it**fant    | enf**en**t      | t**em**ps |
| Ax     | `ə`   |               | p**e**tite      | l**e**    |
| eh     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| Ue     | `ø`   | **u**fs      | cr**eu**ser     | qu**eu**  |
| Ey     | `e`   | ému           | crétin          | - Té       |
| eh   | `ɛ̃`  | **im**portante | p**ein**ture    | stuoia**in** |
| Iy     | `i`   | **i**dée      | pet**i**te      | sono**io**   |
| Oe     | `œ`   | **u**f       | p**eu**r        |           |
| Oh     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| oh .   | `ɔ̃`  | **su**ze      | r**su**deur     | b**su**   |
| Ow     | `o`   | **au**diteur  | b**eau**coup    | p **.**    |
| oe   | `œ̃ ` | **un**        | l**un**di       | br**un**  |
| Uw     | `u`   | **ou**trage   | intr**ou**vable | **Ou**    |
| Uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>Consonanti francesi

| `sapi` | `ipa` | Esempio 1   | Esempio 2     | Esempio 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| f      | `f`   | **f**emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| Ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)parco**ing** |
| Hy     | `ɥ`   | h**u**ile   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | é**c**aille   | essere**c**                          |
| l      | `l`   | **l**ong    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**Adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| Nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | re**p**come     | ca**p**                          |
| r      | `ʁ`   | **r**presso     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | a**ss**ez     | pa**ss**e                        |
| sh     | `ʃ`   | **ch**anter | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**    | T**er**      | ne**t**                          |
| v      | `v`   | **v**ent    | in**v**entrare  | rè**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | Marsiglia**ille**                    |
| z      | `z`   | Z -z zero   | rai**s**onner | ro**s**e                         |
| zh     | `ʒ`   | **j**ardin  | uomo**g**er    | piè**g**e                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *Solo per alcune parole straniere.*

> [!TIP]
> `fr-FR` L'impostazione del telefono del servizio di riconoscimento `n‿`vocale `t‿`non `z‿`supporta le seguenti contenazioni in francese, , e . Se sono necessari, si dovrebbe considerare l'utilizzo diretto dell'IPA.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Sovrasegmentali tedeschi

| Esempio 1 (insetto per consonante, parola iniziale per vocale) | Esempio 2 (Intervocalic per consonante, parola mediale nucleus per vocale) | Esempio 3 (Coda per consonante, parola finale per vocale) | Commenti |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Multiplikationszeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologia /b iy - ow - l ow - g iy **1**/ | Servizio vocale telefono impostare lo stress dopo la vocale della sillaba stressrata |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - - eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | Servizio vocale telefono impostare lo stress dopo la vocale della sillaba sotto-stress |

### <a name="german-vowels"></a>Vocali tedesche

| `sapi` | `ipa`     | Esempio 1                             | Esempio 2     | Esempio 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| a:     | `aː`      | **Una**berla                              | Mast**a**b   | Schem**a**                         |
| a      | `a`       | **Un**fallo                            | B**a**ch      | Agath**a**                         |
| Oh     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| eh:    | `ɛː`      | **Ä**hnlichkeit                       | B **-** r       | [<sup>1 : il</sup>](#de-v-1) nome del Fasci**ae** |
| eh     | `ɛ`       | **-** ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| Ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e**                          |
| Iy     | `iː`      | **Ho**corso                              | abb**cioè**gt   | Relativt-tstheor**cioè**            |
| Ih     | `ɪ`       | **I**nnung                            | s**i**ngen    | Legno**y**                          |
| Ue     | `øː`      | **-sen**                              | abl**sten**  | Malm**ö**                          |
| Ow     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**ow**                        |
| Oe     | `œ`       | **ffnung**                           | bef **-** rdern |                                    |
| Ey     | `e`, `eː` | **E**berhard                          | abf**e**gt    | b                                  |
| Uw     | `uː`      | **U**do                               | H**u**t       | Akk**u**                           |
| Uh     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| ue ue     | `yː`      | **Bermut**(bermut)                           | pfl **-gt**    | Uomini **-**                           |
| Uy     | `ʏ`       | **Ppig**                             | S**y**stelo    |                                    |

<a id="de-v-1"></a>
**1** *Solo in parole di origine straniera, come: Fasci**ae**.*<br>
<a id="de-v-2"></a>
**2** *Parola-intially solo in parole di origine straniera come **Un**punpunto. Syllable-inizialmente in: 'v**e**rstauen.*

### <a name="german-diphthong"></a>Ditthong tedesco

| `sapi` | `ipa`       | Esempio 1    | Esempio 2          | Esempio 3 |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | **ei**nsam   | Unabhàngigk**ei**t | Abt**ei** |
| Aw     | `au`        | **au**en    | abb**au**st        | St**au**  |
| Oy     | `ɔy`, `ɔʏ̯` | **Eu**phorie | tr**u**mt         | sch**eu** |

### <a name="german-semivowels"></a>Semivini tedeschi

| `sapi` | `ipa` | Esempio 1 | Esempio 2    | Esempio 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | ab-nd**er**n | blocco**er** |

### <a name="german-consonants"></a>Consonanti tedesche

| `sapi` | `ipa` | Esempio 1 | Esempio 2 | Esempio 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1 : il</sup>](#de-c-1) nome del Pu**b** |  |
| c | `ç` | **Ch**emie | m'gli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3 (COM del</sup>](#de-c-3) nome Len**d**l | [<sup>4 DEL psu'</sup>](#de-c-4) Clau**d**e |  |
| Jh | `ʤ` | **J**eff | gemana**g**t | 5 Del numero [<sup>3(</sup>](#de-c-5) Chan**g**e |
| f | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut |  | 6 È [<sup>possibile:</sup>](#de-c-6) Gre**g** |  |
| h | `h` | **H**ausanbau |  |  |  |
| y | `j` | **J**od | Reakt**i**su | hu**i** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **l**au | hne**l**n | zuvie**l** |  |
| m | `m` | **M**ut | A**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | Huh**n** |  |
| Ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk** | R**ing** |  |
| p | `p` | **P**artner | abru**p**t | Ti**p** |  |
| Pf | `pf` | **Pf**erd | diga**pf**t | Al**pf** |  |
| r | `ʀ`, `r`, `ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**Saccato** | bi**s**t | mie**s** |  |
| sh | `ʃ` | **Sch**ule | mi**sch**t | lappi**sch** |  |
| t | `t` | **T**raum | S**t**raee | Mu**t** |  |
| ts | `ts` | **ug** | Ar**z**t | Wit**z** |  |
| ch | `tʃ` | **Tsch**echien | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | 9 (in [<sup>vie</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11</sup>](#de-c-11)`ç` | [<sup>12</sup>](#de-c-12) mila Ba**ch**erach | Ma**ch**t m'gli**ch st** | Schma**ch** 'i**ch** |
| z | `z` | **s**uper |  |  |  |
| zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g**e |

<a id="de-c-1"></a>
**1** *Solo in parole di origine straniera, come: Pu**b**.*<br>
<a id="de-c-2"></a>
**2** *Morbido "ch" dopo "e" e "i"*<br>
<a id="de-c-3"></a>
**3** *Solo in parole di origine straniera, come: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *Solo in parole di origine straniera come: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *Solo in parole di origine straniera come: Chan**g**e.*<br>
<a id="de-c-6"></a>
**6** *Parola-terminale solo in parole di origine straniera come Gre**g**.*<br>
<a id="de-c-7"></a>
**7** *Solo in parole di origine straniera come: **Ng**uyen.*<br>
<a id="de-c-8"></a>
**8** *Solo in parole di origine straniera come: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *Solo in parole di origine straniera, come: Gr**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *L'IPA `x` è un "ch" duro dopo tutte le vocali non anteriori (a, aa, oh, ow, uh, uw e il diphthong aw).*<br>
<a id="de-c-11"></a>
**11** *L'IPA `ç` è un morbido 'ch' dopo le vocali anteriori (ih, iy, eh, ae, uy, ue, oe, eu anche nei ditanto, oy) e consonanti*<br>
<a id="de-c-12"></a>
**12** *Parola-inizialmente solo in parole di origine straniera, come ad esempio: **J**uan. Syllable-inizialmente anche in parole come: Ba**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Consonanti orali tedesche

| `sapi` | `ipa` | Esempio 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich /b ax - 1 x t - l ih c/ |

> [!NOTE]
> Abbiamo bisogno di aggiungere\] un [gs telefono tra due vocali distinte, tranne le due vocali sono un vero diphthong. Questa consonante orale è un arresto glottale, per <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>ulteriori informazioni, vedere <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">arresto glottale .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Vocali spagnole

| `sapi` | `ipa` | Esempio 1    | Esempio 2     | Esempio 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **un**lto     | c**un**ntar    | cas**a**     |
| i      | `i`   | **i**bérica  | av**i**spa    | tassa**i**     |
| e      | `e`   | **e**lefante | presso**e**nto    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | ocasenc**o** |
| u      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>Consonanti spagnole

| `sapi` | `ipa`      | Esempio 1  | Esempio 2      | Esempio 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**eque | co**ch**e      | Marraque**ch** |
| d      | `d`        | **d**edo   |                | portlan**d**   |
|        | `ð`        |            | de**d d**o       | verda**d**     |
| f      | `f`        | **f**  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | d'pin**g**     |
|        | `ɣ`        |            | a**g**ua       | tuare**g**     |
| j      | `j`        | **i**odo   | cal**i**ente   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oche  | bo**c**a       | tit-ni**c**    |
| l      | `l`        | **l'piz**  | a**l**a        | corde**l**     |
| ll     | `ʎ`        | **ll**ave  | desarro**ll**o |                |
| m      | `m`        | **m**ordine | a**m**ar       | lbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rata**n**      |
| Nj     | `ɲ`        | **Aa**aa   | ara**azo**    |                |
| p      | `p`        | **p**oca   | a**p**o       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| Rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | a**t**ar       | disprezzo**t**    |
| th     | `θ`        | **z**ebra  | a**z**ul       | l'p**greco z**      |
| w      | `w`        | h**u**eso  | ag**u**a       | gua**u**       |
| x      | `x`        | **j**ota   | a**j**o        | relo**j**      |

> [!TIP]
> `es-ES` L'impostazione del telefono del servizio di `β`riconoscimento vocale non supporta i seguenti indirizzi IPA spagnoli, , `ð`e `ɣ`. Se sono necessari, si dovrebbe considerare l'utilizzo diretto dell'IPA.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

Il telefono del `zh-CN` servizio di riconoscimento vocale impostato per è basato sul set <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">Pinyin <span class="docon docon-navigate-external x-hidden-focus"></span> </a> del telefono nativo.

### <a name="tone"></a>Tono

| Tono Pinyin | `sapi` | Esempio di carattere |
|-------------|--------|-------------------|
| Mā          | ma 1  | <a0>T                 |
| Má          | ma 2  | <a0>T                 |
| m.          | ma 3  | <a0>T                 |
| Mà          | ma 4  | <a0>T                 |
| ma          | ma 5  | <a0>T                 |

#### <a name="example"></a>Esempio

| Carattere | Servizio Voce                |
|-----------|-------------------------------|
| I s.p.o.      | zu 3 - zhi 1 - guan 1 - xi 5 |
| , quindi il numero di persone        | lei 3 -jin 4                 |
| , quindi il numero di siè ad esempio,       | xi 1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

Il telefono del `zh-TW` servizio di riconoscimento vocale impostato per è basato sul telefono nativo set <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo. <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

### <a name="tone"></a>Tono

| Tono del servizio di riconoscimento vocale | Tono Bopomofo | Esempio (parola) | Telefoni del servizio di riconoscimento vocale | Bopomofo | Pinyin |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| <a0>T                   | empty         | <a0>T              | , quindi il numero di siè ad esempio,                   | , quindi il numero di persone       | zhàn        |
| <a0>T                   | <a0>T             | <a0>T              | , quindi il numero di siè ad esempio,                   | , quindi il numero di siè ad esempio,      | Chá         |
| Proprietà .                   | Proprietà .             | <a0>T              | I due stati di si.Com.                   | I due stati di si.Com.      | d'          |
| <a0>T                   | <a0>T             | <a0>T              | , quindi il numero di siè ad esempio,                   | , quindi il numero di siè ad esempio,      | wàng        |
| ˙                   | ˙             | , quindi il numero di persone             | I s.p.a.               | I s.p.a.  | y-ng zi    |

#### <a name="example"></a>Esempio

| Carattere | `sapi`   |
|-----------|----------|
| <a0>T         | I due stati di si.Com.      |
| , quindi il numero di persone        | E s.t. . . . . . . . . . . . . . . .   |
| , quindi il numero di persone        | E s.t. . . . . . . . . . . . . . . . . . . . . . |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

Il telefono del `ja-JP` servizio di riconoscimento vocale impostato per è basato sul set di telefono nativo <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana. <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

### <a name="stress"></a>Stress

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`mainstress |
| `+`    | `ˌ`substress  |

#### <a name="example"></a>Esempio

| Carattere | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| , quindi il numero di persone        | E IL numero di ADES    | Go-w'seji   |
| , quindi il numero di siè ad esempio,       | " ? | "joj' w'JA'JA |
| , quindi il numero di siè ad esempio,       | N. di z.  | sajitecika |

***