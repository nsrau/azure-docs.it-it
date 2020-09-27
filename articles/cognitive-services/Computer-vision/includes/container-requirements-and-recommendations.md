---
title: Indicazioni e requisiti per i contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397190"
---
> [!NOTE]
> I requisiti e le indicazioni sono basati sui benchmark con una singola richiesta al secondo, usando un'immagine di 8 MB di una lettera di business scansionata che contiene 29 righe e un totale di 803 caratteri.

La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore di lettura.

| Contenitore | Minima | Consigliato |
|-----------|---------|-------------|
| Leggi 2,0-Anteprima | 1 core, 8 GB di memoria |  8 core, 16 GB di memoria |
| Leggi 3,0-Anteprima | 8 core, 16 GB di memoria | 8 core, 24 GB di memoria |
| Leggi 3,1-anteprima | 8 core, 16 GB di memoria | 8 core, 24 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.
