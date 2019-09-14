---
title: Pull Docker per il contenitore di Estrazione frasi chiave
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Estrazione frasi chiave contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966794"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pull Docker per il contenitore di Estrazione frasi chiave

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [estrazione frasi chiave](https://go.microsoft.com/fwlink/?linkid=2018757) contenitore nell'hub docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
