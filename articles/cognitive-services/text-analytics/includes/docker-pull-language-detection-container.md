---
title: Pull Docker per il contenitore di Rilevamento lingua
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Rilevamento lingua contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051400"
---
## <a name="pull-the-language-detection-container"></a>Estrarre il contenitore Rilevamento lingua

Le immagini del contenitore per Analisi del testo sono disponibili in Microsoft Container Registry.

| Contenitore | Container Registry/repository/nome dell'immagine |
|-----------|------------|
| Rilevamento lingua | `mcr.microsoft.com/azure-cognitive-services/language` |

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [rilevamento lingua](https://go.microsoft.com/fwlink/?linkid=2018759) contenitore nell'hub docker.


### <a name="docker-pull-for-the-language-detection-container"></a>Pull Docker per il contenitore di Rilevamento lingua

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
