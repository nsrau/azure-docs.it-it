---
title: Pull Docker per il contenitore Sentiment Analysis
titleSuffix: Azure Cognitive Services
description: Comando pull Docker per il contenitore Analisi sentiment
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877069"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Pull Docker per il contenitore Sentiment Analysis

Utilizzare [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) il comando per scaricare un'immagine contenitore da Microsoft Container Registry.

Per una descrizione completa dei tag disponibili per i contenitori Analisi del testo, vedi il contenitore [Analisi del sentiment](https://go.microsoft.com/fwlink/?linkid=2018654) nell'Hub Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
