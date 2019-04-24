---
title: Aggiungere il caching per migliorare le prestazioni in Gestione API di Azure | Microsoft Docs
description: Informazioni su come migliorare la latenza, il consumo della larghezza di banda e il carico del servizio Web per le chiamate del servizio Gestione API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: a0459eb67b5a79219e556cb03473a5ddf691b49d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60527468"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Aggiungere il caching per migliorare le prestazioni in Gestione API di Azure

Le operazioni in Gestione API possono essere configurate per la memorizzazione nella cache della risposta. La memorizzazione nella cache della risposta può ridurre significativamente la latenza delle API, il consumo di larghezza di banda e il carico del servizio Web per i dati che non vengono modificati di frequente.

Per informazioni più dettagliate sulla memorizzazione nella cache, vedere [Criteri di memorizzazione nella cache in Gestione API](api-management-caching-policies.md) e [Memorizzazione nella cache personalizzata in Gestione API di Azure](api-management-sample-cache-by-key.md).

![Criteri della cache](media/api-management-howto-cache/cache-policies.png)

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Aggiungere la memorizzazione delle risposte nella cache per l'API
> * Verificare il funzionamento della memorizzazione nella cache

## <a name="availability"></a>Disponibilità

> [!NOTE]
> La cache interna non è disponibile nel livello **A consumo** di Gestione API di Azure. È possibile [usare una cache di Azure per Redis esterna](api-management-howto-cache-external.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

+ [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
+ [Importare e pubblicare un'API](import-and-publish.md)

## <a name="caching-policies"> </a>Aggiungere i criteri di memorizzazione nella cache

Con i criteri di memorizzazione nella cache illustrati in questo esempio, la prima richiesta per l'operazione **GetSpeakers** restituisce una risposta dal servizio back-end. Questa risposta viene memorizzata nella cache, associata a una chiave in base alle intestazioni e ai parametri delle stringhe di query specifici. Le chiamate successive all'operazione, con i parametri corrispondenti, riceveranno la risposta memorizzata nella cache finché non scade l'intervallo di durata della cache.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Passare all'istanza di Gestione API.
3. Selezionare la scheda **API**.
4. Fare clic su **Demo Conference API** (API Demo Conference) nell'elenco di API.
5. Selezionare **GetSpeakers**.
6. Nella parte superiore della schermata selezionare la scheda **Progettazione**.
7. Nella sezione **Elaborazione in ingresso** fare clic sull'icona **</>**.

    ![Editor di codice](media/api-management-howto-cache/code-editor.png)

8. Nell'elemento **inbound** aggiungere il criterio seguente:

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

9. Nell'elemento **outbound** aggiungere il criterio seguente:

        <cache-store caching-mode="cache-on" duration="20" />

    **durata** specifica l'intervallo di scadenza delle risposte memorizzate nella cache. In questo esempio l'intervallo è di **20** secondi.

> [!TIP]
> Se si usa una cache esterna, come descritto in [Usare una cache Redis esterna in Gestione API di Azure](api-management-howto-cache-external.md), è possibile specificare l'attributo `caching-type` dei criteri di memorizzazione nella cache. Vedere [Criteri di memorizzazione nella cache in Gestione API](api-management-caching-policies.md) per altri dettagli.

## <a name="test-operation"> </a>Chiamare un'operazione e testare la memorizzazione nella cache
Per vedere il funzionamento della memorizzazione nella cache, chiamare l'operazione dal portale per sviluppatori.

1. Nel portale di Azure passare all'istanza di Gestione API.
2. Selezionare la scheda **API**.
3. Selezionare l'API a cui sono stati aggiunti i criteri di memorizzazione nella cache.
4. Selezionare l'operazione **GetSpeakers**.
5. Fare clic sulla scheda **Test** nel menu in alto a destra.
6. Fare clic su **Invia**.

## <a name="next-steps"> </a>Passaggi successivi
* Per altre informazioni sui criteri di caching, vedere [Caching policies][Caching policies] (Criteri di caching) nell'argomento [API Management policy reference][API Management policy reference] (Riferimento ai criteri di Gestione API).
* Per informazioni sul caching degli elementi in base alla chiave usando espressioni di criteri, vedere [Caching personalizzato in Gestione API di Azure](api-management-sample-cache-by-key.md).
* Per altre informazioni sull'uso della cache di Azure per Redis esterna, vedere [Usare una cache Redis esterna in Gestione API di Azure](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
