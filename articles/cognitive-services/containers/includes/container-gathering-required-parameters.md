---
title: Raccolta dei parametri necessari
services: cognitive-services
author: IEvangelist
manager: nitinme
description: I parametri per tutti i contenitori di Servizi cognitivi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465925"
---
## <a name="gathering-required-parameters"></a>Raccolta dei parametri necessari

Esistono tre parametri principali per tutti i contenitori di Servizi cognitivi necessari. Il contratto di licenza con l'utente finale `accept`(EULA) deve essere presente con un valore di . Inoltre, sono necessari sia un URL dell'endpoint che una chiave API.

### <a name="endpoint-uri-endpoint_uri"></a>Endpoint URI`{ENDPOINT_URI}`

Il valore URI **dell'endpoint** è disponibile nella pagina *Panoramica* del portale di Azure della risorsa Servizio cognitivo corrispondente. Passare alla pagina *Panoramica,* passare il `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> mouse sull'endpoint e verrà visualizzata un'icona. Copiare e utilizzare dove necessario.

![Raccogliere l'URI dell'endpoint per un utilizzo successivoGather the endpoint uri for later use](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Chiavi`{API_KEY}`

Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina Chiavi del portale di Azure della risorsa del servizio cognitivo corrispondente. Passare alla pagina *Chiavi* e `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> fare clic sull'icona.

![Ottenere una delle due chiavi per un uso successivoGet one of the two keys for later use](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API del servizio cognitivo. Non condividere le chiavi. Archiviarli in modo sicuro, ad esempio usando L'insieme di credenziali delle chiavi di Azure.Store them securely, for example, using Azure Key Vault. Si consiglia inoltre di rigenerare questi tasti regolarmente. Per effettuare una chiamata API è necessaria una sola chiave. Quando si rigenera la prima chiave, è possibile utilizzare la seconda chiave per l'accesso continuo al servizio.