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
ms.date: 07/29/2020
ms.author: aahi
ms.openlocfilehash: b3cec783b43d0169a3d0d56ca040e1397701102e
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986169"
---
# <a name="text-analytics-api-v3-language-support"></a>Supporto del linguaggio API Analisi del testo V3 

> [!IMPORTANT]
> La versione 3. x del API Analisi del testo non è attualmente disponibile nelle aree seguenti: India centrale, Emirati Arabi Uniti settentrionali, Cina settentrionale 2 Cina orientale.


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
| Italiano               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Giapponese              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Coreano                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norvegese (Bokmål)   |     `no`      |     ✓      |            |                            |                    |
| Polacco                |     `pl`      |     ✓      |            |                            |                    |
| Portoghese (Portogallo) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | Accettato anche `pt` |
| Russo               |     `ru`      |     ✓      |            |                            |                    |
| Spagnolo               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Svedese               |     `sv`      |     ✓      |            |                            |                    |
| Turco               |     `tr`      |     ✓      |            |                            |                    |

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

Il API Analisi del testo è in grado di rilevare un'ampia gamma di linguaggi, varianti, dialetti e lingue regionali/culturali.  Rilevamento lingua restituisce la lingua in generale. Per la frase "I have a dog", ad esempio, restituisce `en` anziché `en-US`. L'unico caso particolare è rappresentato dalla lingua cinese, per la quale la funzionalità Rilevamento lingua restituisce `zh_CHS` o `zh_CHT`, se è in grado di determinare il tipo di scrittura dal testo fornito. In situazioni in cui non è possibile identificare il tipo di scrittura per un documento in cinese, restituirà semplicemente `zh`.

L'elenco esatto delle lingue per questa funzionalità non viene pubblicato, ma è in grado di rilevare un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali. 

Se si ha contenuto espresso in un lingua usata con minore frequenza, si può provare Rilevamento lingua per vedere se viene restituito un codice. La risposta per le lingue che non è possibile rilevare è `unknown`.

---

## <a name="see-also"></a>Vedere anche

* [Qual è la API Analisi del testo?](overview.md)   
