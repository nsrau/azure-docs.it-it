---
title: Supporto per la lingua - API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Ecco un elenco di linguaggi naturali supportati dall'API Analisi del testo. Questo articolo spiega quali lingue sono supportate per le operazioni di analisi del sentiment, estrazione delle frasi chiave, rilevamento della lingua e riconoscimento delle entità.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: 63c8ebe8eda049a770cecfd445757e4b5a2de1ea
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067042"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Supporto lingua e area geografica per l'API Analisi del testo

Questo articolo spiega quali lingue sono supportate per le operazioni di analisi del sentiment, estrazione frasi chiave e rilevamento lingua.

## <a name="language-detection"></a>Rilevamento lingua

L'API Analisi del testo può rilevare fino a 120 lingue diverse. Rilevamento lingua restituisce la lingua in generale. Per la frase "I have a dog", ad esempio, restituisce `en` anziché `en-US`. L'unico caso particolare è rappresentato dalla lingua cinese, per la quale la funzionalità Rilevamento lingua restituisce `zh_CHS` o `zh_CHT`, se è in grado di determinare il tipo di scrittura dal testo fornito. In situazioni in cui non è possibile identificare il tipo di scrittura per un documento in cinese, restituirà semplicemente `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-recognition"></a>Analisi del sentiment, Estrazione frasi chiave e riconoscimento delle entità

Per l'analisi del sentiment, l'estrazione delle frasi chiave e il riconoscimento delle entità, l'elenco delle lingue supportate è più selettivo, poiché gli analizzatori sono ottimizzati in base alle regole linguistiche di lingue aggiuntive.

## <a name="language-list-and-status"></a>Elenco e stato delle lingue

Il supporto di una lingua viene inizialmente implementato in anteprima e quindi promosso alla disponibilità a livello generale (GA), indipendentemente dalle altre lingue e dal servizio Analisi del testo nel suo complesso. È possibile che una lingua rimanga disponibile in anteprima, anche quando l'API Analisi del testo passa alla disponibilità a livello generale.

| Linguaggio    | Codice lingua | Valutazione | Frasi chiave | Riconoscimento delle entità |   Note  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Danese      | `da`          | ✔ \*     | ✔           |             |     |
| Olandese       | `nl`          | ✔ \*     | ✔          |             |     |
| Inglese     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| Finlandese     | `fi`          | ✔ \*     | ✔           |             |     |
| Francese      | `fr`          | ✔        | ✔           |             |     |
| Tedesco      | `de`          | ✔ \*     | ✔           |            |     |
| Greco       | `el`          | ✔ \*     |             |            |     |
| Italiano     | `it`          | ✔ \*     | ✔           |             |     |
| Giapponese    | `ja`          |          | ✔           |            |     |
| Coreano      | `ko`          |          | ✔           |            |     |
| Norvegese (Bokmål) | `no`          | ✔ \*     |  ✔          |             |     |
| Polacco      | `pl`          | ✔ \*     |  ✔          |             |     |
| Portoghese (Portogallo) | `pt-PT`| ✔        |  ✔          |       |Accettato anche `pt`|
| Portoghese (Brasile)   | `pt-BR`|          |  ✔   |         |     |
| Russo     | `ru`          | ✔ \*     | ✔           |             |     |
| Spagnolo     | `es`          | ✔        | ✔           |   ✔ \*\*      |     |
| Svedese     | `sv`          | ✔ \*     | ✔           |             |     |
| Turco     | `tr`          | ✔ \*     |             |             |  |

\* indica che la lingua è supportata in anteprima

\*\* L'estrazione delle entità per lo spagnolo è disponibile solo in [(versione 2.1 anteprima)](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)

## <a name="see-also"></a>Vedere anche 

[Documentazione dei servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/)   
[Pagina del prodotto Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/)
