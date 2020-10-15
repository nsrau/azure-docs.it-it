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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397190"
---
> [!NOTE]
> I requisiti e le indicazioni sono basati su benchmark con una singola richiesta al secondo, usando un'immagine di 8 MB di una lettera commerciale digitalizzata che contiene 29 righe e un totale di 803 caratteri.

La tabella seguente descrive l'allocazione minima e consigliata di risorse per ogni contenitore Read.

| Contenitore | Minima | Implementazione consigliata |
|-----------|---------|-------------|
| Leggi 2,0-Anteprima | 1 core, 8 GB di memoria |  8 core, 16 GB di memoria |
| Read 3.0-preview | 8 core, 16 GB di memoria | 8 core, 24 GB di memoria |
| Read 3.1-preview | 8 core, 16 GB di memoria | 8 core, 24 GB di memoria |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.
