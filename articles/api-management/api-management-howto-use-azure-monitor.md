---
title: Monitorare le API pubblicate con Gestione API di Azure | Microsoft Docs
description: Eseguire le procedure di questa esercitazione per monitorare le API con Gestione API di Azure.
services: api-management
author: vladvino
manager: cfowler
ms.service: api-management
ms.workload: mobile
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 7080bd98bda5c4280ff7b06b235458bea0e9103c
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093583"
---
# <a name="monitor-published-apis"></a>Monitorare le API pubblicate

Con Monitoraggio di Azure è possibile eseguire operazioni di visualizzazione, query, instradamento, archiviazione, e quindi adottare le misure appropriate, sulle metriche o sui log provenienti da risorse di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Visualizzare log di attività
> * Visualizzare i log risorse
> * Visualizzare le metriche dell'API 
> * Configurare una regola di avviso quando l'API riceve delle chiamate non autorizzate

Il video seguente illustra come monitorare Gestione API usando Monitoraggio di Azure. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Visualizzare le metriche delle API

Gestione API genera le metriche ogni minuto in modo da ottenere una visibilità quasi in tempo reale dello stato e dell'integrità delle API. Di seguito sono riportate le due metriche usate più di frequente. Per un elenco di tutte le metriche disponibili, vedere [Metriche supportate](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice).

* Capacità: consente di decidere se eseguire l'aggiornamento o il downgrade dei servizi di Gestione API. La metrica viene emessa ogni minuto e riflette la capacità del gateway nel momento in cui viene eseguito il report. La metrica è compresa tra 0 e 100 e viene calcolata in base alle risorse gateway, come utilizzo della CPU e della memoria.
* Richieste: consente di analizzare il traffico API tramite i servizi di Gestione API. La metrica viene emessa al minuto e segnala il numero di richieste del gateway con dimensioni, inclusi i codici di risposta, la posizione, il nome host e gli errori. 

> [!IMPORTANT]
> Le metriche seguenti sono deprecate a partire da maggio 2019 e verranno ritirate ad agosto 2023: totale richieste gateway, richieste gateway riuscite, richieste gateway non autorizzate, richieste gateway non riuscite, altre richieste gateway. Eseguire la migrazione alla metrica Richieste che fornisce funzionalità equivalenti.

![Grafico metriche](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Per accedere alle metriche:

1. Selezionare **Metriche** dal menu nella parte inferiore della pagina.

    ![Metriche](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. Dall'elenco a discesa selezionare le metriche desiderate. Ad esempio, **Richieste** . 
3. Il grafico mostra il numero totale di chiamate API.
4. È possibile filtrare il grafico usando le dimensioni della metrica **Richieste** . Ad esempio, fare clic su **Aggiungi filtro** , scegliere **Backend Response Code** (Codice di risposta back-end) e immettere 500 come valore. Il grafico mostra ora il numero di richieste non riuscite nel back-end dell'API.   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Configurare una regola di avviso per le richieste non autorizzate

È possibile configurare un avviso basato sulle metriche e sui log attività. Monitoraggio di Azure consente di configurare un avviso in modo che, se attivato, esegua queste operazioni:

* Inviare una notifica via posta elettronica
* Chiamare un webhook
* Richiamare un'app per la logica di Azure

Per configurare gli avvisi:

1. Selezionare **Avvisi** sulla barra dei menu nella parte inferiore della pagina.

    ![Screenshot che mostra l'opzione Avvisi nel menu nella parte inferiore della pagina.](./media/api-management-azure-monitor/alert-menu-item.png)

2. Fare clic su **Nuova regola di avviso** per questo avviso.
3. Fare clic su **Aggiungi condizione** .
4. Selezionare **Metrica** nell'elenco a discesa Tipo di segnale.
5. Selezionare **Richieste del gateway non autorizzate** come segnale da monitorare.

    ![Screenshot in cui sono evidenziati il campo Tipo di segnale e il nome del segnale Richieste del gateway non autorizzate.](./media/api-management-azure-monitor/signal-type.png)

6. Nella visualizzazione **Configura logica dei segnali** specificare una soglia superata la quale dovrà essere attivato un avviso e fare clic su **Fatto** .

    ![Screenshot che mostra la visualizzazione Configura logica dei segnali.](./media/api-management-azure-monitor/threshold.png)

7. Selezionare un gruppo di azioni esistente o crearne uno nuovo. Nell'esempio seguente viene inviato un messaggio di posta elettronica agli amministratori. 

    ![alerts](./media/api-management-azure-monitor/action-details.png)

8. Specificare un nome e una descrizione per la regola di avviso, quindi scegliere il livello di gravità. 
9. Selezionare **Crea regola di avviso** .
10. Provare a chiamare l'API Conference senza una chiave API. Verrà attivato l'avviso e verrà inviato un messaggio di posta elettronica agli amministratori. 

## <a name="activity-logs"></a>Log attività

I log attività offrono informazioni dettagliate sulle operazioni eseguite nei servizi Gestione API. L'uso del log attività consente di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sui servizi Gestione API.

> [!NOTE]
> I log attività non includono le operazioni di lettura (GET) né le operazioni eseguite nel portale di Azure o usando le API di gestione originali.

È possibile accedere ai log attività del servizio Gestione API o ai log di tutte le risorse di Azure in Monitoraggio di Azure. 

![Log attività](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Per visualizzare i log di attività:

1. Selezionare l'istanza del servizio Gestione API.
2. Fare clic su **Log attività** .

    ![Log attività](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Selezionare l'ambito di filtro desiderato e fare clic su **Applica** .

## <a name="resource-logs"></a>Log risorse

I log risorse offrono informazioni dettagliate sulle operazioni e gli errori importanti per il controllo e per la risoluzione dei problemi. I log risorse differiscono dai log attività. I log attività offrono dati analitici sulle operazioni eseguite nelle risorse di Azure. I log risorse offrono dati analitici sulle operazioni eseguite dalla risorsa.

Per configurare i log risorse:

1. Selezionare l'istanza del servizio Gestione API.
2. Fare clic su **Impostazioni di diagnostica** .

    ![log risorse](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Fare clic su **Attiva diagnostica** . I log risorse possono essere archiviati con le metriche in un account di archiviazione, trasmessi a un hub eventi o inviati ai log di Monitoraggio di Azure. 

Attualmente Gestione API offre log risorse (in batch orari) sulle singole richieste API, dove ogni voce ha la struttura seguente:

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

| Proprietà  | Type | Descrizione |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | True se la richiesta HTTP è stata completata con codice di stato risposta compreso nell'intervallo 2xx o 3xx |
| time | datetime | Timestamp relativo all'avvio dell'elaborazione della richiesta nel gateway |
| operationName | string | Valore costante 'Microsoft.ApiManagement/GatewayLogs' |
| category | string | Valore costante 'GatewayLogs' |
| durationMs | integer | Numero di millisecondi dal momento in cui il gateway ha ricevuto la richiesta al momento dell'invio della risposta completa. Include clienTime, cacheTime e backendTime. |
| callerIpAddress | string | Indirizzo IP del chiamante gateway immediato (può essere un intermediario) |
| correlationId | string | Identificatore richiesta http univoco assegnato da Gestione API |
| posizione | string | Nome dell'area di Azure in cui si trovava il gateway che ha elaborato la richiesta |
| httpStatusCodeCategory | string | Categoria di codice di stato della risposta HTTP: richiesta riuscita (minore o uguale a 301 oppure 304 o 307), richiesta non autorizzata (401, 403, 429), errore (400, valore compreso tra 500 e 600), altro |
| resourceId | string | ID della risorsa di Gestione API /SUBSCRIPTIONS/\<subscription>/RESOURCEGROUPS/\<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/\<name> |
| properties | object | Proprietà della richiesta corrente |
| method | string | Metodo HTTP della richiesta in ingresso |
| url | string | URL della richiesta in ingresso |
| clientProtocol | string | Versione del protocollo HTTP della richiesta in ingresso |
| responseCode | integer | Codice di stato della risposta HTTP inviata a un client |
| backendMethod | string | Metodo HTTP della richiesta inviata a un back-end |
| backendUrl | string | URL della richiesta inviata a un back-end |
| backendResponseCode | integer | Codice della risposta HTTP ricevuta da un back-end |
| backendProtocol | string | Versione del protocollo HTTP della richiesta inviata a un back-end | 
| requestSize | integer | Numero di byte ricevuti da un client durante l'elaborazione della richiesta | 
| responseSize | integer | Numero di byte inviati a un client durante l'elaborazione della richiesta | 
| cache | string | Stato di intervento della cache di Gestione API nell'elaborazione della richiesta (hit, miss, none) | 
| cacheTime | integer | Numero di millisecondi impiegati complessivamente per l'I/O della cache di Gestione API (connessione, invio e ricezione byte) | 
| backendTime | integer | Numero di millisecondi impiegati complessivamente per l'I/O del back-end (connessione, invio e ricezione byte) | 
| clientTime | integer | Numero di millisecondi impiegati complessivamente per l'I/O del client (connessione, invio e ricezione byte) | 
| apiId | string | Identificatore dell'entità API per la richiesta corrente | 
| operationId | string | Identificatore dell'entità operazione per la richiesta corrente | 
| productId | string | Identificatore dell'entità prodotto per la richiesta corrente | 
| userId | string | Identificatore dell'entità utente per la richiesta corrente | 
| apimSubscriptionId | string | Identificatore dell'entità sottoscrizione per la richiesta corrente | 
| backendId | string | Identificatore dell'entità back-end per la richiesta corrente | 
| lastError | object | Errore di elaborazione dell'ultima richiesta | 
| elapsed | integer | Numero di millisecondi trascorsi tra la ricezione della richiesta nel gateway e il momento in cui si è verificato l'errore | 
| source | string | Nome del criterio o del gestore interno di elaborazione che ha causato l'errore | 
| scope | string | Ambito del documento dei criteri contenente il criterio che ha causato l'errore | 
| section | string | Sezione del documento dei criteri contenente il criterio che ha causato l'errore | 
| reason | string | Motivo dell'errore | 
| message | string | Messaggio di errore | 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Visualizzare log di attività
> * Visualizzare i log risorse
> * Visualizzare le metriche dell'API
> * Configurare una regola di avviso quando l'API riceve delle chiamate non autorizzate

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Tracciare le chiamate](api-management-howto-api-inspector.md)
