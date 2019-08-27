---
title: Pull Docker per il contenitore di Analisi del sentiment
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Analisi del sentiment contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051314"
---
## <a name="pull-the-sentiment-analysis-container"></a>Estrarre il contenitore Analisi del sentiment

Le immagini del contenitore per Analisi del testo sono disponibili in Microsoft Container Registry.

| Contenitore | Container Registry/repository/nome dell'immagine |
|-----------|------------|
| Analisi del sentiment | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [analisi del sentiment](https://go.microsoft.com/fwlink/?linkid=2018654) contenitore nell'hub docker.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Pull Docker per il contenitore di Analisi del sentiment

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
