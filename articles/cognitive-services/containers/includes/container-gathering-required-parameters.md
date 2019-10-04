---
title: Raccolta dei parametri obbligatori
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Parametri per tutti i contenitori di servizi cognitivi
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 06f68d28d06dec7c9e738ad8cb50ca337fa840be
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102023"
---
## <a name="gathering-required-parameters"></a>Raccolta dei parametri obbligatori

Sono disponibili tre parametri primari per tutti i contenitori di servizi cognitivi richiesti. Il contratto di licenza con l'utente finale deve essere presente con un valore pari `accept`a. Sono inoltre necessari un URL dell'endpoint e una chiave API.

> [!NOTE]
> L'unica eccezione a questi tre parametri obbligatori è quando i contenitori sono considerati contenitori "offline". I contenitori offline non segnalano l'utilizzo, non vengono misurati e seguono una metodologia di fatturazione diversa.

### <a name="endpoint-uri-endpoint_uri"></a>URI dell'endpoint`{ENDPOINT_URI}`

Il valore dell'URI dell' **endpoint** è disponibile nella pagina *Panoramica* portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *Panoramica* , posizionare il puntatore del mouse sull'endpoint e `Copy to clipboard` verrà visualizzata un' <span class="docon docon-edit-copy x-hidden-focus"></span> icona. Copiare e usare se necessario.

![Raccogliere l'URI dell'endpoint per un uso successivo](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Chiavi`{API_KEY}`

Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina chiavi del portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *chiavi* e fare clic sull' `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icona.

![Ottenere una delle due chiavi per un uso successivo](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di servizi cognitivi. Non condividere le chiavi. Archiviarli in modo sicuro, ad esempio usando Azure Key Vault. Si consiglia inoltre di rigenerare regolarmente queste chiavi. È necessaria una sola chiave per effettuare una chiamata API. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuo al servizio.