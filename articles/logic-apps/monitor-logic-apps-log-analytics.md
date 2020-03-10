---
title: Monitorare le app per la logica usando i log di monitoraggio di Azure
description: Risolvere i problemi relativi alle app per la logica configurando i log di monitoraggio di Azure e raccogliendo i dati di diagnostica
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381901"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Configurare i log di monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di Azure

Per ottenere informazioni di debug più complete sulle app per la logica in fase di esecuzione, è possibile configurare e usare i [log di monitoraggio di Azure](../azure-monitor/platform/data-platform-logs.md) per registrare e archiviare le informazioni relative a eventi e dati di runtime, ad esempio eventi trigger, eventi di esecuzione ed eventi di azione in un'area di [lavoro log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). [Monitoraggio di Azure](../azure-monitor/overview.md) consente di monitorare gli ambienti cloud e locali in modo da mantenere la disponibilità e le prestazioni più semplici. Usando i log di monitoraggio di Azure, è possibile creare [query di log](../azure-monitor/log-query/log-query-overview.md) che consentono di raccogliere ed esaminare queste informazioni. È anche possibile [usare questi dati di diagnostica con altri servizi di Azure](#extend-data), ad esempio archiviazione di Azure e hub eventi di Azure.

Per configurare la registrazione per l'app per la logica, è possibile [abilitare log Analytics quando si crea l'app](#logging-for-new-logic-apps)per la logica oppure è possibile [installare la soluzione di gestione delle app](#install-management-solution) per la logica nell'area di lavoro log Analytics per le app per la logica esistenti. Questa soluzione fornisce informazioni aggregate per le esecuzioni dell'app per la logica e include dettagli specifici, ad esempio stato, tempo di esecuzione, stato di reinvio e ID correlazione. Quindi, per abilitare la registrazione e la creazione di query per queste informazioni, [configurare i log di monitoraggio di Azure](#set-up-resource-logs).

Questo articolo illustra come abilitare Log Analytics quando si creano app per la logica, come installare e configurare la soluzione di gestione delle app per la logica e come impostare e creare query per i log di monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisites

Prima di iniziare, è necessario disporre di un' [area di lavoro log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). Se non si dispone di un'area di lavoro, vedere la pagina relativa [alla creazione di un'area di lavoro log Analytics](../azure-monitor/learn/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Abilitare Log Analytics per le nuove app per la logica

È possibile attivare Log Analytics quando si crea l'app per la logica.

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro in cui vengono fornite le informazioni per creare l'app per la logica, attenersi alla procedura seguente:

   1. In **log Analytics**selezionare **on**.

   1. Dall'elenco **log Analytics area di lavoro** selezionare l'area di lavoro a cui si vogliono inviare i dati dalle esecuzioni dell'app per la logica.

      ![Specificare le informazioni sull'app per la logica](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Al termine di questo passaggio, Azure crea l'app per la logica, che ora è associata all'area di lavoro Log Analytics personale. In questo passaggio viene inoltre installata automaticamente la soluzione di gestione delle app per la logica nell'area di lavoro.

1. Al termine, selezionare **Crea**.

1. Dopo aver eseguito l'app per la logica, per visualizzare le esecuzioni dell'app per la logica [continuare con questi passaggi](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Installare la soluzione di gestione delle app per la logica

Se è stato attivato Log Analytics quando è stata creata l'app per la logica, ignorare questo passaggio. È già installata la soluzione di gestione delle app per la logica nell'area di lavoro Log Analytics.

1. Nella casella di ricerca del [portale di Azure](https://portal.azure.com)immettere `log analytics workspaces`, quindi selezionare **log Analytics aree di lavoro**.

   ![Selezionare "aree di lavoro Log Analytics"](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. In **log Analytics aree di lavoro**selezionare l'area di lavoro.

   ![Selezionare l'area di lavoro Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. Nel riquadro **Panoramica** , in introduzione **a log Analytics** > configurare le **soluzioni di monitoraggio**, selezionare **Visualizza soluzioni**.

   ![Nel riquadro Panoramica selezionare "Visualizza soluzioni".](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. In **Panoramica**selezionare **Aggiungi**.

   ![Nel riquadro Panoramica aggiungere una nuova soluzione](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Dopo l'apertura del **Marketplace** , nella casella di ricerca immettere `logic apps management`e selezionare **gestione app**per la logica.

   ![Da Marketplace selezionare "gestione app per la logica"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. Nel riquadro Descrizione soluzione selezionare **Crea**.

   ![Selezionare "Crea" per aggiungere la soluzione di gestione delle app per la logica](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Verificare e confermare l'area di lavoro Log Analytics in cui si vuole installare la soluzione e selezionare di nuovo **Crea** .

   ![Selezionare "Crea" per "gestione app per la logica"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Quando Azure distribuisce la soluzione al gruppo di risorse di Azure che contiene l'area di lavoro Log Analytics, la soluzione viene visualizzata nel riquadro di riepilogo dell'area di lavoro.

   ![Riquadro di riepilogo area di lavoro](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Configurare i log di Monitoraggio di Azure

Quando si archiviano informazioni sugli eventi e i dati di runtime nei [log di monitoraggio di Azure](../azure-monitor/platform/data-platform-logs.md), è possibile creare query di [log](../azure-monitor/log-query/log-query-overview.md) che consentono di trovare e rivedere tali informazioni.

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'app per la logica.

1. Nel menu dell'app per la logica, in **monitoraggio**, selezionare **impostazioni di diagnostica** > **Aggiungi impostazione di diagnostica**.

   ![In "monitoraggio" selezionare "impostazioni di diagnostica" > "Aggiungi impostazione di diagnostica".](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Per creare l'impostazione, attenersi alla seguente procedura:

   1. Consente di specificare un nome per l'impostazione.

   1. Selezionare **Invia a Log Analytics**.

   1. Per **Subscription (sottoscrizione**) selezionare la sottoscrizione di Azure associata all'area di lavoro log Analytics.

   1. Per **log Analytics area di lavoro**, selezionare l'area di lavoro che si desidera utilizzare.

   1. In **log**selezionare la categoria **WorkflowRuntime** , che specifica la categoria di eventi che si desidera registrare.

   1. Per selezionare tutte le metriche, in **metrica**selezionare **AllMetrics**.

   1. Al termine, selezionare **Salva**.

   Ad esempio:

   ![Selezionare l'area di lavoro Log Analytics e i dati per la registrazione](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Visualizza lo stato delle esecuzioni dell'app per la logica

Dopo l'esecuzione dell'app per la logica, è possibile visualizzare i dati relativi a tali esecuzioni nell'area di lavoro Log Analytics.

1. Nella [portale di Azure](https://portal.azure.com)individuare e aprire l'area di lavoro log Analytics.

1. Nel menu dell'area di lavoro selezionare **Riepilogo area di lavoro** > **gestione app**per la logica.

   ![Stato e numero di esecuzioni dell'app per la logica](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Se il riquadro Gestione app per la logica non visualizza immediatamente i risultati dopo un'esecuzione, provare a selezionare **Aggiorna** o attendere un breve periodo di tempo prima di riprovare.

   In questo riquadro le esecuzioni dell'app per la logica vengono raggruppate in base al nome o allo stato di esecuzione. Questa pagina mostra anche i dettagli sugli errori di azioni o trigger per le esecuzioni delle app per la logica.

   ![Riepilogo dello stato per le esecuzioni dell'app per la logica](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Per visualizzare tutte le esecuzioni per un'app per la logica o uno stato specifico, selezionare la riga per l'app per la logica o lo stato.

   Nell'esempio seguente sono visualizzate tutte le esecuzioni per un'app per la logica specifica:

   ![Visualizzare le esecuzioni e lo stato delle app per la logica](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Per le azioni in cui si [impostano le proprietà rilevate](#extend-data), è anche possibile visualizzare tali proprietà selezionando **Visualizza** nella colonna **proprietà rilevate** . Per cercare le proprietà rilevate, usare il filtro di colonna.

   ![Visualizzare le proprietà rilevate per un'app per la logica](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Le proprietà rilevate o gli eventi completati potrebbero subire ritardi di 10-15 minuti prima che vengano visualizzati nell'area di lavoro Log Analytics.
   > Inoltre, la funzionalità di **reinvio** in questa pagina non è attualmente disponibile.

1. Per filtrare i risultati, è possibile eseguire il filtro lato client e lato server.

   * **Filtro lato client**: per ogni colonna, selezionare i filtri desiderati, ad esempio:

     ![Esempi di filtri di colonna](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Filtro lato server**: per selezionare un intervallo di tempo specifico o per limitare il numero di esecuzioni visualizzate, usare il controllo ambito nella parte superiore della pagina. Per impostazione predefinita, vengono visualizzati contemporaneamente solo 1.000 record.

     ![Modificare l'intervallo di tempo](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Per visualizzare tutte le azioni e i relativi dettagli per un'esecuzione specifica, selezionare la riga per l'esecuzione di un'app per la logica.

   Di seguito è riportato un esempio in cui vengono illustrate tutte le azioni e i trigger per un'esecuzione di app per la logica specifica:

   ![Visualizzare le azioni per un'esecuzione dell'app per la logica](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Inviare i dati di diagnostica ad archiviazione di Azure e hub eventi di Azure

Insieme ai log di monitoraggio di Azure, è possibile estendere il modo in cui si usano i dati di diagnostica dell'app per la logica con altri servizi di Azure, ad esempio:

* [Archiviare i log delle risorse di Azure nell'account di archiviazione](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Trasmettere i log della piattaforma Azure a hub eventi di Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

È quindi possibile eseguire il monitoraggio in tempo reale usando i dati di telemetria e l'analisi da altri servizi, ad esempio [Analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../azure-monitor/platform/powerbi.md), Ad esempio:

* [Trasmettere i dati da Hub eventi ad Analisi di flusso](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizzare i dati di streaming con Analisi di flusso e creare un dashboard di analisi in tempo reale in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

In base alle posizioni in cui si vogliono inviare i dati di diagnostica, assicurarsi di [creare prima un account di archiviazione di Azure](../storage/common/storage-create-storage-account.md) o [creare un hub eventi di Azure](../event-hubs/event-hubs-create.md). È quindi possibile selezionare le destinazioni in cui si desidera inviare i dati. I periodi di conservazione si applicano solo quando si usa un account di archiviazione.

![Inviare i dati all'hub di eventi o all'account di archiviazione di Azure](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Eventi di diagnostica di monitoraggio di Azure

Ogni evento di diagnostica ha dettagli sull'app per la logica e sull'evento stesso, ad esempio lo stato, l'ora di inizio, l'ora di fine e così via. Per configurare il monitoraggio, il rilevamento e la registrazione a livello di codice, è possibile usare queste informazioni con l' [API REST per app per la logica di Azure](https://docs.microsoft.com/rest/api/logic) e l' [API REST per monitoraggio di Azure](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows). È anche possibile usare le proprietà `clientTrackingId` e `trackedProperties`, che vengono visualizzate in 

* `clientTrackingId`: se non specificata, Azure genera automaticamente questo ID e correla gli eventi in un'esecuzione dell'app per la logica, inclusi eventuali flussi di lavoro annidati chiamati dall'app per la logica. È possibile specificare manualmente questo ID in un trigger passando un'intestazione `x-ms-client-tracking-id` con il valore ID personalizzato nella richiesta del trigger. È possibile usare un trigger di richiesta, un trigger HTTP o un trigger webhook.

* `trackedProperties`: per tenere traccia degli input o degli output nei dati di diagnostica, è possibile aggiungere una sezione di `trackedProperties` a un'azione usando la finestra di progettazione dell'app per la logica o direttamente nella definizione JSON dell'app per la logica. Le proprietà rilevate possono tenere traccia solo di singoli input e output di azioni, ma è possibile usare le proprietà `correlation` degli eventi per correlare più azioni in un'esecuzione. Per tenere traccia di più di una proprietà, una o più proprietà, aggiungere la `trackedProperties` sezione e le proprietà desiderate alla definizione dell'azione.

  Di seguito è riportato un esempio che illustra come la definizione di azione della **variabile Initialize** includa le proprietà rilevate dall'input dell'azione in cui l'input è una matrice, non un record.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  Questo esempio Mostra più proprietà rilevate:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

Questo esempio illustra il modo in cui l'evento `ActionCompleted` include gli attributi `clientTrackingId` e `trackedProperties`:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
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
         "workflowId": "<logic-app-workflow-ID>",
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
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Creazione di query di monitoraggio e rilevamento](../logic-apps/create-monitoring-tracking-queries.md)
* [Monitorare i messaggi B2B con i log di monitoraggio di Azure](../logic-apps/monitor-b2b-messages-log-analytics.md)
