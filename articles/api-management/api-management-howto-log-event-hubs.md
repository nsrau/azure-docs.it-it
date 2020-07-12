---
title: Come registrare gli eventi in Hub eventi di Azure in Gestione API di Azure | Microsoft Docs
description: Informazioni su come registrare eventi nell'Hub eventi di Azure in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: f594d4467e64ead40ff3c26aaf3e3a44cb673a98
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250295"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Come registrare eventi nell'Hub eventi di Azure in Gestione API di Azure
Hub di eventi di Azure è un servizio di ingresso dati altamente scalabile che può inserire milioni di eventi al secondo in modo che è possibile elaborare e analizzare enormi quantità di dati generati per i dispositivi connessi e le applicazioni. Gli hub di eventi fungono da "porta principale" per una pipeline di eventi e una volta che i dati vengono raccolti in un hub di eventi, possono essere trasformati e archiviati con qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Gli hub di eventi separano la produzione di un flusso di eventi dal consumo di questi eventi, in modo che i consumer di eventi può accedere agli eventi in base a una pianificazione.

Questo articolo è complementare al video su come [Integrare la Gestione API di Azure con gli Hub di eventi](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descrive come registrare gli eventi di gestione API mediante gli hub di eventi di Azure.

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure

Per informazioni dettagliate su come creare un hub eventi e ottenere le stringhe di connessione necessarie per inviare e ricevere eventi da e verso l'hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il portale di Azure](../event-hubs/event-hubs-create.md).

## <a name="create-an-api-management-logger"></a>Creare un logger di Gestione API
Dopo aver creato un hub eventi, è necessario configurare un [Logger](/rest/api/apimanagement/2019-12-01/logger) nel servizio Gestione API, in modo che possa registrare eventi nell'hub eventi.

I logger di Gestione API vengono configurati mediante l' [API REST Gestione API](https://aka.ms/apimapi). Per esempi di richiesta dettagliati, vedere [come creare logger](/rest/api/apimanagement/2019-12-01/logger/createorupdate).

## <a name="configure-log-to-eventhub-policies"></a>Configurare i criteri log-to-eventhub

Dopo la configurazione del logger in gestione API, è possibile configurare i criteri log-to-eventhub per registrare gli eventi desiderati. Il criterio log-to-eventhub può essere usato nella sezione dei criteri in ingresso o nei criteri in uscita.

1. Passare all'istanza di Gestione API.
2. Selezionare la scheda API.
3. Selezionare l'API a cui si vuole aggiungere il criterio. In questo esempio si aggiungono dei criteri all'**API Echo** nel prodotto **Unlimited**.
4. Selezionare **Tutte le operazioni**.
5. Nella parte superiore della schermata selezionare la scheda Progettazione.
6. Nella finestra Elaborazione in ingresso o Elaborazione in uscita fare clic sul triangolo (accanto alla matita).
7. Selezionare l'editor di codice. Per altre informazioni, vedere [Come configurare o modificare i criteri](set-edit-policies.md).
8. Posizionare il cursore nella sezione relativa ai criteri `inbound` o `outbound`.
9. Nella finestra a destra selezionare **criteri avanzati**  >  **Accedi a EventHub**. In questo modo viene inserito il modello di istruzione del criterio `log-to-eventhub`.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
Sostituire `logger-id` con il valore usato per `{loggerId}` nell'URL della richiesta per creare il logger nel passaggio precedente.

È possibile usare qualsiasi espressione che restituisce una stringa come valore dell'elemento `log-to-eventhub` . In questo esempio viene registrata una stringa in formato JSON che contiene la data e l'ora, il nome del servizio, l'ID della richiesta, l'indirizzo IP della richiesta e il nome dell'operazione.

Fare clic su **Salva** per salvare la configurazione aggiornata dei criteri. Il criterio risulta attivo immediatamente dopo il salvataggio e gli eventi vengono registrati nell'hub eventi designato.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>Visualizzare in anteprima il log in hub eventi usando analisi di flusso di Azure

È possibile visualizzare in anteprima il log in hub eventi usando le [query di analisi di flusso di Azure](../event-hubs/process-data-azure-stream-analytics.md). 

1. Nella portale di Azure passare all'hub eventi a cui il logger invia gli eventi. 
2. In **funzionalità**selezionare la scheda **elabora dati** .
3. Nella scheda **Abilita informazioni dettagliate in tempo reale da eventi** selezionare **Esplora**.
4. Dovrebbe essere possibile visualizzare l'anteprima del log nella scheda **Anteprima input** . Se i dati visualizzati non sono aggiornati, selezionare **Aggiorna** per visualizzare gli eventi più recenti.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'Hub eventi di Azure
  * [Introduzione all'Hub eventi](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ricevere messaggi con EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md)
* Altre informazioni sull'integrazione di Gestione API e Hub eventi
  * [Informazioni di riferimento per l'entità logger](/rest/api/apimanagement/2019-12-01/logger)
  * [Informazioni di riferimento per i criteri log-to-event](./api-management-advanced-policies.md#log-to-eventhub)
  * [Monitorare le API con Gestione API di Azure, Hub eventi e Moesif](api-management-log-to-eventhub-sample.md)  
* Altre informazioni sull'[integrazione con Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
