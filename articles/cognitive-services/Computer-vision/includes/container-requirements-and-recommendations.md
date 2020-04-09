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
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877976"
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
