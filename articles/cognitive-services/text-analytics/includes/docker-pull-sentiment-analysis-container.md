---
title: Pull Docker per il contenitore di Analisi del sentiment
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Analisi del sentiment contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966776"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Pull Docker per il contenitore di Analisi del sentiment

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [analisi del sentiment](https://go.microsoft.com/fwlink/?linkid=2018654) contenitore nell'hub docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
