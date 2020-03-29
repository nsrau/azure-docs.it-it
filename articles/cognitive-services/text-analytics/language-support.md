---
title: Supporto per la lingua - API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Ecco un elenco di linguaggi naturali supportati dall'API Analisi del testo. Questo articolo spiega quali lingue sono supportate per le operazioni di analisi del sentiment, estrazione delle frasi chiave, rilevamento della lingua e riconoscimento delle entità.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219280"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Supporto lingua e area geografica per l'API Analisi del testo

Questo articolo spiega quali lingue sono supportate per ogni operazione: analisi del sentiment, estrazione di frasi chiave, rilevamento della lingua e riconoscimento di entità denominate.

## <a name="language-detection"></a>Rilevamento lingua

L'API Analisi del testo è in grado di rilevare un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali.  Rilevamento lingua restituisce la lingua in generale. Per la frase "I have a dog", ad esempio, restituisce `en` anziché `en-US`. L'unico caso particolare è rappresentato dalla lingua cinese, per la quale la funzionalità Rilevamento lingua restituisce `zh_CHS` o `zh_CHT`, se è in grado di determinare il tipo di scrittura dal testo fornito. In situazioni in cui non è possibile identificare il tipo di scrittura per un documento in cinese, restituirà semplicemente `zh`.

L'elenco esatto delle lingue per questa funzionalità non viene pubblicato, ma è in grado di rilevare un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali. 

Se si ha contenuto espresso in un lingua usata con minore frequenza, si può provare Rilevamento lingua per vedere se viene restituito un codice. La risposta per le lingue che non è possibile rilevare è `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analisi del sentiment, estrazione di frasi chiave e riconoscimento di entità denominateSentiment Analysis, Key Phrase Extraction, and Named Entity Recognition

Per l'analisi del sentiment, l'estrazione delle frasi chiave e il riconoscimento delle entità, l'elenco delle lingue supportate è più selettivo, poiché gli analizzatori sono ottimizzati in base alle regole linguistiche di lingue aggiuntive. In Named Entity Recognition v2, il supporto per il set completo di tipi di [entità](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) è attualmente limitato alle seguenti lingue: 
* Inglese
* Cinese semplificato
* Francese
* Tedesco
* Spagnolo

Vengono `Person`restituite solo le entità , `Location` e `Organization` denominate per le altre lingue.

## <a name="language-list-and-status"></a>Elenco e stato delle lingue

Il supporto di una lingua viene inizialmente implementato in anteprima e quindi promosso alla disponibilità a livello generale (GA), indipendentemente dalle altre lingue e dal servizio Analisi del testo nel suo complesso. È possibile che una lingua rimanga disponibile in anteprima, anche quando l'API Analisi del testo passa alla disponibilità a livello generale.

> [!NOTE]
> Per il supporto dettagliato del linguaggio per l'anteprima pubblica di Named Entity Recognition (NER) v3, vedere [Named entity types](named-entity-types.md).

| Linguaggio              | Codice lingua | Valutazione | Frasi chiave | Riconoscimento di entità denominate | Collegamento di entità |       Note        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabo                |     `ar`      |           |             |           ✔\*           |                |                    |
| Ceco                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Cinese semplificato    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | Accettato anche `zh`                   |
| Cinese tradizionale   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| Danese                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Olandese                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Inglese               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| Finlandese               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Francese                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Tedesco                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Greco                 |     `el`      |   ✔\*    |             |                          |                |                    |
| Ungherese             |     `hu`      |           |             |           ✔\*           |                |                    |
| Italiano               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Giapponese              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Coreano                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Norvegese (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | Accettato anche `nb`                   |
| Polacco                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Portoghese (Portogallo) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | Accettato anche `pt` |
| Portoghese (Brasile)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Russo               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Spagnolo               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Svedese               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Turco               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*Il supporto linguistico è in anteprima

\*- Disponibile anche nelle anteprime pubbliche [sentiment Analysis v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) e/o [Named Entity Recognition v3.](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

## <a name="see-also"></a>Vedere anche

[Pagina della documentazione di Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/)   
[Pagina del prodotto Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/)
