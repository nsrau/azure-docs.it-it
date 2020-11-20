---
title: Supporto per la lingua - API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Ecco un elenco di linguaggi naturali supportati dall'API Analisi del testo. Questo articolo illustra le lingue supportate per ogni operazione.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: b90fe6b7e0937a9ac3b300e904085122610d5645
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957079"
---
# <a name="text-analytics-api-v3-language-support"></a>Supporto del linguaggio API Analisi del testo V3 

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]


#### <a name="sentiment-analysis"></a>[Analisi del sentiment](#tab/sentiment-analysis)

| Linguaggio              | Codice lingua | supporto V2 | supporto V3 | Avvio della versione del modello V3: |              Note |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Cinese semplificato    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | Accettato anche `zh` |
| Cinese tradizionale   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Danese               |     `da`      |     ✓      |            |                            |                    |
| Olandese                 |     `nl`      |     ✓      |            |                            |                    |
| Inglese               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finlandese               |     `fi`      |     ✓      |            |                            |                    |
| Francese                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Tedesco                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Greco                 |     `el`      |     ✓      |            |                            |                    |
| Hindi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Giapponese              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Polacco                |     `pl`      |     ✓      |            |                            |                    |
| Portoghese (Brasile)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | Accettato anche `pt` |
| Russo               |     `ru`      |     ✓      |            |                            |                    |
| Spagnolo               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Svedese               |     `sv`      |     ✓      |            |                            |                    |
| Turco               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Opinion Mining (versione 3.1-solo anteprima)

| Linguaggio              | Codice lingua | A partire dalla versione del modello V3: |              Note |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Inglese               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Riconoscimento di entità denominate (NER)](#tab/named-entity-recognition)

> [!NOTE]
> * NER V3 supporta attualmente solo le lingue inglese e spagnola. Se si chiama NER V3 con una lingua diversa, l'API restituirà i risultati v 2.1, purché la lingua sia supportata nella versione 2,1.
> * v 2.1 restituisce solo il set completo di entità disponibili per le lingue inglese, cinese semplificato, francese, tedesco e spagnolo.  Le entità "person", "location" e "Organization" vengono restituite per le altre lingue supportate.

| Linguaggio               | Codice lingua | supporto v 2.1 | supporto V3 | A partire dalla versione del modello V3: |       Note        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arabo                |     `ar`      |     ✓      |            |                                 |                    |
| Ceco                 |     `cs`      |     ✓      |            |                                 |                    |
| Cinese semplificato     |   `zh-hans`   |     ✓      |            |                                 | Accettato anche `zh` |
| Cinese tradizionale   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Danese                |     `da`      |     ✓      |            |                                 |                    |
| Olandese                 |     `nl`      |     ✓      |            |                                 |                    |
| Inglese                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finlandese               |     `fi`      |     ✓      |            |                                 |                    |
| Francese                 |     `fr`      |     ✓      |            |                                 |                    |
| Tedesco                 |     `de`      |     ✓      |            |                                 |                    |
| Ebraico                |     `he`      |     ✓      |            |                                 |                    |
| Ungherese             |     `hu`      |     ✓      |            |                                 |                    |
| Italiano               |     `it`      |     ✓      |            |                                 |                    |
| Giapponese              |     `ja`      |     ✓      |            |                                 |                    |
| Coreano                |     `ko`      |     ✓      |            |                                 |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓      |            |                                 | Accettato anche `nb` |
| Polacco                |     `pl`      |     ✓      |            |                                 |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓      |            |                                 | Accettato anche `pt` |
| Portoghese (Brasile)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Russo              |     `ru`      |     ✓      |            |                                 |                    |
| Spagnolo               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Svedese               |     `sv`      |     ✓      |            |                                 |                    |
| Turco               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Estrazione delle frasi chiave](#tab/key-phrase-extraction)

> [!NOTE]
> Le versioni del modello di Estrazione frasi chiave precedenti alla 2020-07-01 hanno un limite di 64 caratteri. Questo limite non è presente nelle versioni successive del modello.

| Linguaggio              | Codice lingua | supporto V2 | supporto V3 | Disponibile a partire dalla versione del modello V3: |       Note        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Olandese                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Inglese               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finlandese               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francese                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Tedesco                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Italiano               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Giapponese              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Coreano                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | Accettato anche `nb` |
| Polacco                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | Accettato anche `pt` |
| Portoghese (Brasile)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Russo               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spagnolo               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Svedese               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Collegamento di entità](#tab/entity-linking)

| Linguaggio | Codice lingua | supporto V2 | supporto V3 | Disponibile a partire dalla versione del modello V3: | Note |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Inglese  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spagnolo  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Rilevamento lingua](#tab/language-detection)

Il API Analisi del testo è in grado di rilevare un'ampia gamma di linguaggi, varianti, dialetti e lingue regionali/culturali e di restituire le lingue rilevate con il nome e il codice. Analisi del testo Rilevamento lingua parametri del codice lingua sono conformi allo standard [BCP-47](https://tools.ietf.org/html/bcp47) con la maggior parte di essi conformi agli identificatori [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) . 

Se si ha contenuto espresso in un lingua usata con minore frequenza, si può provare Rilevamento lingua per vedere se viene restituito un codice. La risposta per le lingue che non è possibile rilevare è `unknown`.

| Linguaggio | Codice lingua |  supporto V3 | Disponibile a partire dalla versione del modello V3: |
|:---------|:-------------:|:----------:|:-----------------------------------------:|
|Afrikaans|`af`|✓|    |
|Albanese|`sq`|✓|    |
|Arabo|`ar`|✓|    |
|Armeno|`hy`|✓|    |
|Basco|`eu`|✓|    |
|Bielorusso|`be`|✓|    |
|Bengalese|`bn`|✓|    |
|Bosniaco|`bs`|✓|2020-09-01|
|Bulgaro|`bg`|✓|    |
|Birmano|`my`|✓|    |
|Catalano, Valenziano|`ca`|✓|    |
|Khmer centrale|`km`|✓|    |
|Cinese|`zh`|✓|    |
|Cinese semplificato|`zh_chs`|✓|    |
|Cinese tradizionale|`zh_cht`|✓|    |
|Croato|`hr`|✓|    |
|Ceco|`cs`|✓|    |
|Danese|`da`|✓|    |
|Dari|`prs`|✓|2020-09-01|
|Divehi, Dhivehi, Maldiviano|`dv`|✓|    |
|Olandese, fiammingo|`nl`|✓|    |
|Inglese|`en`|✓|    |
|Esperanto|`eo`|✓|    |
|Estone|`et`|✓|    |
|Figiano|`fj`|✓|2020-09-01|
|Finlandese|`fi`|✓|    |
|Francese|`fr`|✓|    |
|Galiziano|`gl`|✓|    |
|Georgiano|`ka`|✓|    |
|Tedesco|`de`|✓|    |
|Greco|`el`|✓|    |
|Gujarati|`gu`|✓|    |
|Haitiano Creolo Haitiano|`ht`|✓|    |
|Ebraico|`he`|✓|    |
|Hindi|`hi`|✓|    |
|Hmong Daw|`mww`|✓|2020-09-01|
|Ungherese|`hu`|✓|    |
|Islandese|`is`|✓|    |
|Indonesiano|`id`|✓|    |
|Inuktitut|`iu`|✓|    |
|Irlandese|`ga`|✓|    |
|Italiano|`it`|✓|    |
|Giapponese|`ja`|✓|    |
|Kannada|`kn`|✓|    |
|Kazako|`kk`|✓|2020-09-01|
|Coreano|`ko`|✓|    |
|Curdo|`ku`|✓|    |
|Lao|`lo`|✓|    |
|Latino|`la`|✓|    |
|Lettone|`lv`|✓|    |
|Lituano|`lt`|✓|    |
|Macedone|`mk`|✓|    |
|Malgascio|`mg`|✓|2020-09-01|
|Malese|`ms`|✓|    |
|Malayalam|`ml`|✓|    |
|Maltese|`mt`|✓|    |
|Maori|`mi`|✓|2020-09-01|
|Marathi|`mr`|✓|2020-09-01|
|Norvegese|`no`|✓|    |
|Norvegese Nynorsk|`nn`|✓|    |
|Oriya|`or`|✓|    |
|Pashto, Pushto|`ps`|✓|    |
|Persiano|`fa`|✓|    |
|Polacco|`pl`|✓|    |
|Portoghese|`pt`|✓|    |
|Punjabi, Picchio|`pa`|✓|    |
|Querétaro Otomi|`otq`|✓|2020-09-01|
|Rumeno, moldavo, moldavo|`ro`|✓|    |
|Russo|`ru`|✓|    |
|Samoano|`sm`|✓|2020-09-01|
|Serbo|`sr`|✓|    |
|Singalese, singalese|`si`|✓|    |
|Slovacco|`sk`|✓|    |
|Sloveno|`sl`|✓|    |
|Somalo|`so`|✓|    |
|Spagnolo, castigliano|`es`|✓|    |
|Swahili|`sw`|✓|    |
|Svedese|`sv`|✓|    |
|Tagalog|`tl`|✓|    |
|Tahitiano|`ty`|✓|2020-09-01|
|Tamil|`ta`|✓|    |
|Telugu|`te`|✓|    |
|Thai|`th`|✓|    |
|Tongano|`to`|✓|2020-09-01|
|Turco|`tr`|✓|    |
|Ucraino|`uk`|✓|    |
|Urdu|`ur`|✓|    |
|Uzbeco|`uz`|✓|    |
|Vietnamita|`vi`|✓|    |
|Gallese|`cy`|✓|    |
|Yiddish|`yi`|✓|    |
|Yucatec Maya|`yua`|✓|    |


---


## <a name="see-also"></a>Vedere anche

* [Qual è la API Analisi del testo?](overview.md)   
