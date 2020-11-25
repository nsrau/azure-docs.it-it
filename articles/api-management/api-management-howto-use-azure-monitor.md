---
title: 'Esercitazione: Monitorare le API pubblicate in Gestione API di Azure | Microsoft Docs'
description: Seguire i passaggi di questa esercitazione per informazioni su come usare metriche, avvisi, log attività e log delle risorse per monitorare le API in Gestione API di Azure.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 2317e61111c3ad328e8f112e7d9567f3f5d47990
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997045"
---
# <a name="tutorial-monitor-published-apis"></a>Esercitazione: Monitorare le API pubblicate

Con Monitoraggio di Azure è possibile eseguire operazioni di visualizzazione, query, instradamento, archiviazione, e quindi adottare le misure appropriate, sulle metriche o sui log provenienti dal servizio Gestione API.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Visualizzare le metriche dell'API 
> * Configurare una regola di avviso 
> * Visualizzare log di attività
> * Abilitare e visualizzare i log delle risorse

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).
+ Completare anche l'esercitazione seguente: [Importare e pubblicare la prima API](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Visualizzare le metriche delle API

Gestione API genera le [metriche](../azure-monitor/platform/data-platform-metrics.md) ogni minuto in modo da ottenere una visibilità near real-time dello stato e dell'integrità delle API. Di seguito sono riportate le due metriche usate più di frequente. Per un elenco di tutte le metriche disponibili, vedere [Metriche supportate](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice).

* **Capacità**: consente di decidere se eseguire l'aggiornamento o il downgrade dei servizi di Gestione API. La metrica viene emessa ogni minuto e riflette la capacità del gateway nel momento in cui viene eseguito il report. La metrica è compresa tra 0 e 100 e viene calcolata in base alle risorse gateway, come utilizzo della CPU e della memoria.
* **Richieste**: consente di analizzare il traffico dell'API che transitano attraverso i servizi di Gestione API. La metrica viene emessa al minuto e segnala il numero di richieste del gateway con dimensioni, inclusi i codici di risposta, la posizione, il nome host e gli errori. 

> [!IMPORTANT]
> Le metriche seguenti sono deprecate a partire da maggio 2019 e verranno ritirate ad agosto 2023: totale richieste gateway, richieste gateway riuscite, richieste gateway non autorizzate, richieste gateway non riuscite, altre richieste gateway. Eseguire la migrazione alla metrica Richieste che fornisce funzionalità equivalenti.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="Screenshot delle metriche nella panoramica di Gestione API":::

Per accedere alle metriche:

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API. Nella pagina **Panoramica** esaminare le metriche principali per le API.
1. Per esaminare le metriche in dettaglio, selezionare **Metriche** dal menu nella parte inferiore della pagina.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Screenshot della voce Metriche nel menu Monitoraggio":::

1. Dall'elenco a discesa selezionare le metriche desiderate. Ad esempio, **Richieste**. 
1. Il grafico mostra il numero totale di chiamate API.
1. È possibile filtrare il grafico usando le dimensioni della metrica **Richieste**. Ad esempio, selezionare **Aggiungi filtro**, quindi **Backend Response Code Category** (Categoria codice di risposta back-end) e immettere 500 come valore. Il grafico mostra ora il numero di richieste non riuscite nel back-end dell'API.   

## <a name="set-up-an-alert-rule"></a>Configurare una regola di avviso 

È possibile ricevere gli [avvisi](../azure-monitor/platform/alerts-metric-overview.md) in base alle metriche e ai log attività. Monitoraggio di Azure consente di [configurare un avviso](../azure-monitor/platform/alerts-metric.md) che quando attivato eseguirà le operazioni seguenti:

* Inviare una notifica via posta elettronica
* Chiamare un webhook
* Richiamare un'app per la logica di Azure

Per configurare una regola di avviso di esempio in base a una metrica della richiesta:

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. Selezionare **Avvisi** sulla barra dei menu nella parte inferiore della pagina.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Screenshot dell'opzione Avvisi nel menu Monitoraggio":::

1. Selezionare **+ Nuova regola di avviso**.
1. Nella finestra **Crea regola di avviso** fare clic su **Seleziona condizione**.
1. Nella finestra **Configura logica dei segnali**:
    1. Selezionare **Metriche** per **Tipo segnale**.
    1. Selezionare **Richieste** per **Nome segnale**.
    1. In **Dividi per dimensioni**, in **Nome dimensione** selezionare **Gateway Response Code Category** (Categoria codice di risposta del gateway).
    1. In **Valori di dimensione** selezionare **4xx** per gli errori client come le richieste non autorizzate o non valide.
    1. In **Logica avvisi** specificare una soglia superata la quale dovrà essere attivato l'avviso e fare clic su **Fine**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Screenshot della finestra Configura logica dei segnali":::

1. Selezionare un gruppo di azioni esistente o crearne uno nuovo. Nell'esempio seguente viene creato un nuovo gruppo di azioni. Verrà inviato un messaggio di posta elettronica di notifica all'indirizzo admin@contoso.com. 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Screenshot delle notifiche per il nuovo gruppo di azioni":::

1. Specificare un nome e una descrizione per la regola di avviso e selezionare il livello di gravità. 
1. Selezionare **Crea regola di avviso**.
1. A questo punto, testare la regola di avviso chiamando l'API Conference senza una chiave API. Ad esempio:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    Verrà attivato un avviso in base al periodo di valutazione e verrà inviato un messaggio di posta elettronica all'indirizzo admin@contoso.com. 

    Gli avvisi vengono visualizzati anche nella pagina **Avvisi** per l'istanza di Gestione API.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Screenshot degli avvisi nel portale":::

## <a name="activity-logs"></a>Log attività

I log attività offrono informazioni dettagliate sulle operazioni eseguite nei servizi Gestione API. L'uso del log attività consente di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sui servizi Gestione API.

> [!NOTE]
> I log attività non includono le operazioni di lettura (GET) né le operazioni eseguite nel portale di Azure o usando le API di gestione originali.

È possibile accedere ai log attività del servizio Gestione API o ai log di tutte le risorse di Azure in Monitoraggio di Azure. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Screenshot del log attività nel portale":::

Per visualizzare il log attività:

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.

1. Selezionare **Log attività**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Screenshot della voce Log attività nel menu Monitoraggio":::
1. Selezionare l'ambito di filtro desiderato e fare clic su **Applica**.

## <a name="resource-logs"></a>Log risorse

I log risorse offrono informazioni dettagliate sulle operazioni e gli errori importanti per il controllo e per la risoluzione dei problemi. I log risorse differiscono dai log attività. Il log attività fornisce informazioni dettagliate sulle operazioni eseguite nelle risorse di Azure. I log risorse offrono dati analitici sulle operazioni eseguite dalla risorsa.

Per configurare i log risorse:

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
2. Selezionare **Impostazioni di diagnostica**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Screenshot della voce Impostazioni di diagnostica nel menu Monitoraggio":::

1. Fare clic su **+ Aggiungi impostazione di diagnostica**.
1. Selezionare i log o le metriche da raccogliere.

   È possibile archiviare i log delle risorse con le metriche in un account di archiviazione, trasmetterli in streaming a un hub eventi o inviarli a un'area di lavoro Log Analytics. 

Per altre informazioni, vedere [Creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](../azure-monitor/platform/diagnostic-settings.md).

## <a name="view-diagnostic-data-in-azure-monitor"></a>Visualizzare i dati di diagnostica in Monitoraggio di Azure

Se si abilita la raccolta dei log o delle metriche del gateway in un'area di lavoro Log Analytics, potrebbero essere necessari alcuni minuti prima che i dati vengano visualizzati in Monitoraggio di Azure. Per visualizzare i dati:

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. Selezionare **Log** dal menu nella parte inferiore della pagina.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Screenshot della voce Log nel menu Monitoraggio":::

Eseguire query per visualizzare i dati. Sono disponibili numerose [query di esempio](../azure-monitor/log-query/saved-queries.md) oppure eseguire una query personalizzata. Ad esempio, la query seguente recupera le ultime 24 ore di dati dalla tabella GatewayLogs:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

Per altre informazioni sull'utilizzo dei log delle risorse per Gestione API, vedere:

* [Introduzione a Log Analytics in Monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md) oppure provare l'[ambiente demo di Log Analytics](https://portal.loganalytics.io/demo).

* [Panoramica delle query su log in Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).

Il codice JSON seguente indica una voce di esempio in GatewayLogs per una richiesta API riuscita. Per informazioni dettagliate, vedere le [informazioni di riferimento sullo schema](gateway-log-schema-reference.md). 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Visualizzare le metriche dell'API
> * Configurare una regola di avviso 
> * Visualizzare log di attività
> * Abilitare e visualizzare i log delle risorse


Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Tracciare le chiamate](api-management-howto-api-inspector.md)
