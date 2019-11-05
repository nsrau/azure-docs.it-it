---
title: Raccolta dei parametri obbligatori
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametri per tutti i contenitori di servizi cognitivi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465925"
---
## <a name="gathering-required-parameters"></a>Raccolta dei parametri obbligatori

Sono disponibili tre parametri primari per tutti i contenitori di servizi cognitivi richiesti. Il contratto di licenza con l'utente finale deve essere presente con il valore `accept`. Sono inoltre necessari un URL dell'endpoint e una chiave API.

### <a name="endpoint-uri-endpoint_uri"></a>URI dell'endpoint `{ENDPOINT_URI}`

Il valore dell'URI dell' **endpoint** è disponibile nella pagina *Panoramica* portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *Panoramica* , posizionare il puntatore del mouse sull'endpoint e verrà <span class="docon docon-edit-copy x-hidden-focus"></span> visualizzata un'icona `Copy to clipboard`. Copiare e usare se necessario.

![Raccogliere l'URI dell'endpoint per un uso successivo](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Chiavi `{API_KEY}`

Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina chiavi del portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *chiavi* e fare clic sull'icona `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Ottenere una delle due chiavi per un uso successivo](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di servizi cognitivi. Non condividere le chiavi. Archiviarli in modo sicuro, ad esempio usando Azure Key Vault. Si consiglia inoltre di rigenerare regolarmente queste chiavi. È necessaria una sola chiave per effettuare una chiamata API. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuo al servizio.