---
title: Pull Docker per il contenitore di Estrazione frasi chiave
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Estrazione frasi chiave contenitore
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877129"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pull Docker per il contenitore di Estrazione frasi chiave

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [estrazione frasi chiave](https://go.microsoft.com/fwlink/?linkid=2018757) contenitore nell'hub docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
