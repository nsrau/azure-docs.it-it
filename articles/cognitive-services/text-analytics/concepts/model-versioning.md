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
ms.openlocfilehash: c9c598d385768e604d9b42e3ea055629e6ae2866
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108550"
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

## <a name="text-analytics-for-health"></a>Analisi del testo per l'integrità

Il [analisi del testo per il contenitore di integrità](../how-tos/text-analytics-for-health.md) usa il controllo delle versioni del modello distinto rispetto agli endpoint API precedenti.  Si noti che è disponibile solo una versione del modello per ogni immagine del contenitore.

| Endpoint                        | Tag dell'immagine del contenitore                     | Versione del modello |
|---------------------------------|-----------------------------------------|---------------|
| `/domains/health`               | `1.1.012640001-amd64-preview`o versione più recente | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |




## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
