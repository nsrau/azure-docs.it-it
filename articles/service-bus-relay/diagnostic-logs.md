---
title: Log di diagnostica per Connessioni ibride
description: Questo articolo fornisce una panoramica di tutti i log di attività e diagnostica disponibili per il servizio di inoltro di Azure.
services: service-bus-messaging
author: spelluru
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.date: 04/27/2020
ms.author: spelluru
ms.openlocfilehash: b7ac5f1da70352115bf05df1a61120f46a85ec5d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783461"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Abilitare i log di diagnostica per il Connessioni ibride di inoltro di Azure
Quando si inizia a usare il Connessioni ibride di inoltro di Azure, potrebbe essere necessario monitorare come e quando i listener e i mittenti vengono aperti e chiusi e come vengono creati i Connessioni ibride e vengono inviati i messaggi. Questo articolo fornisce una panoramica dei log di attività e diagnostica forniti dal servizio di inoltro di Azure. 

È possibile visualizzare due tipi di log per il inoltro di Azure:

- [Log attività](../azure-monitor/platform/platform-logs-overview.md): questi log contengono informazioni sulle operazioni eseguite sullo spazio dei nomi nell'portale di Azure o tramite Azure Resource Manager modello. Questi log sono sempre abilitati. Ad esempio: "crea o Aggiorna spazio dei nomi", "crea o Aggiorna connessione ibrida". 
- [Log di diagnostica](../azure-monitor/platform/platform-logs-overview.md): è possibile configurare i log di diagnostica per una visualizzazione più completa di tutto ciò che accade con le operazioni e le azioni eseguite sullo spazio dei nomi tramite l'API o tramite il linguaggio SDK.

## <a name="view-activity-logs"></a>Visualizzare log di attività
Per visualizzare i log attività per lo spazio dei nomi di inoltro di Azure, passare alla pagina **log attività** nel portale di Azure.

![Inoltro di Azure-log attività](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Abilitare i log di diagnostica

> [!NOTE]
> I log di diagnostica sono disponibili solo per Connessioni ibride e non per l'inoltro Windows Communication Foundation (WCF).

Per abilitare i log di diagnostica, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com)passare allo spazio dei nomi di inoltro di Azure e quindi selezionare **impostazioni di diagnostica**in **monitoraggio**.
1. Nella pagina **impostazioni di diagnostica** selezionare **Aggiungi impostazione di diagnostica**.  

   ![Collegamento "Aggiungi impostazione di diagnostica"](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Per configurare le impostazioni di diagnostica, seguire questa procedura:
    1. Nella casella **nome** immettere un nome per le impostazioni di diagnostica.  
    2. Selezionare **HybridConnectionsEvent** per il tipo di log. 
    3. Selezionare una delle tre **destinazioni** seguenti per i log di diagnostica:  
        1. Se si seleziona **archivia in un account di archiviazione**, configurare l'account di archiviazione in cui verranno archiviati i log di diagnostica.  
        2. Se si seleziona **flusso in un hub eventi**, configurare l'hub eventi in cui si desidera trasmettere i log di diagnostica.
        3. Se si seleziona **Invia a log Analytics**, specificare l'istanza di log Analytics a cui verrà inviata la diagnostica.  

        ![Impostazioni di diagnostica di esempio](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni.

Le nuove impostazioni hanno effetto in circa 10 minuti. I log vengono visualizzati nella destinazione di archiviazione configurata, nel riquadro **log di diagnostica** . Per altre informazioni sulla configurazione delle impostazioni di diagnostica, vedere [Panoramica dei log di diagnostica di Azure](../azure-monitor/platform/diagnostic-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Schema per gli eventi di connessioni ibride
Le stringhe JSON del registro eventi di connessioni ibride includono gli elementi elencati nella tabella seguente:

| Nome | Description |
| ------- | ------- |
| ResourceId | ID della risorsa Azure Resource Manager |
| ActivityId | ID interno, usato per identificare l'operazione specificata. Può anche essere noto come "TrackingId" |
| Endpoint | Indirizzo della risorsa di inoltro |
| OperationName | Tipo di Connessioni ibride operazione da registrare |
| EventTimeString | Timestamp UTC del record di log |
| Message | Messaggio dettagliato dell'evento |
| Categoria | Categoria dell'evento. Attualmente, è disponibile solo `HybridConnectionsEvents`. 


## <a name="sample-hybrid-connections-event"></a>Evento di connessioni ibride di esempio
Ecco un evento di connessioni ibride di esempio in formato JSON. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Eventi e operazioni acquisiti nei log di diagnostica

| Operazione | Descrizione | 
| --------- | ----------- | 
| AuthorizationFailed | Autorizzazione non riuscita.|
| InvalidSasToken | Token SAS non valido. | 
| ListenerAcceptingConnection | Il listener sta accettando la connessione. |
| ListenerAcceptingConnectionTimeout | Si è verificato il timeout del listener che accetta la connessione. |
| ListenerAcceptingHttpRequestFailed | Il listener che accetta la richiesta HTTP non è riuscito a causa di un'eccezione. |
| ListenerAcceptingRequestTimeout | Timeout della richiesta di accettazione del listener. |  
| ListenerClosingFromExpiredToken | Il listener sta per essere chiuso perché il token di sicurezza è scaduto. | 
| ListenerRejectedConnection | Il listener ha rifiutato la connessione. |
| ListenerReturningHttpResponse | Il listener sta restituendo una risposta HTTP. |  
| ListenerReturningHttpResponseFailed | Il listener restituisce una risposta HTTP con un codice di errore. | 
 ListenerSentHttpResponse | Il servizio di inoltro ha ricevuto una risposta HTTP dal listener. | 
| ListenerUnregistered | È stata annullata la registrazione del listener. | 
| ListenerUnresponsive | Il listener non risponde quando viene restituita una risposta. | 
| MessageSendingToListener | Messaggio inviato al listener. |
| MessageSentToListener | Il messaggio viene inviato al listener. | 
| NewListenerRegistered | Nuovo listener registrato. |
| NewSenderRegistering | È in corso la registrazione del nuovo mittente. | 
| ProcessingRequestFailed | L'elaborazione di un'operazione di connessione ibrida non è riuscita. | 
| SenderConnectionClosed | La connessione del mittente è stata chiusa. |
| SenderListenerConnectionEstablished | Connessione stabilita con il mittente e il listener. |
| SenderSentHttpRequest | Il mittente ha inviato una richiesta HTTP. | 


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul relè di Azure, vedere:

* [Introduzione al relè di Azure](relay-what-is-it.md)
* [Introduzione alle connessioni ibride di inoltro](relay-hybrid-connections-dotnet-get-started.md)
