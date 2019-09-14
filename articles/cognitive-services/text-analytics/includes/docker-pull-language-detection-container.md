---
title: Pull Docker per il contenitore di Rilevamento lingua
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Rilevamento lingua contenitore
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966700"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Pull Docker per il contenitore di Rilevamento lingua

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [rilevamento lingua](https://go.microsoft.com/fwlink/?linkid=2018759) contenitore nell'hub docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
