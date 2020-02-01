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
ms.openlocfilehash: 2f07f6a27e78ee4df8c64a09918758d02c28c6d4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898781"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Come registrare eventi nell'Hub eventi di Azure in Gestione API di Azure
Hub di eventi di Azure è un servizio di ingresso dati altamente scalabile che può inserire milioni di eventi al secondo in modo che è possibile elaborare e analizzare enormi quantità di dati generati per i dispositivi connessi e le applicazioni. Gli hub di eventi fungono da "porta principale" per una pipeline di eventi e una volta che i dati vengono raccolti in un hub di eventi, possono essere trasformati e archiviati con qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Gli hub di eventi separano la produzione di un flusso di eventi dal consumo di questi eventi, in modo che i consumer di eventi può accedere agli eventi in base a una pianificazione.

Questo articolo è complementare al video su come [Integrare la Gestione API di Azure con gli Hub di eventi](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descrive come registrare gli eventi di gestione API mediante gli hub di eventi di Azure.

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure

Per informazioni dettagliate su come creare un hub eventi e ottenere le stringhe di connessione necessarie per inviare e ricevere eventi da e verso l'hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il portale di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Creare un logger di Gestione API
Dopo aver creato un hub eventi, è necessario configurare un [Logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger) nel servizio Gestione API, in modo che possa registrare eventi nell'hub eventi.

I logger di Gestione API vengono configurati mediante l' [API REST Gestione API](https://aka.ms/apimapi). Per esempi di richiesta dettagliati, vedere [come creare logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate).

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
Sostituire `logger-id` con il valore usato per `{new logger name}` nell'URL per creare il logger nel passaggio precedente.

È possibile usare qualsiasi espressione che restituisce una stringa come valore dell'elemento `log-to-eventhub` . In questo esempio viene registrata una stringa contenente data e ora, nome del servizio, ID richiesta, indirizzo IP della richiesta e nome dell'operazione.

Fare clic su **Salva** per salvare la configurazione aggiornata dei criteri. Il criterio risulta attivo immediatamente dopo il salvataggio e gli eventi vengono registrati nell'hub eventi designato.

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'Hub eventi di Azure
  * [Introduzione all'Hub eventi](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Ricevere messaggi con EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guida alla programmazione di Hub eventi](../event-hubs/event-hubs-programming-guide.md)
* Altre informazioni sull'integrazione di Gestione API e Hub eventi
  * [Informazioni di riferimento per l'entità logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger)
  * [Informazioni di riferimento per i criteri log-to-event](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
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
