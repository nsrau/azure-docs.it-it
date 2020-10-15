---
title: Log di diagnostica per le connessioni ibride
description: Questo articolo illustra tutti i log di attività e diagnostica disponibili per Inoltro di Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 980f2f7a737d3f2460c17a84c472cbf56f5eb90f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533003"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Abilitare i log di diagnostica per le connessioni ibride di Inoltro di Azure
Quando si iniziano a usare le connessioni ibride di Inoltro di Azure, potrebbe essere utile monitorare l'apertura e la chiusura di listener e mittenti, nonché la modalità di creazione delle connessioni ibride e l'invio dei messaggi. Questo articolo include una panoramica dei log di attività e diagnostica forniti dal servizio Inoltro di Azure. 

È possibile visualizzare due tipi di log per Inoltro di Azure:

- [Log attività](../azure-monitor/platform/platform-logs-overview.md): questi log contengono informazioni sulle operazioni eseguite sullo spazio dei nomi nel portale di Azure o tramite il modello di Azure Resource Manager. Questi log sono sempre abilitati. Ad esempio: "Creazione o aggiornamento dello spazio dei nomi", "Creazione o aggiornamento della connessione ibrida". 
- [Log di diagnostica](../azure-monitor/platform/platform-logs-overview.md): è possibile configurare i log di diagnostica per un quadro completo degli eventi relativi a operazioni e azioni eseguite sullo spazio dei nomi con l'API o tramite l'SDK del linguaggio.

## <a name="view-activity-logs"></a>Visualizzare log di attività
Per visualizzare i log attività per lo spazio dei nomi di Inoltro di Azure, passare alla pagina **Log attività** nel portale di Azure.

![Inoltro di Azure - Log attività](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Abilitare i log di diagnostica

> [!NOTE]
> I log di diagnostica sono disponibili solo per le connessioni ibride e non per l'inoltro WCF (Windows Communication Foundation).

Per abilitare i log di diagnostica, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) passare allo spazio dei nomi di Inoltro di Azure e quindi, in **Monitoraggio**, selezionare **Impostazioni di diagnostica**.
1. Nella pagina **Impostazioni di diagnostica** selezionare **Aggiungi impostazione di diagnostica**.  

   ![Collegamento "Aggiungi impostazione di diagnostica"](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Per configurare le impostazioni di diagnostica, seguire questa procedura:
    1. Nella casella **Nome** immettere un nome per le impostazioni di diagnostica.  
    2. Selezionare **HybridConnectionsEvent** per il tipo di log. 
    3. Selezionare una delle tre **destinazioni** seguenti per i log di diagnostica:  
        1. Se si seleziona **Archivia in un account di archiviazione**, configurare l'account di archiviazione in cui verranno archiviati i log di diagnostica.  
        2. Se si seleziona **Streaming in un hub eventi**, configurare l'hub eventi a cui si vogliono trasmettere i log di diagnostica.
        3. Se si seleziona **Invia a Log Analytics**, specificare l'istanza di Log Analytics a cui verrà inviata la diagnostica.  

        ![Impostazioni di diagnostica di esempio](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Selezionare **Salva** sulla barra degli strumenti per salvare le impostazioni.

Le nuove impostazioni diventano effettive entro 10 minuti circa. I log vengono visualizzati nella destinazione di archiviazione configurata, nel riquadro **Log di diagnostica**. Per altre informazioni sulla configurazione delle impostazioni di diagnostica, vedere la [panoramica dei log di diagnostica di Azure](../azure-monitor/platform/platform-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Schema per gli eventi di connessioni ibride
Le stringhe JSON dei log degli eventi di connessioni ibride includono gli elementi elencati nella tabella seguente:

| Nome | Descrizione |
| ------- | ------- |
| ResourceId | ID della risorsa Azure Resource Manager |
| ActivityId | ID interno, usato per identificare l'operazione specificata. Può essere indicato anche come "TrackingId" |
| Endpoint | Indirizzo della risorsa di Inoltro |
| OperationName | Tipo dell'operazione delle connessioni ibride da includere nel log |
| EventTimeString | Timestamp UTC del record del log |
| Message | Messaggio dettagliato dell'evento |
| Category | Categoria dell'evento. Attualmente è disponibile solo `HybridConnectionsEvents`. 


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
| InvalidSasToken | Il token di firma di accesso condiviso non è valido. | 
| ListenerAcceptingConnection | Il listener accetta la connessione. |
| ListenerAcceptingConnectionTimeout | Si è verificato un timeout del listener che accetta la connessione. |
| ListenerAcceptingHttpRequestFailed | Si è verificato un errore del listener che accetta la richiesta HTTP a causa di un'eccezione. |
| ListenerAcceptingRequestTimeout | Si è verificato un timeout del listener che accetta la richiesta. |  
| ListenerClosingFromExpiredToken | Il listener verrà chiuso perché il token di sicurezza è scaduto. | 
| ListenerRejectedConnection | Il listener ha rifiutato la connessione. |
| ListenerReturningHttpResponse | Il listener restituisce una risposta HTTP. |  
| ListenerReturningHttpResponseFailed | Il listener restituisce una risposta HTTP con un codice errore. | 
 ListenerSentHttpResponse | Il servizio Inoltro ha ricevuto una risposta HTTP dal listener. | 
| ListenerUnregistered | La registrazione del listener è stata annullata. | 
| ListenerUnresponsive | Il listener non risponde quando viene restituita una risposta. | 
| MessageSendingToListener | Il messaggio verrà inviato al listener. |
| MessageSentToListener | Il messaggio è stato inviato al listener. | 
| NewListenerRegistered | È stato registrato un nuovo listener. |
| NewSenderRegistering | È in corso la registrazione del nuovo mittente. | 
| ProcessingRequestFailed | L'elaborazione di un'operazione di connessione ibrida non è riuscita. | 
| SenderConnectionClosed | La connessione al mittente viene chiusa. |
| SenderListenerConnectionEstablished | La connessione al mittente e al listener è stata stabilita. |
| SenderSentHttpRequest | Il mittente ha inviato una richiesta HTTP. | 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Inoltro di Azure, vedere:

* [Introduzione a Inoltro di Azure](relay-what-is-it.md)
* [Introduzione alle connessioni ibride di inoltro](relay-hybrid-connections-dotnet-get-started.md)
