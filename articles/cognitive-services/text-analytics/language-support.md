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
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 69c14c6b98b572bc413f5a35696269e13344387e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66417321"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Supporto lingua e area geografica per l'API Analisi del testo

Questo articolo spiega in quali lingue sono supportate per ogni operazione: analisi del sentiment, estrazione frasi chiave, il rilevamento della lingua e riconoscimento di entità denominate.

## <a name="language-detection"></a>Rilevamento lingua

L'API di Analitica di testo può rilevare un'ampia gamma di linguaggi, varianti, dialetti e alcune lingue internazionali/culturali.  Rilevamento lingua restituisce la lingua in generale. Per la frase "I have a dog", ad esempio, restituisce `en` anziché `en-US`. L'unico caso particolare è rappresentato dalla lingua cinese, per la quale la funzionalità Rilevamento lingua restituisce `zh_CHS` o `zh_CHT`, se è in grado di determinare il tipo di scrittura dal testo fornito. In situazioni in cui non è possibile identificare il tipo di scrittura per un documento in cinese, restituirà semplicemente `zh`.

L'elenco esatto delle lingue per questa funzionalità non viene pubblicato, ma è in grado di rilevare un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali. 

Se si ha contenuto espresso in un lingua usata con minore frequenza, si può provare Rilevamento lingua per vedere se viene restituito un codice. La risposta per le lingue che non è possibile rilevare è `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analisi del sentiment, estrazione frasi chiave e riconoscimento di entità denominate

Per l'analisi del sentiment, l'estrazione delle frasi chiave e il riconoscimento delle entità, l'elenco delle lingue supportate è più selettivo, poiché gli analizzatori sono ottimizzati in base alle regole linguistiche di lingue aggiuntive.

## <a name="language-list-and-status"></a>Elenco e stato delle lingue

Il supporto di una lingua viene inizialmente implementato in anteprima e quindi promosso alla disponibilità a livello generale (GA), indipendentemente dalle altre lingue e dal servizio Analisi del testo nel suo complesso. È possibile che una lingua rimanga disponibile in anteprima, anche quando l'API Analisi del testo passa alla disponibilità a livello generale.

| Linguaggio    | Codice lingua | Valutazione | Frasi chiave | Riconoscimento di entità denominate |   Note  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabo      | `ar`          |           |             | ✔ \*                     | |
| Ceco       | `cs`          |           |             | ✔ \*                     | |
| Cinese semplificato | `zh-CN`|           |             | ✔ \*        |    |
| Danese      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Olandese       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Inglese     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Finlandese     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Francese      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| Tedesco      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| Greco       | `el`          | ✔ \*     |             |            |     |
| Ungherese   | `hu`          |           |             |  ✔ \*          |     | 
| Italiano     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Giapponese    | `ja`          |          | ✔           |  ✔ \*          |     |
| Coreano      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norvegese (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polacco      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portoghese (Portogallo) | `pt-PT`| ✔        |  ✔          | ✔ \*      |Accettato anche `pt`|
| Portoghese (Brasile)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Russo     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Spagnolo     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| Svedese     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turco     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Supporto del linguaggio è disponibile in anteprima

\*\* Riconoscimento di entità denominato e [collegamento di entità](how-tos/text-analytics-how-to-entity-linking.md) sono entrambi disponibili per questa lingua.    

## <a name="see-also"></a>Vedere anche

[Documentazione dei servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/)   
[Pagina del prodotto Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/)
