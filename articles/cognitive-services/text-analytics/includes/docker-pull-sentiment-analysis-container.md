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
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877069"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Pull Docker per il contenitore di Analisi del sentiment

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [analisi del sentiment](https://go.microsoft.com/fwlink/?linkid=2018654) contenitore nell'hub docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
