---
title: Eseguire il pull Docker per il contenitore Estrazione frase chiaveDocker pull for the Key Phrase Extraction container
titleSuffix: Azure Cognitive Services
description: Comando pull Docker per il contenitore Estrazione frase chiave
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966794"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Eseguire il pull Docker per il contenitore Estrazione frase chiaveDocker pull for the Key Phrase Extraction container

Utilizzare [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) il comando per scaricare un'immagine contenitore da Microsoft Container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il contenitore [Estrazione frase chiave](https://go.microsoft.com/fwlink/?linkid=2018757) nell'hub Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
