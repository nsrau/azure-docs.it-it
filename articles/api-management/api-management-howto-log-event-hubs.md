---
title: Come registrare gli eventi in Hub eventi di Azure in Gestione API di Azure | Microsoft Docs
description: Informazioni su come registrare eventi nell'Hub eventi di Azure in Gestione API di Azure.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: ff882889aba7add77d974500ac13a474523b3b53
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Come registrare eventi nell'Hub eventi di Azure in Gestione API di Azure
Hub di eventi di Azure è un servizio di ingresso dati altamente scalabile che può inserire milioni di eventi al secondo in modo che è possibile elaborare e analizzare enormi quantità di dati generati per i dispositivi connessi e le applicazioni. Gli hub di eventi fungono da "porta principale" per una pipeline di eventi e una volta che i dati vengono raccolti in un hub di eventi, possono essere trasformati e archiviati con qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Gli hub di eventi separano la produzione di un flusso di eventi dal consumo di questi eventi, in modo che i consumer di eventi può accedere agli eventi in base a una pianificazione.

Questo articolo è complementare al video su come [Integrare la Gestione API di Azure con gli Hub di eventi](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descrive come registrare gli eventi di gestione API mediante gli hub di eventi di Azure.

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure
Per creare un nuovo hub eventi, accedere al [portale di Azure classico](https://manage.windowsazure.com), quindi fare clic su **Nuovo**->**Servizi app**->**Bus di servizio**->**Hub eventi**->**Creazione rapida**. Immettere un nome e un'area per l'hub eventi, selezionare una sottoscrizione e quindi uno spazio dei nomi. Se non è ancora stato creato uno spazio dei nomi, è possibile crearne uno immettendo un nome nella casella di testo **Spazio dei nomi** . Al termine della configurazione di tutte le proprietà, fare clic su **Crea un nuovo hub eventi** per creare l'hub eventi.

![Creare un hub eventi][create-event-hub]

Passare quindi alla scheda **Configura** per il nuovo hub eventi e creare due tipi di **criteri di accesso condiviso**. Denominare il primo tipo di criteri **Invio** e assegnare ad essi le autorizzazioni di **invio**.

![Criterio Invio][sending-policy]

Denominare il secondo criterio **Ricezione**, assegnare al criterio le autorizzazioni **Listen**, quindi fare clic su **Salva**.

![Criterio Ricezione][receiving-policy]

Ogni tipo di criteri di accesso condiviso consente alle applicazioni di inviare e ricevere eventi verso e dall'hub eventi. Per accedere alle stringhe di connessione relative a questi criteri, passare alla scheda **Dashboard** dell'hub eventi e fare clic su **Informazioni di connessione**.

![Stringa di connessione][event-hub-dashboard]

La stringa di connessione di **invio** viene usata durante la registrazione di eventi, mentre la stringa di connessione di **ricezione** viene usata durante il download di eventi dall'hub eventi.

![Stringa di connessione][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Creare un logger di Gestione API
Dopo aver creato un hub eventi, è necessario configurare un [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) nel servizio Gestione API, in modo che possa registrare eventi nell'hub eventi.

I logger di Gestione API vengono configurati mediante l' [API REST Gestione API](http://aka.ms/smapi). Prima di usare l'API REST per la prima volta, vedere i [prerequisiti](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) e assicurarsi di avere [abilitato l'accesso all'API REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Per creare un logger, effettuare una richiesta HTTP PUT usando il seguente modello URL.

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Sostituire `{your service}` con il nome dell'istanza del servizio Gestione API.
* Sostituire `{new logger name}` con il nome desiderato per il nuovo logger. Si farà riferimento a questo nome al momento di configurare i criteri [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) .

Aggiungere le intestazioni seguenti alla richiesta.

* Content-Type: application/json
* Authorization : SharedAccessSignature 58...
  * Per istruzioni sulla generazione di `SharedAccessSignature` , vedere [Autenticazione dell'API REST Gestione API di Azure](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Specificare il corpo della richiesta usando il modello seguente.

```json
{
  "loggertype" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggertype` deve essere impostato su `AzureEventHub`.
* `description` fornisce una descrizione facoltativa del logger e può essere una stringa di lunghezza zero, se lo si desidera.
* `credentials` contiene i valori `name` e `connectionString` di Hub eventi di Azure.

Quando si esegue la richiesta, se viene creato il logger, verrà visualizzato un codice di stato `201 Created` .

> [!NOTE]
> Per altri possibili codici restituiti e i relativi motivi, vedere [Creare un logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Per informazioni su come eseguire altre operazioni, ad esempio elencare, aggiornare o eliminare, vedere la documentazione relativa all'entità [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) .
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Configurare i criteri log-to-eventhubs
Dopo la configurazione del logger in Gestione API, è possibile configurare i criteri log-to-eventhubs in modo da registrare gli eventi desiderati. I criteri log-to-eventhubs possono essere usati nella sezione relativa ai criteri in ingresso o in quella relativa ai criteri in uscita.

Per configurare i criteri, accedere al [portale di Azure](https://portal.azure.com), passare al servizio Gestione API e fare clic su **Portale di pubblicazione** per accedervi.

![Portale di pubblicazione][publisher-portal]

Scegliere **Criteri** dal menu di Gestione API a sinistra, selezionare il prodotto e l'API desiderati e quindi fare clic su **Aggiungi criteri**. In questo esempio si aggiungono dei criteri all'**API Echo** nel prodotto **Unlimited**.

![Aggiungi criteri][add-policy]

Posizionare il cursore nella sezione dei criteri `inbound` e fare clic sui criteri **Accedi a Hub eventi** per inserire il modello di istruzione dei criteri `log-to-eventhub`.

![Policy editor][event-hub-policy]

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```

Sostituire `logger-id` con il nome del logger di Gestione API configurato nel passaggio precedente.

È possibile usare qualsiasi espressione che restituisce una stringa come valore dell'elemento `log-to-eventhub` . In questo esempio viene registrata una stringa contenente data e ora, nome del servizio, ID richiesta, indirizzo IP della richiesta e nome dell'operazione.

Fare clic su **Salva** per salvare la configurazione aggiornata dei criteri. Il criterio risulta attivo immediatamente dopo il salvataggio e gli eventi vengono registrati nell'hub eventi designato.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'Hub eventi di Azure
  * [Introduzione all'Hub eventi](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ricevere messaggi con EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md)
* Altre informazioni sull'integrazione di Gestione API e Hub eventi
  * [Informazioni di riferimento per l'entità logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Informazioni di riferimento per i criteri log-to-event](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorare le API con Gestione API di Azure, Hub eventi e Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Guardare un video con la procedura dettagliata
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
