---
title: 'Controllare lo stato, configurare la registrazione e ottenere avvisi: App per la logica di Azure | Microsoft Docs'
description: Monitorare lo stato e le prestazioni per le app per la logica, registrare i dati di diagnostica e configurare gli avvisi
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 4795f5728d4ce6ff21b97bc3fefd6a53e0c6a11b
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Monitorare lo stato, configurare la registrazione diagnostica e attivare gli avvisi per App per la logica di Azure

Dopo avere [creato ed eseguito un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md), è possibile controllarne la cronologia delle esecuzioni, la cronologia dei trigger, lo stato e le prestazioni. Per il monitoraggio degli eventi in tempo reale e il debug avanzato, configurare la [registrazione diagnostica](#azure-diagnostics) per l'app per la logica. In questo modo è possibile [trovare e visualizzare gli eventi](#find-events), ad esempio eventi di attivazione, eventi di esecuzione ed eventi di azione. È anche possibile usare questi [dati diagnostici con altri servizi](#extend-diagnostic-data), ad esempio Archiviazione di Azure e Hub eventi di Azure. 

Per ottenere notifiche sugli errori o su altri possibili problemi, configurare gli [avvisi](#add-azure-alerts). È ad esempio possibile creare un avviso che rileva "quando più di cinque esecuzioni in un'ora hanno esito negativo". È anche possibile configurare il monitoraggio, la verifica e la registrazione a livello di codice usando [impostazioni e proprietà degli eventi di Diagnostica di Azure](#diagnostic-event-properties).

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Visualizzare la cronologia di esecuzioni e trigger per l'app per la logica

1. Per trovare l'app per la logica nel [portale di Azure](https://portal.azure.com) scegliere **Altri servizi** dal menu principale di Azure. Nella casella di ricerca cercare "app per la logica" e scegliere **App per la logica**.

   ![Trovare l'app per la logica](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Il portale di Azure visualizza tutte le app per la logica associate alla sottoscrizione di Azure. 

2. Selezionare l'app per la logica, quindi scegliere **Panoramica**.

   Il portale di Azure visualizza la cronologia delle esecuzioni e la cronologia dei trigger per l'app per la logica, ad esempio:

   ![Cronologia delle esecuzioni e cronologia dei trigger delle app per la logica](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **Cronologia esecuzioni** visualizza tutte le esecuzioni per l'app per la logica. 
   * **Cronologia trigger** visualizza tutte le attività dei trigger per l'app per la logica.

   Per le descrizioni degli stati, vedere [Risolvere i problemi relativi all'app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

   > [!TIP]
   > Se non si trovano i dati previsti, scegliere **Aggiorna** sulla barra degli strumenti.

3. Per visualizzare i passaggi da una specifica esecuzione, selezionarla in **Cronologia esecuzioni**. 

   La vista di monitoraggio mostra ogni passaggio di tale esecuzione, ad esempio:

   ![Azioni per una specifica esecuzione](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Per ottenere altri dettagli sull'esecuzione, scegliere **Dettagli esecuzione**. Queste informazioni riepilogano i passaggi, lo stato, gli input e gli output per l'esecuzione. 

   ![Scegliere "Dettagli esecuzione"](media/logic-apps-monitor-your-logic-apps/run-details.png)

   È ad esempio possibile ottenere l'**ID di correlazione** dell'esecuzione, che potrebbe essere necessario quando si usa l'[API REST per App per la logica](https://docs.microsoft.com/rest/api/logic).

5. Per ottenere informazioni dettagliate su un passaggio specifico, scegliere il passaggio. È ora possibile esaminare dettagli come input, output ed eventuali errori verificatisi per tale passaggio, ad esempio:

   ![Dettagli del passaggio](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Tutti i dettagli ed eventi di runtime vengono crittografati nel servizio App per la logica. Vengono decrittografati solo quando un utente richiede di visualizzare i dati. È anche possibile controllare l'accesso a questi eventi con il [controllo degli accessi in base al ruolo di Azure](../active-directory/role-based-access-control-what-is.md).

6. Per ottenere informazioni dettagliate su un evento di attivazione specifico, tornare al riquadro **Panoramica**. In **Cronologia trigger** selezionare l'evento di attivazione. È ora possibile esaminare dettagli come input e output, ad esempio:

   ![Dettagli dell'output dell'evento di attivazione](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Attivare la registrazione diagnostica per l'app per la logica

Per il debug avanzato con dettagli ed eventi di runtime, è possibile configurare la registrazione diagnostica con [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics è un servizio di [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) che consente di monitorare gli ambienti cloud e locali per garantirne la disponibilità e le prestazioni. 

Prima di iniziare, è necessario avere un'area di lavoro di OMS. Informazioni su [come creare un'area di lavoro di OMS](../log-analytics/log-analytics-get-started.md).

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'app per la logica. 

2. Nel menu del pannello dell'app per la logica, in **Monitoraggio** scegliere **Diagnostica** > **Impostazioni di diagnostica**.

   ![Andare a Monitoraggio, Diagnostica, Impostazioni di diagnostica](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. In **Impostazioni di diagnostica** scegliere **Sì**.

   ![Attivare i log di diagnostica](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Selezionare ora l'area di lavoro di OMS e la categoria di eventi per la registrazione, come indicato:

   1. Selezionare **Invia a Log Analytics**. 
   2. In **Log Analytics** scegliere **Configura**. 
   3. In **Aree di lavoro OMS** selezionare l'area di lavoro di OMS da usare per la registrazione.
   4. In **Log** selezionare la categoria **WorkflowRuntime**.
   5. Scegliere l'intervallo di metrica.
   6. Al termine dell'operazione, scegliere **Salva**.

   ![Selezionare l'area di lavoro di OMS e i dati per la registrazione](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

È ora possibile trovare eventi e altri dati per eventi di attivazione, eventi di esecuzione ed eventi di azione.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Trovare eventi e dati per l'app per la logica

Per trovare e visualizzare gli eventi nell'app per la logica, ad esempio eventi di attivazione, eventi di esecuzione ed eventi di azione, seguire questa procedura.

1. Nel [portale di Azure](https://portal.azure.com) scegliere **Altri servizi**. Cercare "Log Analytics", quindi scegliere **Log Analytics**, come illustrato qui:

   ![Scegliere "Log Analytics"](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. In **Log Analytics** trovare e selezionare l'area di lavoro di OMS. 

   ![Selezionare l'area di lavoro di OMS](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. In **Gestione** scegliere **Portale di OMS**.

   ![Scegliere "Portale di OMS"](media/logic-apps-monitor-your-logic-apps/omsportalpage.png)

4. Nella home page di OMS scegliere **Ricerca log**.

   ![Nella home page di OMS scegliere "Ricerca log"](media/logic-apps-monitor-your-logic-apps/logsearch.png)

   -oppure-

   ![Dal menu di OMS scegliere "Ricerca log"](media/logic-apps-monitor-your-logic-apps/logsearch-2.png)

5. Nella casella di ricerca specificare un campo che si vuole trovare e premere **INVIO**. Quando si inizia a digitare, OMS visualizza le possibili corrispondenze e operazioni che è possibile usare. 

   Per trovare, ad esempio, i primi 10 eventi che si sono verificati, immettere e selezionare questa query di ricerca: **Category=WorkflowRuntime |top 10**

   ![Immettere la stringa di ricerca](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Altre informazioni su [come trovare i dati in Log Analytics](../log-analytics/log-analytics-log-searches.md).

6. Nella barra a sinistra della pagina dei risultati scegliere l'intervallo di tempo che si vuole visualizzare.
Per affinare la query aggiungendo un filtro, scegliere **+Aggiungi**.

   ![Scegliere l'intervallo di tempo per i risultati della query](media/logic-apps-monitor-your-logic-apps/query-results.png)

7. In **Aggiungi filtri** immettere il nome del filtro per trovare quello desiderato. Selezionare il filtro e scegliere **+Aggiungi**.

   L'esempio usa la parola "status" per trovare gli eventi non riusciti in **AzureDiagnostics**.
   Qui il filtro per **status_s** è già selezionato.

   ![Selezionare il filtro](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

8. Nella barra a sinistra selezionare il valore del filtro che si vuole usare e scegliere **Applica**.

   ![Selezionare il valore del filtro, scegliere "Applica"](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

9. Tornare ora alla query che si sta creando. La query viene aggiornata con il filtro e il valore selezionati. Vengono ora filtrati anche i risultati precedenti.

   ![Tornare alla query con i risultati filtrati](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

10. Per salvare la query per un uso futuro, scegliere **Salva**. Informazioni su [come salvare la query](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Estendere l'uso dei dati di diagnostica con altri servizi

Con Azure Log Analytics, è possibile usare in modo diverso i dati di diagnostica dell'app per la logica con altri servizi di Azure, ad esempio: 

* [Archiviare i log di diagnostica di Azure in Archiviazione di Microsoft Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Trasmettere i log di diagnostica di Azure a Hub eventi di Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

È quindi possibile eseguire il monitoraggio in tempo reale usando i dati di telemetria e l'analisi da altri servizi, ad esempio [Analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../log-analytics/log-analytics-powerbi.md), ad esempio:

* [Trasmettere i dati da Hub eventi ad Analisi di flusso](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizzare i dati di streaming con Analisi di flusso e creare un dashboard di analisi in tempo reale in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

In base alle opzioni che si vuole configurare, assicurarsi prima di tutto di [creare un account di archiviazione di Azure](../storage/common/storage-create-storage-account.md) o di [creare un hub eventi di Azure](../event-hubs/event-hubs-create.md). Selezionare quindi le opzioni per la posizione a cui si vogliono inviare i dati di diagnostica:

![Inviare i dati all'hub di eventi o all'account di archiviazione di Azure](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> I periodi di conservazione si applicano solo quando si sceglie di usare un account di archiviazione.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Configurare gli avvisi per l'app per la logica

Per monitorare metriche specifiche o le soglie superate per l'app per la logica, configurare gli [avvisi in Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). Informazioni sulle [metriche in Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Per configurare gli avvisi senza [Azure Log Analytics](../log-analytics/log-analytics-overview.md), seguire questa procedura. Per azioni e criteri di avviso più avanzati, [configurare anche Log Analytics](#azure-diagnostics).

1. Nel menu del pannello dell'app per la logica, in **Monitoraggio** scegliere **Diagnostica** > **Regole di avviso** > **Aggiungi avviso**, come illustrato qui:

   ![Aggiungere un avviso per l'app per la logica](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Nel pannello **Aggiungi una regola di avviso** creare l'avviso come indicato:

   1. In **Risorsa** selezionare l'app per la logica, se non è già selezionata. 
   2. Specificare un nome e una descrizione per l'avviso.
   3. Selezionare una **metrica** o un evento di cui si vuole tenere traccia.
   4. Selezionare una **condizione**, specificare una **soglia** per la metrica e selezionare il **periodo** per il monitoraggio di questa metrica.
   5. Scegliere se inviare posta per l'avviso. 
   6. Specificare eventuali altri indirizzi di posta elettronica a cui inviare l'avviso. 
   È anche possibile specificare l'URL di un webhook a cui si vuole inviare l'avviso.

   Questa regola, ad esempio, invia un avviso quando cinque o più esecuzioni in un'ora hanno esito negativo:

   ![Creare una regola di avviso per la metrica](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Per eseguire un'app per la logica da un avviso, è possibile includere il [trigger della richiesta](../connectors/connectors-native-reqres.md) nel flusso di lavoro, che consente di eseguire attività come le seguenti:
> 
> * [Pubblicare su Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Inviare un testo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Aggiungere un messaggio a una coda](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Impostazioni e dettagli degli eventi di Diagnostica di Azure

Ogni evento di diagnostica ha dettagli sull'app per la logica e sull'evento stesso, ad esempio lo stato, l'ora di inizio, l'ora di fine e così via. Per configurare a livello di codice il monitoraggio, la verifica e la registrazione, è possibile usare questi dettagli con l'[API REST per App per la logica di Azure](https://docs.microsoft.com/rest/api/logic) e l'[API REST per Diagnostica di Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftlogicworkflows).

L'evento `ActionCompleted`, ad esempio, ha le proprietà `clientTrackingId` e `trackedProperties` che è possibile usare per la verifica e il monitoraggio:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: se non specificata, Azure genera automaticamente questo ID e correla gli eventi in un'esecuzione dell'app per la logica, inclusi eventuali flussi di lavoro annidati chiamati dall'app per la logica. È possibile specificare manualmente questo ID da un trigger passando un'intestazione `x-ms-client-tracking-id` con il valore dell'ID personalizzato nella richiesta del trigger. È possibile usare un trigger di richiesta, un trigger HTTP o un trigger webhook.

* `trackedProperties`: per tenere traccia degli input o degli output nei dati di diagnostica, è possibile aggiungere le proprietà rilevate alle azioni nella definizione JSON dell'app per la logica. Le proprietà rilevate possono tenere traccia solo di singoli input e output di azioni, ma è possibile usare le proprietà `correlation` degli eventi per correlare più azioni in un'esecuzione.

  Per tenere traccia di una o più proprietà, aggiungere la sezione `trackedProperties` e le proprietà desiderate alla definizione dell'azione. Si supponga, ad esempio, di voler tenere traccia di dati come un "ID ordine" nella telemetria:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>Passaggi successivi

* [Creare modelli per la distribuzione delle app per la logica e la gestione dei rilasci](../logic-apps/logic-apps-create-deploy-template.md)
* [Scenari B2B con Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Monitorare i messaggi B2B](../logic-apps/logic-apps-monitor-b2b-message.md)