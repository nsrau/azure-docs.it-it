---
title: Requisiti del contenitore e consigli.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034482"
---
La tabella seguente indica i core CPU minimi e consigliati e la memoria da allocare per ogni contenitore di Riconoscimento del testo.

| Contenitore | Minima | Consigliato |TPS<br>(Minimo, massimo)|
|-----------|---------|-------------|--|
|Riconoscimento del testo|1 core, 8 GB di memoria, 0,5 TPS|2 Core, 8 GB di memoria, 1 TPS|0,5, 1|

* Ogni core deve essere di almeno 2,6 gigahertz (GHz) o superiore.
* TPS - transazioni al secondo

Core e memoria corrispondono alle impostazioni `--cpus` e `--memory` che vengono usate come parte del comando `docker run`.