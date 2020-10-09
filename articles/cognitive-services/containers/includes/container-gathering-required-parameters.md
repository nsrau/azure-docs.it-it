---
title: Raccolta dei parametri obbligatori
services: cognitive-services
author: aahill
manager: nitinme
description: Parametri per tutti i contenitori di servizi cognitivi
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80875044"
---
## <a name="gathering-required-parameters"></a>Raccolta dei parametri obbligatori

Sono disponibili tre parametri primari per tutti i contenitori di servizi cognitivi richiesti. Il contratto di licenza con l'utente finale deve essere presente con un valore pari a `accept` . Sono inoltre necessari un URL dell'endpoint e una chiave API.

### <a name="endpoint-uri-endpoint_uri"></a>URI dell'endpoint `{ENDPOINT_URI}`

Il valore dell'URI dell' **endpoint** è disponibile nella pagina *Panoramica* portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *Panoramica* , posizionare il puntatore del mouse sull'endpoint e `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> verrà visualizzata un'icona. Copiare e usare se necessario.

![Raccogliere l'URI dell'endpoint per un uso successivo](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Chiavi `{API_KEY}`

Questa chiave viene usata per avviare il contenitore ed è disponibile nella pagina chiavi del portale di Azure della risorsa del servizio cognitiva corrispondente. Passare alla pagina *chiavi* e fare clic sull' `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icona.

![Ottenere una delle due chiavi per un uso successivo](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Queste chiavi di sottoscrizione vengono usate per accedere all'API di Servizi cognitivi. Non condividerle. Archiviarli in modo sicuro, ad esempio usando Azure Key Vault. È inoltre consigliabile rigenerare queste chiavi regolarmente. Per effettuare una chiamata API è necessaria una sola chiave. Quando si rigenera la prima chiave, è possibile usare la seconda chiave per l'accesso continuato al servizio.