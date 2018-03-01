---
title: Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi | Microsoft Docs
description: Descrizione della messaggistica del bus di servizio e dell'integrazione in Griglia di eventi
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: bf771428505081cb60ca4417f87a4f6c2afbd25d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Panoramica dell'integrazione del bus di servizio di Azure in Griglia di eventi di Azure

Il bus di servizio Azure offre ora una nuova integrazione in Griglia di eventi di Azure. Il principale scenario abilitato da questa funzionalità è la possibilità, in caso di code e sottoscrizioni del bus di servizio con un volume ridotto di messaggi, di non avere un ricevitore che esegue costantemente il polling dei messaggi. Il bus di servizio può ora inviare eventi a Griglia di eventi di Azure quando una coda o una sottoscrizione contiene messaggi e non sono presenti ricevitori. È possibile creare sottoscrizioni di Griglia di eventi di Azure per gli spazi dei nomi del bus di servizio e quindi restare in ascolto di tali eventi e reagire a essi avviando un ricevitore. Questa funzionalità consente di usare il bus di servizio in modelli di programmazione reattivi.

Per abilitare questa funzionalità, sono necessari gli elementi seguenti:

* Spazio dei nomi Premium del bus di servizio di Azure con almeno una coda o un argomento del bus di servizio con almeno una sottoscrizione.
* Accesso di tipo Collaboratore allo spazio dei nomi del bus di servizio di Azure.
* Sottoscrizione di Griglia di eventi di Azure per lo spazio dei nomi del bus di servizio. Questa sottoscrizione riceve la notifica da Griglia di eventi di Azure che segnala la presenza di messaggi da prelevare. I sottoscrittori tipici, che elaborano quindi i messaggi, potrebbero essere app per la logica, funzioni di Azure o un webhook che contatta un'app Web. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Verificare di avere l'accesso di tipo Collaboratore

Passare allo spazio dei nomi del bus di servizio e selezionare "Controllo di accesso (IAM)" come illustrato di seguito:

![1][]

### <a name="events-and-event-schemas"></a>Eventi e schemi di eventi

Il bus di servizio di Azure attualmente invia eventi per due scenari.

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

Usa inoltre i [meccanismi di autenticazione](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication) e la sicurezza standard di Griglia di eventi di Azure.

Per altri dettagli sugli schemi di eventi di Griglia di eventi, fare clic su [questo](https://docs.microsoft.com/en-us/azure/event-grid/event-schema) collegamento.

#### <a name="active-messages-available-event"></a>Evento per disponibilità di messaggi attivi

Questo evento viene generato se in una coda o una sottoscrizione sono presenti messaggi attivi e nessun ricevitore è in ascolto.

Di seguito è riportato lo schema per questo evento:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>Evento per disponibilità di messaggi inseriti nella coda di messaggi non recapitabili

Viene generato almeno un evento per ogni coda di messaggi non recapitabili che contiene messaggi e non ha alcun ricevitore attivo.

Di seguito è riportato lo schema per questo evento:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>Numero di eventi emessi e frequenza di emissione

Se si hanno più code/argomenti e sottoscrizioni nello spazio dei nomi, vengono generati almeno un evento per coda e un evento per sottoscrizione. Gli eventi vengono emessi immediatamente se arriva un nuovo messaggio e non sono presenti messaggi nell'entità del bus di servizio oppure ogni due minuti, a meno che il bus di servizio di Azure non rilevi un ricevitore attivo. L'esplorazione di messaggi non interrompe gli eventi.

Per impostazione predefinita, il bus di servizio di Azure emette eventi per tutte le entità nello spazio dei nomi. Se si vogliono ottenere eventi solo per entità specifiche, vedere la sezione seguente sui filtri.

### <a name="filtering-limiting-from-where-you-get-events"></a>Applicazione di filtri per limitare la provenienza degli eventi

Se si vogliono ottenere eventi solo per una coda o una sottoscrizione nello spazio dei nomi, ad esempio, è possibile usare i filtri "Inizia con" o "Termina con" offerti da Griglia di eventi di Azure, che in alcune interfacce sono denominati filtri per "prefisso" e "suffisso". Se si vogliono ottenere eventi per più code e sottoscrizioni ma non per tutte, è possibile creare più sottoscrizioni di Griglia di eventi di Azure diverse e specificare un filtro per ognuna.

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>Come creare sottoscrizioni di Griglia di eventi di Azure per gli spazi dei nomi del bus di servizio

Per creare sottoscrizioni di Griglia di eventi per gli spazi dei nomi del bus di servizio sono disponibili tre diversi modi.

* [Il portale di Azure](#portal-instructions)
* [Interfaccia della riga di comando di Azure](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>Istruzioni per il portale

Per creare una nuova sottoscrizione di Griglia di eventi di Azure, passare allo spazio dei nomi nel portale di Azure e selezionare il pannello Griglia di eventi. Fare clic su "+ Sottoscrizione di eventi". Di seguito è illustrato uno spazio dei nomi che ha già alcune sottoscrizioni di Griglia di eventi.

![20][]

Lo screenshot seguente mostra un esempio di come creare una sottoscrizione per una funzione di Azure o un webhook senza filtri specifici:

![21][]

## <a name="azure-cli-instructions"></a>Istruzioni per l'interfaccia della riga di comando di Azure

Verificare prima di tutto che sia installata almeno la versione 2.0 dell'interfaccia della riga di comando di Azure. È possibile scaricare il programma di installazione qui. Premere quindi "Windows + X" e aprire una nuova console di PowerShell con autorizzazioni di amministratore. In alternativa è anche possibile usare una shell dei comandi nel portale di Azure.

Eseguire questo codice:

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Istruzioni per PowerShell

Verificare che sia installato Azure PowerShell, disponibile qui. Premere quindi "Windows + X" e aprire una nuova console di PowerShell con autorizzazioni di amministratore. In alternativa è anche possibile usare una shell dei comandi nel portale di Azure.

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

A questo punto è possibile esplorare le altre opzioni di configurazione oppure [testare che gli eventi vengano trasmessi](#test-that-events-are-flowing).

## <a name="next-steps"></a>Passaggi successivi

* [Esempi](service-bus-to-event-grid-integration-example.md) relativi al bus di servizio e Griglia di eventi.
* Altre informazioni su [Griglia di eventi di Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Altre informazioni su [Funzioni di Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Altre informazioni su [App per la logica di Azure](https://docs.microsoft.com/en-us/azure/logic-apps/).
* Altre informazioni sul [bus di servizio di Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png