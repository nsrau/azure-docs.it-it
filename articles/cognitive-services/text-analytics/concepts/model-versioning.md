---
title: Specificare la versione del modello in Analisi del testo V3
titleSuffix: Azure Cognitive Services
description: Informazioni su come specificare il modello di API Analisi del testo usato nei dati.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: 9431ff862dd987a1a806087053014e7c880bf801
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143304"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Controllo delle versioni dei modelli nella API Analisi del testo

La versione 3 della API Analisi del testo consente di scegliere la versione del modello che viene utilizzata sui dati. Usare il `model-version` parametro facoltativo per selezionare la versione del modello nelle richieste API. Ad esempio: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Se questo parametro non è specificato, l'API utilizzerà per impostazione predefinita la versione stabile più recente. 

## <a name="available-versions"></a>Versioni disponibili

Usare la tabella seguente per trovare le versioni del modello supportate da ogni endpoint.


| Endpoint                        | Versioni supportate                       | versione più recente |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


Per informazioni dettagliate sugli aggiornamenti per questi [modelli, vedere novità.](../whats-new.md)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento entità](../how-tos/text-analytics-how-to-entity-linking.md)