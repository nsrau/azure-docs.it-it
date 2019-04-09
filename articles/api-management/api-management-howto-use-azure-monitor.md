---
title: Monitorare le API pubblicate con Gestione API di Azure | Microsoft Docs
description: Eseguire le procedure di questa esercitazione per monitorare le API con Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 6a4e9a0c33b227716227213e94948df430566065
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622196"
---
# <a name="monitor-published-apis"></a>Monitorare le API pubblicate

Con Monitoraggio di Azure è possibile eseguire operazioni di visualizzazione, query, instradamento, archiviazione, e quindi adottare le misure appropriate, sulle metriche o sui log provenienti da risorse di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Visualizzare log di attività
> * Visualizzare i log di diagnostica
> * Visualizzare le metriche dell'API 
> * Configurare una regola di avviso quando l'API riceve delle chiamate non autorizzate

Il video seguente illustra come monitorare Gestione API usando Monitoraggio di Azure. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'argomento di avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Visualizzare le metriche delle API

Gestione API genera le metriche ogni minuto in modo da ottenere una visibilità quasi in tempo reale dello stato e dell'integrità delle API. Di seguito è riportato un riepilogo delle metriche disponibili:

* Capacità (anteprima): consente di decidere se eseguire l'aggiornamento o il downgrade dei servizi di Gestione API. La metrica viene emessa ogni minuto e riflette la capacità del gateway nel momento in cui viene eseguito il report. La metrica è compresa tra 0 e 100 e viene calcolata in base alle risorse gateway, come utilizzo della CPU e della memoria.
* Totale richieste gateway: numero di richieste di API nel periodo. 
* Richieste gateway riuscite: numero di richieste di API che hanno ricevuto codici di risposta HTTP con esito positivo, tra cui 304, 307 e qualsiasi valore minore di 301, ad esempio 200.
* Richieste gateway non riuscite: numero di richieste di API che hanno ricevuto codici di risposta HTTP con esito negativo, tra cui 400 e qualsiasi valore maggiore di 500.
* Richieste gateway non autorizzate: numero di richieste di API che hanno ricevuto codici di risposta HTTP tra cui 401, 403 e 429.
* Altre richieste gateway: numero di richieste di API che hanno ricevuto codici di risposta HTTP che non appartengono a una delle categorie precedenti, ad esempio, 418.

![Grafico metriche](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Per accedere alle metriche:

1. Selezionare **Metriche** dal menu nella parte inferiore della pagina.

    ![Metriche](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. Dall'elenco a discesa selezionare le metriche desiderate. Ad esempio, **Richieste gateway riuscite**. È anche possibile aggiungere più metriche al grafico.
3. Il grafico mostra il numero totale di chiamate API con esito positivo.

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurare una regola di avviso per le richieste non autorizzate

È possibile configurare un avviso basato sulle metriche e sui log attività. Monitoraggio di Azure consente di configurare un avviso in modo che, se attivato, esegua queste operazioni:

* Inviare una notifica via posta elettronica
* Chiamare un webhook
* Richiamare un'app per la logica di Azure

Per configurare gli avvisi:

1. Selezionare **Avvisi** sulla barra dei menu nella parte inferiore della pagina.

    ![alerts](./media/api-management-azure-monitor/alert-menu-item.png)

2. Fare clic su **Nuova regola di avviso** per questo avviso.
3. Fare clic su **Aggiungi condizione**.
4. Selezionare **Metrica** nell'elenco a discesa Tipo di segnale.
5. Selezionare **Richieste del gateway non autorizzate** come segnale da monitorare.

    ![alerts](./media/api-management-azure-monitor/signal-type.png)

6. Nella visualizzazione **Configura logica dei segnali** specificare una soglia superata la quale dovrà essere attivato un avviso e fare clic su **Fatto**.

    ![alerts](./media/api-management-azure-monitor/threshold.png)

7. Selezionare un gruppo di azioni esistente o crearne uno nuovo. Nell'esempio seguente viene inviato un messaggio di posta elettronica agli amministratori. 

    ![alerts](./media/api-management-azure-monitor/action-details.png)

8. Specificare un nome e una descrizione per la regola di avviso, quindi scegliere il livello di gravità. 
9. Selezionare **Crea regola di avviso**.
10. Provare a chiamare l'API Conference senza una chiave API. Verrà attivato l'avviso e verrà inviato un messaggio di posta elettronica agli amministratori. 

## <a name="activity-logs"></a>Log attività

I log attività offrono informazioni dettagliate sulle operazioni eseguite nei servizi Gestione API. L'uso del log attività consente di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sui servizi Gestione API.

> [!NOTE]
> I log attività non includono le operazioni di lettura (GET) né le operazioni eseguite nel portale di Azure o usando le API di gestione originali.

È possibile accedere ai log attività del servizio Gestione API o ai log di tutte le risorse di Azure in Monitoraggio di Azure. 

![Log attività](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Per visualizzare i log di attività:

1. Selezionare l'istanza del servizio Gestione API.
2. Fare clic su **Log attività**.

    ![Log attività](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Selezionare l'ambito di filtro desiderato e fare clic su **Applica**.

## <a name="diagnostic-logs"></a>Log di diagnostica

I log di diagnostica offrono informazioni dettagliate sulle operazioni e gli errori importanti per il controllo e per la risoluzione dei problemi. I log di diagnostica differiscono dai log attività. I log attività offrono informazioni approfondite sulle operazioni eseguite nelle risorse di Azure. I log di diagnostica forniscono informazioni dettagliate sulle operazioni eseguite dalla risorsa.

Per configurare i log di diagnostica:

1. Selezionare l'istanza del servizio Gestione API.
2. Fare clic su **Impostazioni di diagnostica**.

    ![Log di diagnostica](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Fare clic su **Attiva diagnostica**. I log di diagnostica possono essere archiviati con le metriche in un account di archiviazione, trasmessi a un hub eventi o inviati ai log di Monitoraggio di Azure. 

Attualmente Gestione API offre log di diagnostica (in batch orari) sulle singole richieste API, dove ogni voce ha la struttura seguente:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Proprietà  | Type | DESCRIZIONE |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | True se la richiesta HTTP è stata completata con codice di stato risposta compreso nell'intervallo 2xx o 3xx |
| time | datetime | Timestamp di ricezione della richiesta HTTP dal gateway |
| operationName | stringa | Valore costante 'Microsoft.ApiManagement/GatewayLogs' |
| category | stringa | Valore costante 'GatewayLogs' |
| durationMs | numero intero | Numero di millisecondi dal momento in cui il gateway ha ricevuto la richiesta al momento dell'invio della risposta completa |
| callerIpAddress | stringa | Indirizzo IP del chiamante gateway immediato (può essere un intermediario) |
| correlationId | stringa | Identificatore richiesta http univoco assegnato da Gestione API |
| location | stringa | Nome dell'area di Azure in cui si trovava il gateway che ha elaborato la richiesta |
| httpStatusCodeCategory | stringa | Categoria di codice di stato della risposta HTTP: richiesta riuscita (minore o uguale a 301 oppure 304 o 307), richiesta non autorizzata (401, 403, 429), errore (400, valore compreso tra 500 e 600), altro |
| resourceId | stringa | ID della risorsa di Gestione API /SUBSCRIPTIONS/\<subscription>/RESOURCEGROUPS/\<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/\<name> |
| properties | object | Proprietà della richiesta corrente |
| statico | stringa | Metodo HTTP della richiesta in ingresso |
| URL | stringa | URL della richiesta in ingresso |
| clientProtocol | stringa | Versione del protocollo HTTP della richiesta in ingresso |
| responseCode | numero intero | Codice di stato della risposta HTTP inviata a un client |
| backendMethod | stringa | Metodo HTTP della richiesta inviata a un back-end |
| backendUrl | stringa | URL della richiesta inviata a un back-end |
| backendResponseCode | numero intero | Codice della risposta HTTP ricevuta da un back-end |
| backendProtocol | stringa | Versione del protocollo HTTP della richiesta inviata a un back-end | 
| requestSize | numero intero | Numero di byte ricevuti da un client durante l'elaborazione della richiesta | 
| responseSize | numero intero | Numero di byte inviati a un client durante l'elaborazione della richiesta | 
| cache | stringa | Stato di intervento della cache di Gestione API nell'elaborazione della richiesta (hit, miss, none) | 
| cacheTime | numero intero | Numero di millisecondi impiegati complessivamente per l'I/O della cache di Gestione API (connessione, invio e ricezione byte) | 
| backendTime | numero intero | Numero di millisecondi impiegati complessivamente per l'I/O del back-end (connessione, invio e ricezione byte) | 
| clientTime | numero intero | Numero di millisecondi impiegati complessivamente per l'I/O del client (connessione, invio e ricezione byte) | 
| apiId | stringa | Identificatore dell'entità API per la richiesta corrente | 
| operationId | stringa | Identificatore dell'entità operazione per la richiesta corrente | 
| productId | stringa | Identificatore dell'entità prodotto per la richiesta corrente | 
| userId | stringa | Identificatore dell'entità utente per la richiesta corrente | 
| apimSubscriptionId | stringa | Identificatore dell'entità sottoscrizione per la richiesta corrente | 
| backendId | stringa | Identificatore dell'entità back-end per la richiesta corrente | 
| LastError | object | Errore di elaborazione dell'ultima richiesta | 
| elapsed | numero intero | Numero di millisecondi trascorsi da quando il gateway ha ricevuto la richiesta fino al momento in cui si è verificato l'errore | 
| una sezione source | stringa | Nome del criterio o del gestore interno di elaborazione che ha causato l'errore | 
| scope | stringa | Ambito del documento dei criteri contenente il criterio che ha causato l'errore | 
| section | stringa | Sezione del documento dei criteri contenente il criterio che ha causato l'errore | 
| reason | stringa | Motivo dell'errore | 
| Message | stringa | Messaggio di errore | 

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Visualizzare log di attività
> * Visualizzare i log di diagnostica
> * Visualizzare le metriche dell'API
> * Configurare una regola di avviso quando l'API riceve delle chiamate non autorizzate

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Tracciare le chiamate](api-management-howto-api-inspector.md)
