---
title: Come aggiungere operazioni a un'API in Gestione API di Azure | Microsoft Docs
description: Informazioni su come aggiungere operazioni a un'API in Gestione API di Azure.
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 1158a023-1913-4e9c-93de-9164b672f9b3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 105fc51c2d1152a40a5757985da47330e0b7b8cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Come aggiungere operazioni a un'API in Gestione API di Azure
Prima di poter usare un'API in Gestione API, è necessario aggiungervi le operazioni. Questa guida descrive come aggiungere e configurare diversi tipi di operazioni a un'API in Gestione API.

## <a name="add-operation"> </a>Aggiungere un'operazione
Le operazioni vengono aggiunte e configurate in un'API nel portale di pubblicazione. Per accedere al portale di pubblicazione, fare clic su **Portale di pubblicazione** nel portale di Azure per il servizio Gestione API.

![Portale di pubblicazione][api-management-management-console]

> Se non è stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API][Create an API Management service instance] nell'esercitazione [Introduzione a Gestione API di Azure][Get started with Azure API Management].
> 
> 

Selezionare l'API desiderata nel portale di pubblicazione e quindi selezionare la scheda **Operazioni** . 

![Operazioni][api-management-operations]

Fare clic su **Aggiungi operazione** per aggiungere una nuova operazione. Verrà visualizzata la finestra **Nuova operazione** in cui la scheda **Firma** è visualizzata per impostazione predefinita.

![Aggiungi operazione][api-management-add-operation]

Specificare il **verbo HTTP** dall'elenco a discesa.

![Metodo HTTP][api-management-http-method]

<a name="url-template"></a>

Definire il modello di URL digitando un frammento dell'URL composto da uno o più segmenti di percorso dell'URL e zero o più parametri di stringa di query. Il modello di URL, aggiunto all'URL di base dell'API, identifica una singola operazione HTTP. Può contenere una o più parti variabili denominate identificate da parentesi graffe. Queste parti variabili sono denominate parametri di modello e ad esse vengono assegnati in modo dinamico i valori estratti dall'URL della richiesta quando la richiesta viene elaborata dalla piattaforma di Gestione API.

> Il modello di URL può includere caratteri jolly. Specificando ad esempio `/*`, tutte le richieste per il metodo HTTP verranno inoltrate al servizio back-end.

![Modello di URL][api-management-url-template]

<a name="rewrite-url-template"></a>

Se si vuole, è possibile specificare il **Modello di URL di riscrittura**. Questo consente di usare il modello di URL standard per elaborare le richieste in ingresso nel front-end, chiamando il back-end tramite un URL convertito in base al modello di riscrittura. Nel modello di riscrittura è necessario usare i parametri di modello dell'URL di modello. L'esempio seguente mostra il modo in cui il tipo di contenuto codificato come segmento del percorso nel servizio Web dell'esempio precedente può essere specificato come parametro di query nell'API pubblicata tramite la piattaforma di Gestione API usando i modelli di URL.

![Riscrittura modello di URL][api-management-url-template-rewrite]

I chiamanti delle operazioni useranno il formato `/customers?customerid=ALFKI` che verrà mappato a `/Customers('ALFKI')` quando viene richiamato il servizio back-end.

In **Nome visualizzato** e **Descrizione** viene fornita una descrizione dell'operazione e vengono usati per fornire la documentazione necessaria agli sviluppatori che usano questa API nel portale per sviluppatori.

![Description][api-management-description]

È possibile specificare la descrizione dell'operazione come testo normale o HTML nella casella di testo **Descrizione** .

## <a name="operation-caching"> </a>Memorizzazione nella cache di un'operazione
La memorizzazione nella cache della risposta riduce la latenza percepita dai consumer dell'API, riduce l'utilizzo della larghezza di banda e diminuisce il carico nel servizio Web HTTP che implementa l'API. 

Per abilitare la memorizzazione nella cache per l'operazione in modo facile e veloce, selezionare la scheda **Memorizzazione nella cache** e selezionare la casella di controllo **Abilita**.

![Memorizzazione nella cache][api-management-caching-tab]

**Durata** viene specificato il periodo di tempo durante il quale la risposta dell'operazione rimane nella cache. Il valore predefinito è 3.600 secondi o un'ora.

Le chiavi della cache vengono usate per differenziare le risposte in modo che ogni risposta corrispondente a una chiave della cache ottenga il proprio valore memorizzato nella cache separato. Facoltativamente, è possibile immettere parametri di stringa di query specifici e/o intestazioni HTTP da usare nei valori delle chiavi della cache di calcolo rispettivamente nelle caselle di testo **Varia in base ai parametri di stringa di query** e **Varia in base alle intestazioni**. Se non viene specificato alcun valore, per la generazione della chiave della cache vengono usati i valori dell'URL di richiesta completo e l'intestazione HTTP seguenti: **Accept** e **Accept-Charset**.

> Per altre informazioni sulla memorizzazione nella cache e sui relativi criteri, vedere [Come memorizzare nella cache i risultati dell'operazione in Gestione API di Azure][How to cache operation results in Azure API Management].
> 
> 

## <a name="request-parameters"> </a>Parametri della richiesta
È possibile gestire i parametri dell'operazione nella scheda Parametri. I parametri specificati in **Modello di URL** nella scheda **Firma** vengono aggiunti automaticamente e possono essere cambiati solo modificando il modello di URL. È possibile immettere i parametri aggiuntivi manualmente.

Per aggiungere un parametro di query, fare clic su **Aggiungi parametro di query** e immettere le informazioni seguenti:

* **Nome** : nome del parametro.
* **Descrizione** : breve descrizione del parametro (facoltativa).
* **Tipo** : tipo del parametro, selezionato nell'elenco a discesa.
* **Valori** : valori che possono essere assegnati al parametro. Uno dei valori può essere contrassegnato come predefinito (facoltativo).
* **Obbligatorio** : selezionare questa casella di controllo per rendere il parametro obbligatorio. 

![Parametri della richiesta][api-management-request-parameters]

## <a name="request-body"> </a>Corpo della richiesta
Se l'operazione consente (ad esempio, PUT, POST) e richiede un corpo, è possibile fornirne un esempio in tutti i formati di rappresentazione supportati, ad esempio JSON e XML. 

> Il corpo della richiesta è usato solo ai fini della documentazione e non viene convalidato.
> 
> 

Per immettere un corpo della richiesta, passare alla scheda **Corpo** .

Fare clic su **Aggiungi rappresentazione**, iniziare a digitare il nome del tipo di contenuto desiderato (ad esempio, applicazione/json), selezionarlo nell'elenco a discesa e incollare nella casella di testo l'esempio di corpo della richiesta nel formato selezionato. 

![Corpo della richiesta][api-management-request-body]

Oltre alle rappresentazioni, è possibile specificare una descrizione di testo facoltativa nella casella di testo **Descrizione** .

## <a name="responses"> </a>Risposte
È consigliabile fornire esempi di risposte per tutti i codici di stato restituiti dall'operazione. Per ogni codice di stato può esistere più di un esempio di corpo della risposta, ovvero uno per ogni tipo di contenuto supportato. 

Per aggiungere una risposta, fare clic su **Aggiungi** e iniziare a digitare il codice di stato desiderato. In questo esempio il codice di stato è **200 OK**. Quando il codice viene visualizzato nell'elenco a discesa, selezionarlo in modo che il codice della risposta venga creato e aggiunto all'operazione.

![Codice della risposta][api-management-response-code]

Fare clic su **Aggiungi rappresentazione**, iniziare a digitare il nome del tipo di contenuto desiderato (ad esempio, applicazione/json) e selezionarlo nell'elenco a discesa.

![Tipo di contenuto del corpo][api-management-response-body-content-type]

Incollare nella casella di testo l'esempio del corpo della risposta nel formato selezionato. 

![Corpo della risposta][api-management-response-body]

Se necessario, aggiungere una descrizione facoltativa nella casella di testo **Descrizione** .

Dopo avere configurato l'operazione, fare clic su **Salva**.

## <a name="next-steps"> </a>Passaggi successivi
Una volta aggiunte le operazioni a un'API, nel passaggio successivo l'API verrà associata a un prodotto e verrà pubblicata in modo che gli sviluppatori possano chiamare le relative operazioni.

* [Come creare e pubblicare un prodotto][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
