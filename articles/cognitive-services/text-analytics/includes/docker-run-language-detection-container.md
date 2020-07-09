---
title: Esempio di esecuzione del contenitore del comando Docker Run
titleSuffix: Azure Cognitive Services
description: Comando Docker Run per Rilevamento lingua contenitore
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: e39f9b537b2b57b455d2e0ab40ba09f1a6a890ba
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108970"
---
Per eseguire il contenitore *rilevamento lingua* , eseguire il `docker run` comando seguente. Sostituire i segnaposto seguenti con valori personalizzati:

| Segnaposto | Valore | Formato o esempio |
|-------------|-------|---|
| **{API_KEY}** | Chiave per la risorsa Analisi del testo. È possibile trovarlo nella pagina **chiave ed endpoint** della risorsa, nella portale di Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Endpoint per l'accesso alla API Analisi del testo. È possibile trovarlo nella pagina **chiave ed endpoint** della risorsa, nella portale di Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Questo comando:

* Esegue un *rilevamento lingua* contenitore dall'immagine del contenitore
* Alloca un core CPU e 4 GB di memoria
* Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
* Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.