---
title: Pull Docker per il contenitore di Estrazione frasi chiave
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Estrazione frasi chiave contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051399"
---
## <a name="pull-the-key-phrase-extraction-container"></a>Estrarre il contenitore Estrazione frasi chiave

Le immagini del contenitore per Analisi del testo sono disponibili in Microsoft Container Registry.

| Contenitore | Container Registry/repository/nome dell'immagine |
|-----------|------------|
| Estrazione frasi chiave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [estrazione frasi chiave](https://go.microsoft.com/fwlink/?linkid=2018757) contenitore nell'hub docker.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pull Docker per il contenitore di Estrazione frasi chiave

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
