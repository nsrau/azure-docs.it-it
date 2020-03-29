---
title: Pull Docker per il contenitore Sentiment Analysis
titleSuffix: Azure Cognitive Services
description: Comando pull Docker per il contenitore Analisi sentiment
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966776"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Pull Docker per il contenitore Sentiment Analysis

Utilizzare [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) il comando per scaricare un'immagine contenitore da Microsoft Container Registry.

Per una descrizione completa dei tag disponibili per i contenitori Analisi del testo, vedi il contenitore [Analisi del sentiment](https://go.microsoft.com/fwlink/?linkid=2018654) nell'Hub Docker.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
