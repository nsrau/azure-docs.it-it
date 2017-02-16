---
title: Aggiungere il caching per migliorare le prestazioni in Gestione API di Azure | Microsoft Docs
description: Informazioni su come migliorare la latenza, il consumo della larghezza di banda e il carico del servizio Web per le chiamate del servizio Gestione API.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: d4ba7c276b0ad8539cfbad9b9a6afe193af3a0b8


---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Aggiungere il caching per migliorare le prestazioni in Gestione API di Azure
Le operazioni in Gestione API possono essere configurate per la memorizzazione nella cache della risposta. La memorizzazione nella cache della risposta può ridurre significativamente la latenza delle API, il consumo di larghezza di banda e il carico del servizio Web per i dati che non vengono modificati di frequente.

Questa Guida illustra come aggiungere la memorizzazione delle risposte nella cache per l'API e configurare i criteri per le operazioni API Echo di esempio. Per verificare il funzionamento della memorizzazione della cache, è possibile chiamare l'operazione dal portale per sviluppatori.

> [!NOTE]
> Per informazioni sul caching degli elementi in base alla chiave usando espressioni di criteri, vedere [Caching personalizzato in Gestione API di Azure](api-management-sample-cache-by-key.md).
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Prima di eseguire i passaggi di questa guida, è necessario avere un'istanza del servizio Gestione API con un'API e un prodotto configurati. Se non è stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance] nell'esercitazione [Introduzione a Gestione API di Azure][Get started with Azure API Management].

## <a name="configure-caching"> </a>Configurare un'operazione per la memorizzazione nella cache
In questo passaggio vengono riviste le impostazioni di caching dell'operazione **GET su risorsa (memorizzata nella cache)** dell'API Echo di esempio.

> [!NOTE]
> Ogni istanza del servizio Gestione API è preconfigurata con un'API Echo utilizzabile per sperimentare e ottenere altre informazioni su Gestione API. Per altre informazioni, vedere [Introduzione a Gestione API di Azure][Get started with Azure API Management].
> 
> 

Per iniziare, fare clic sul **portale di pubblicazione** nel Portale di Azure relativo al servizio Gestione API. Verrà visualizzato il portale di pubblicazione di Gestione API.

![Portale di pubblicazione][api-management-management-console]

Scegliere **API** dal menu **Gestione API** a sinistra, quindi fare clic su **Echo API** (API Echo).

![API Echo][api-management-echo-api]

Fare clic sulla scheda **Operazioni**, quindi sull'operazione **GET Resource (cached)** (GET su risorsa - memorizzata nella cache) nell'elenco **Operazioni**.

![Operazioni dell'API Echo][api-management-echo-api-operations]

Fare clic sulla scheda **Caching** per visualizzare le impostazioni di caching per l'operazione.

![Scheda Memorizzazione nella cache][api-management-caching-tab]

Per abilitare il caching per un'operazione, selezionare la casella di controllo **Abilita** . In questo esempio, il caching è abilitato.

La risposta di ogni operazione è associata a una chiave in base ai valori dei campi **Variabile in base a parametri stringa query** e **Vary by headers** (Variabile in base a intestazioni). Per memorizzare nella cache più risposte in base ai parametri delle stringhe di query o alle intestazioni, è possibile configurarle in questi due campi.

**durata** specifica l'intervallo di scadenza delle risposte memorizzate nella cache. In questo esempio l'intervallo è di **3600** secondi, equivalente a un'ora.

Nella configurazione di esempio del caching la prima richiesta all'operazione **GET su risorsa (memorizzata nella cache)** restituisce una risposta dal servizio back-end. Questa risposta viene memorizzata nella cache, associata a una chiave mediante le intestazioni e i parametri delle stringhe di query specificati. Le chiamate successive all'operazione, con i parametri corrispondenti, riceveranno la risposta memorizzata nella cache finché non scade l'intervallo di durata della cache.

## <a name="caching-policies"> </a>Rivedere i criteri di memorizzazione nella cache
In questo passaggio vengono riviste le impostazioni di caching dell'operazione **GET su risorsa** (memorizzata nella cache) dell'API Echo di esempio.

Quando le impostazioni di memorizzazione nella cache vengono configurate per un'operazione nella scheda **Memorizzazione nella cache** , vengono aggiunti i criteri di memorizzazione nella cache per l'operazione. Questi criteri possono essere visualizzati e modificati nell'editor dei criteri.

Fare clic su **Criteri** dal menu **Gestione API** a sinistra, quindi selezionare **Echo API / GET Resource (cached)** (API Echo/GET su risorsa - memorizzata nella cache) dall'elenco a discesa **Operazione**.

![Operazione nell'ambito dei criteri][api-management-operation-dropdown]

Visualizza i criteri per l'operazione nell'editor dei criteri.

![Editor dei criteri di Gestione API][api-management-policy-editor]

La definizione dei criteri per questa operazione include i criteri che definiscono la configurazione della memorizzazione nella cache rivisti usando la scheda **Memorizzazione nella cache** nel passaggio precedente.

```xml
<policies>
    <inbound>
        <base />
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
        </cache-lookup>
        <rewrite-uri template="/resource" />
    </inbound>
    <outbound>
        <base />
        <cache-store caching-mode="cache-on" duration="3600" />
    </outbound>
</policies>
```

> [!NOTE]
> Le modifiche apportate ai criteri di caching nell'editor dei criteri si rifletteranno nella scheda **Caching** di un'operazione e viceversa.
> 
> 

## <a name="test-operation"> </a>Chiamare un'operazione e testare la memorizzazione nella cache
Per vedere il funzionamento della memorizzazione nella cache, l'operazione viene chiamata dal portale per sviluppatori. Fare clic su **Developer portal** nel menu in alto a destra.

![Portale per sviluppatori][api-management-developer-portal-menu]

Fare clic su **API** dal menu in alto e quindi scegliere **Echo API** (API Echo).

![API Echo][api-management-apis-echo-api]

> Se è stata configurata una sola API o se ne è visibile solo una per l'account, facendo clic sulle API vengono visualizzate le operazioni per l'API.
> 
> 

Selezionare l'operazione **GET Resource (cached)** (GET su risorsa - memorizzata nella cache), quindi fare clic su **Apri console**.

![Open console][api-management-open-console]

La console consente di richiamare le operazioni direttamente dal portale per sviluppatori.

![Console][api-management-console]

Mantenere i valori predefiniti per **param1** e **param2**.

Selezionare la chiave desiderata dall'elenco a discesa **subscription-key** . Se l'account ha una sola sottoscrizione, sarà già selezionata automaticamente.

Immettere **sampleheader:value1** nella casella di testo **Intestazioni della richiesta**.

Fare clic su **GET HTTP** e annotare le intestazioni di risposta.

Immettere **sampleheader:value2** nella casella di testo **Intestazioni della richiesta** e quindi fare clic su **GET HTTP**.

Il valore di **sampleheader** nella risposta è ancora **value1**. Provare altri valori diversi e notare che viene restituita la risposta memorizzata nella cache della prima chiamata.

Immettere **25** nel campo **param2**, quindi fare clic su **GET HTTP**.

Il valore di **sampleheader** nella risposta ora è **value2**. I risultati dell'operazione vengono associati a una chiave in base alla stringa di query, quindi non viene restituita la risposta memorizzata nella cache precedente.

## <a name="next-steps"> </a>Passaggi successivi
* Per altre informazioni sui criteri di caching, vedere [Caching policies][Caching policies] (Criteri di caching) nell'argomento [API Management policy reference][API Management policy reference] (Riferimento ai criteri di Gestione API).
* Per informazioni sul caching degli elementi in base alla chiave usando espressioni di criteri, vedere [Caching personalizzato in Gestione API di Azure](api-management-sample-cache-by-key.md).

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
[Get started with Azure API Management]: api-management-get-started.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps



<!--HONumber=Dec16_HO3-->


