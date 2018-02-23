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
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 77c3e41dd4b1fdf7e518de67b353f69fcb758c60
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Come registrare eventi nell'Hub eventi di Azure in Gestione API di Azure
Hub di eventi di Azure è un servizio di ingresso dati altamente scalabile che può inserire milioni di eventi al secondo in modo che è possibile elaborare e analizzare enormi quantità di dati generati per i dispositivi connessi e le applicazioni. Gli hub di eventi fungono da "porta principale" per una pipeline di eventi e una volta che i dati vengono raccolti in un hub di eventi, possono essere trasformati e archiviati con qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Gli hub di eventi separano la produzione di un flusso di eventi dal consumo di questi eventi, in modo che i consumer di eventi può accedere agli eventi in base a una pianificazione.

Questo articolo è complementare al video su come [Integrare la Gestione API di Azure con gli Hub di eventi](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descrive come registrare gli eventi di gestione API mediante gli hub di eventi di Azure.

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure

Per informazioni dettagliate su come creare un hub eventi e ottenere le stringhe di connessione necessarie per inviare e ricevere eventi da e verso l'hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il portale di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Creare un logger di Gestione API
Dopo aver creato un hub eventi, è necessario configurare un [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) nel servizio Gestione API, in modo che possa registrare eventi nell'hub eventi.

I logger di Gestione API vengono configurati mediante l' [API REST Gestione API](http://aka.ms/smapi). Prima di usare l'API REST per la prima volta, vedere i [prerequisiti](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) e assicurarsi di avere [abilitato l'accesso all'API REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Per creare un logger, effettuare una richiesta HTTP PUT usando il modello URL seguente:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Sostituire `{your service}` con il nome dell'istanza del servizio Gestione API.
* Sostituire `{new logger name}` con il nome desiderato per il nuovo logger. Si farà riferimento a questo nome al momento di configurare i criteri [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub).

Aggiungere le intestazioni seguenti alla richiesta:

* Content-Type: application/json
* Authorization : SharedAccessSignature 58...
  * Per istruzioni sulla generazione di `SharedAccessSignature` , vedere [Autenticazione dell'API REST Gestione API di Azure](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Specificare il corpo della richiesta usando il modello seguente:

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
> Per altri possibili codici restituiti e i relativi motivi, vedere [Creare un logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Per informazioni su come eseguire altre operazioni, ad esempio elencare, aggiornare o eliminare, vedere la documentazione relativa all'entità [Logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity).
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Configurare i criteri log-to-eventhubs

Dopo la configurazione del logger in Gestione API, è possibile configurare i criteri log-to-eventhubs in modo da registrare gli eventi desiderati. I criteri log-to-eventhubs possono essere usati nella sezione relativa ai criteri in ingresso o in quella relativa ai criteri in uscita.

1. Passare all'istanza di Gestione API.
2. Selezionare la scheda API.
3. Selezionare l'API a cui si vuole aggiungere il criterio. In questo esempio si aggiungono dei criteri all'**API Echo** nel prodotto **Unlimited**.
4. Selezionare **Tutte le operazioni**.
5. Nella parte superiore della schermata selezionare la scheda Progettazione.
6. Nella finestra Elaborazione in ingresso o Elaborazione in uscita fare clic sul triangolo (accanto alla matita).
7. Selezionare l'editor di codice. Per altre informazioni, vedere [Come configurare o modificare i criteri](set-edit-policies.md).
8. Posizionare il cursore nella sezione relativa ai criteri `inbound` o `outbound`.
9. Nella finestra di destra selezionare **Criteri avanzati** > **Log to EventHub** (Accedi a hub eventi). In questo modo viene inserito il modello di istruzione del criterio `log-to-eventhub`.

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

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
