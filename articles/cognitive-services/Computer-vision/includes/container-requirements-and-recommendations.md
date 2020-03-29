---
title: Indicazioni e requisiti per i contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481750"
---
> [!NOTE]
> I requisiti e le raccomandazioni si basano su benchmark con una singola richiesta al secondo, utilizzando un'immagine di 8 MB di una lettera commerciale digitalizzata che contiene 29 righe e un totale di 803 caratteri.

Nella tabella seguente viene descritta l'allocazione minima e consigliata delle risorse per ogni contenitore Read.

| Contenitore | Minima | Consigliato |Tps<br>(Minimo, Massimo)|
|-----------|---------|-------------|--|
| Lettura | 1 core, 8 GB di memoria, 0,24 TPS | 8 core, 16 GB di memoria, 1,17 TPS | 0.24, 1.17 |

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* TPS - transazioni al secondo.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.
