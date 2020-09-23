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
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906043"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pull Docker per il contenitore di Estrazione frasi chiave

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare un'immagine del contenitore da Microsoft container Registry.

Per una descrizione completa dei tag disponibili per i contenitori di Analisi del testo, vedere il [estrazione frasi chiave](https://go.microsoft.com/fwlink/?linkid=2018757) contenitore nell'hub docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
