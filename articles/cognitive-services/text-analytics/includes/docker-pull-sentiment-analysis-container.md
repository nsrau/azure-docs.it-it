---
title: Pull Docker per il contenitore di Analisi del sentiment
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Analisi del sentiment contenitore
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906034"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Pull Docker per il contenitore Analisi del sentiment V3

Il contenitore dell'analisi dei sentimenti V3 è disponibile in diverse lingue. Per scaricare il contenitore per il contenitore inglese, usare il comando seguente. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Per scaricare il contenitore per un'altra lingua, sostituire `en` con uno dei codici di lingua indicati di seguito. 

| Contenitore di Analisi del testo | Codice lingua |
|--|--|
| Inglese | `en` |
| Spagnolo | `es` |
| Francese | `fr` |
| Italiano | `it` |
| Tedesco | `de` |
| Cinese semplificato | `zh` |
| Cinese tradizionale | `zht` |
| Giapponese | `ja` |
| Portoghese | `pt` |
| Olandese | `nl` |

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere [Hub Docker](https://go.microsoft.com/fwlink/?linkid=2018654).