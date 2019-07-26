---
title: Raccolta dei parametri obbligatori
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametri per tutti i contenitori di servizi cognitivi
ms.service: cognitive-services
ms.topic: include
ms.date: 7/24/2019
ms.author: dapine
ms.openlocfilehash: 636a41fde345a08db1549e53626522962f9cf74f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488793"
---
## <a name="gathering-required-parameters"></a>Raccolta dei parametri obbligatori

Sono disponibili tre parametri primari per tutti i contenitori di servizi cognitivi richiesti. Il contratto di licenza con l'utentefinale deve essere presente con un valore pari `accept`a. Sono inoltre necessari un URL dell'endpoint e una chiave API.

### <a name="endpoint-uri-endpointuri"></a>URI dell'endpoint`{Endpoint_URI}`

Il valore dell'URI dell' **endpoint** è disponibile nella pagina *Panoramica* portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *Panoramica* , posizionare il puntatore del mouse sull'endpoint e `Copy to clipboard` verrà visualizzata un' <span class="docon docon-edit-copy x-hidden-focus"></span> icona. Copiare e usare se necessario.

![Raccogliere l'URI dell'endpoint per un uso successivo](../media/overview-endpoint-uri.png)

### <a name="keys-apikey"></a>Chiavi`{API_Key}`

Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina chiavi del portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *chiavi* e fare clic sull' `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icona.

![Ottenere una delle due chiavi per un uso successivo](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di servizi cognitivi. Non condividere le chiavi. Archiviarli in modo sicuro, ad esempio usando Azure Key Vault. Si consiglia inoltre di rigenerare regolarmente queste chiavi. È necessaria una sola chiave per effettuare una chiamata API. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuo al servizio.