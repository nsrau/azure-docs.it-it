---
title: Indicazioni e requisiti per i contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006909"
---
> [!NOTE]
> I requisiti e le indicazioni sono basati su benchmark con una singola richiesta al secondo, usando un'immagine di 8 MB di una lettera commerciale digitalizzata che contiene 29 righe e un totale di 803 caratteri.

La tabella seguente descrive l'allocazione minima e consigliata di risorse per ogni contenitore Read.

| Contenitore | Minima | Implementazione consigliata |
|-----------|---------|-------------|
| Leggi 2,0-Anteprima | 1 core, 8 GB di memoria |  8 core, 16 GB di memoria |
| Leggi 3,2-anteprima | 8 core, 16 GB di memoria | 8 core, 24 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.
