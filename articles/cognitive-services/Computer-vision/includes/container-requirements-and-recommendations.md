---
title: Indicazioni e requisiti per i contenitori
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129808"
---
> [!NOTE]
> I requisiti e le indicazioni sono basati sui benchmark con una singola richiesta al secondo, usando un'immagine di 8 MB di una lettera di business scansionata che contiene 29 righe e un totale di 803 caratteri.

#### <a name="readtabread"></a>[Lettura](#tab/read)

La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore di lettura.

| Contenitore | Minima | Consigliato |TPS<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
| Leggi | 1 core, 8 GB di memoria, 0,24 TPS | 8 core, 16 GB di memoria, 1,17 TPS | 0,24, 1,17 |

#### <a name="recognize-texttabrecognize-text"></a>[Riconoscimento del testo](#tab/recognize-text)

La tabella seguente descrive l'allocazione minima e consigliata delle risorse per ogni contenitore riconoscimento del testo.

| Contenitore | Minima | Consigliato |TPS<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
| Riconoscimento del testo | 1 core, 8 GB di memoria, 0,12 TPS | 8 core, 16 GB di memoria, 0,60 TPS | 0,12, 0,60 |

***

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* TPS-transazioni al secondo.

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.
