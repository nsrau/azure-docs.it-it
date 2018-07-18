---
title: Lingue supportate nell'API Analisi del testo - Servizi cognitivi di Azure | Microsoft Docs
description: Elenco di lingue con supporto in disponibilità generale e in anteprima per le operazioni dell'API Analisi del testo. Si applica all'analisi del sentiment, all'estrazione frasi chiave e al rilevamento lingua.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 2d341cfaf261bea6367bb55dd5d322f419e22d34
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377809"
---
# <a name="supported-languages-in-the-text-analytics-api"></a>Lingue supportate nell'API Analisi del testo

Questo articolo spiega quali lingue sono supportate per le operazioni di analisi del sentiment, estrazione frasi chiave e rilevamento lingua.

## <a name="language-detection"></a>Rilevamento lingua

L'API Analisi del testo può rilevare fino a 120 lingue diverse. Rilevamento lingua restituisce la lingua in generale. Per la frase "I have a dog", ad esempio, restituisce `en` anziché `en-US`. L'unico caso particolare è rappresentato dalla lingua cinese, per la quale la funzionalità Rilevamento lingua restituisce `zh_CHS` o `zh_CHT`, se è in grado di determinare il tipo di scrittura dal testo fornito. In situazioni in cui non è possibile identificare il tipo di scrittura per un documento in cinese, restituirà semplicemente `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Analisi del sentiment, estrazione frasi chiave e collegamento delle entità

Per l'analisi del sentiment, l'estrazione frasi chiave e il collegamento delle entità, l'elenco delle lingue supportate è più selettivo, poiché gli analizzatori sono ottimizzati in base alle regole linguistiche di lingue aggiuntive.

## <a name="language-list-and-status"></a>Elenco e stato delle lingue

Il supporto di una lingua viene inizialmente implementato in anteprima e quindi promosso alla disponibilità a livello generale (GA), indipendentemente dalle altre lingue e dal servizio Analisi del testo nel suo complesso. È possibile che una lingua rimanga disponibile in anteprima, anche quando l'API Analisi del testo passa alla disponibilità a livello generale.

| Linguaggio    | Codice lingua | Valutazione | Frasi chiave | Collegamento delle entità |   Note  |
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
| Spagnolo     | `es`          | ✔        | ✔           |     |     |
| Svedese     | `sv`          | ✔ \*     | ✔           |             |     |
| Turco     | `tr`          | ✔ \*     |             |             |     |

\* indica che la lingua è supportata in anteprima

## <a name="see-also"></a>Vedere anche 

[Documentazione dei servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/)   
[Pagina del prodotto Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/)
