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
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588393"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Pull Docker per il contenitore Analisi del sentiment V3

Il contenitore dell'analisi dei sentimenti V3 è disponibile in diverse lingue. Per scaricare il contenitore per il contenitore inglese, usare il comando seguente. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
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