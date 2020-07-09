---
title: Pull Docker per il contenitore di integrità
titleSuffix: Azure Cognitive Services
description: Comando Docker pull per Analisi del testo per il contenitore di integrità
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108990"
---
Compilare e inviare il [modulo di richiesta di contenitori di servizi cognitivi](https://aka.ms/cognitivegate) per richiedere l'accesso al contenitore.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Usare il comando Docker login con le credenziali fornite nel messaggio di posta elettronica di onboarding per connettersi al registro contenitori privato per i contenitori di servizi cognitivi.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Usare il [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando per scaricare l'immagine del contenitore dal registro contenitori privato.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
