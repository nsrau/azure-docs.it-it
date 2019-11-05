---
title: Indicazioni e requisiti dei contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481750"
---
> [!NOTE]
> I requisiti e le indicazioni sono basati sui benchmark con una singola richiesta al secondo, usando un'immagine di 8 MB di una lettera di business scansionata che contiene 29 righe e un totale di 803 caratteri.

La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore di lettura.

| Contenitore | Minima | Consigliato |PROGRAMMI<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
| Lettura | 1 core, 8 GB di memoria, 0,24 TPS | 8 core, 16 GB di memoria, 1,17 TPS | 0,24, 1,17 |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* TPS-transazioni al secondo.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.
