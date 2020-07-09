---
title: Esempio di esecuzione del contenitore del comando Docker Run
titleSuffix: Azure Cognitive Services
description: Comando Docker Run per il contenitore di integrità
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108952"
---
Per eseguire il contenitore, trovare prima il relativo ID immagine:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Eseguire il `docker run` comando seguente. Sostituire i segnaposto seguenti con valori personalizzati:

| Segnaposto | Valore | Formato o esempio |
|-------------|-------|---|
| **{API_KEY}** | Chiave per la risorsa Analisi del testo. È possibile trovarlo nella pagina **chiave ed endpoint** della risorsa, nella portale di Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Endpoint per l'accesso alla API Analisi del testo. È possibile trovarlo nella pagina **chiave ed endpoint** della risorsa, nella portale di Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | ID immagine per il contenitore. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Directory di input per il contenitore. | Windows: `C:\healthcareMount` <br> Linux/MacOS:`/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Questo comando:

- Presuppone che la directory di input esista nel computer host
- Esegue un Analisi del testo per il contenitore di integrità dall'immagine del contenitore
- Alloca 6 core CPU e 12 gigabyte (GB) di memoria
- Espone la porta TCP 5000 e alloca un pseudo terminale TTY per il contenitore
- Rimuove automaticamente il contenitore dopo la chiusura. L'immagine del contenitore rimane disponibile nel computer host.
