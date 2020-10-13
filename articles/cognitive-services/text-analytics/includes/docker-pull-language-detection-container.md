---
title: Pull Docker per il contenitore di Rilevamento lingua
titleSuffix: Azure Cognitive Services
description: Comando pull di Docker per Rilevamento lingua contenitore
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 95bcb4b424010f63ac1ee4eb02f9e4793647051a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906078"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>Pull Docker per il contenitore di Rilevamento lingua

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [rilevamento lingua](https://go.microsoft.com/fwlink/?linkid=2018759) contenitore nell'hub docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```
