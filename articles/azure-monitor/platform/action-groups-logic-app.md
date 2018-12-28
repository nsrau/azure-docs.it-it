---
title: Come attivare azioni complesse con avvisi di Monitoraggio di Azure
description: Informazioni su come creare un'azione dell'app per la logica per elaborare gli avvisi di Monitoraggio di Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 0ab496b884506b013c6a4abb2dff1e1feff4fc72
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342987"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Come attivare azioni complesse con avvisi di Monitoraggio di Azure

Questo articolo illustra come configurare e attivare un'app per la logica per creare una conversazione in Microsoft Teams quando viene generato un avviso.

## <a name="overview"></a>Panoramica
Quando si attiva un avviso di Monitoraggio di Azure, viene chiamato un [gruppo di azioni](../../azure-monitor/platform/action-groups.md). I gruppi di azioni consentono di attivare una o più azioni per notificare un avviso agli utenti e correggere il problema.

Il processo generale è il seguente:

-   Creare l'app per la logica per il rispettivo tipo di avviso.

-   Importare lo schema per il rispettivo tipo di avviso nell'app per la logica.

-   Definire il comportamento dell'app per la logica.

-   Copiare l'endpoint HTTP dell'app per la logica in un gruppo di azioni di Azure.

Il processo è simile se si vuole che l'app per la logica esegua un'azione diversa.

## <a name="create-an-activity-log-alert-administrative"></a>Creare un avviso del log attività: Amministrativo

1.  Nel portale di Azure selezionare **Crea una risorsa** nell'angolo superiore sinistro.

2.  Cercare e selezionare  **App per la logica** e quindi selezionare  **Crea**.

3.  Assegnare un **Nome** all'app per la logica, scegliere un **Gruppo di risorse** e così via.

    ![Creare un'app per la logica](media/action-groups-logic-app/create-logic-app-dialog.png "Creare un'app per la logica")

4.  Fare clic su **Crea** per creare l'app per la logica. Un messaggio popup indica che l'app per la logica è stata creata. Selezionare **Launch Resource** (Avvia risorsa) per aprire **Progettazione app per la logica**.

5.  Selezionare il trigger: **Alla ricezione di una richiesta HTTP**.

    ![Trigger dell'app per la logica](media/action-groups-logic-app/logic-app-triggers.png "Trigger dell'app per la logica")

6.  Selezionare **Modifica** per modificare il trigger di richiesta HTTP.

    ![Trigger di richiesta HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Trigger di richiesta HTTP")

7.  Selezionare **Usare il payload di esempio per generare lo schema**.

    ![Usare un esempio di payload](media/action-groups-logic-app/use-sample-payload-button.png "Usare un esempio di payload")

8.  Copiare e incollare lo schema di esempio seguente nella finestra di dialogo:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. In **Progettazione app per la logica** viene visualizzata una finestra popup per ricordare che la richiesta inviata all'app per la logica deve impostare l'intestazione **Content-Type** su **application/json**. Chiudere la finestra popup. L'avviso di Monitoraggio di Azure imposta l'intestazione.

    ![Impostare l'intestazione Content-Type](media/action-groups-logic-app/content-type-header.png "Impostare l'intestazione Content-Type")

10. Selezionare **+** **Nuovo passaggio** e quindi selezionare **Aggiungi un'azione**.

    ![Aggiungere un'azione](media/action-groups-logic-app/add-action.png "Aggiungere un'azione")

11. Cercare e selezionare il connettore Microsoft Teams. Scegliere l'azione **Microsoft Teams – Pubblica messaggio**.

    ![Azioni di Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Azioni di Microsoft Teams")

12. Configurare l'azione di Microsoft Teams. In **Progettazione app per la logica** viene chiesto di autenticarsi con l'account Office 365. Scegliere l'**ID team** e l'**ID canale** per l'invio del messaggio.

13. Configurare il messaggio usando una combinazione di testo statico e riferimenti ai \<campi\> nel contenuto dinamico. Copiare e incollare il testo seguente nel campo **Messaggio**:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Cercare e sostituire quindi i \<campi\> con tag di contenuto dinamico dello stesso nome.

    > [!NOTE]
    > Sono disponibili due campi dinamici denominati **stato**. Aggiungere entrambi questi campi al messaggio. Usare il campo che si trova nell'elenco delle proprietà **activityLog** ed eliminare l'altro campo. Passare il cursore sul campo **stato** per visualizzare il riferimento completo del campo, come mostrato nella schermata seguente:

    ![Azione Microsoft Teams: Inserisci un messaggio](media/action-groups-logic-app/teams-action-post-message.png "azione Microsoft Teams: Inserisci un messaggio")

14. Nella parte superiore di **Progettazione app per la logica**, selezionare **Salva** per salvare l'app per la logica.

15. Aprire il gruppo di azioni esistente e aggiungere un'azione per fare riferimento all'app per la logica. Se non si dispone di un gruppo di azioni esistente, vedere [Creare e gestire gruppi di azioni nel portale di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) per crearne uno. Non dimenticare di salvare le modifiche.

    ![Aggiornare il gruppo di azioni](media/action-groups-logic-app/update-action-group.png "Aggiornare il gruppo di azioni")

Quando un avviso richiama successivamente il gruppo di azioni, viene chiamata l'app per la logica.

## <a name="create-a-service-health-alert"></a>Creare un avviso di integrità dei servizi

Le voci di integrità dei servizi di Azure fanno parte del log attività. Il processo di creazione dell'avviso è simile alla [creazione di un avviso del log attività](#create-an-activity-log-alert-administrative), ma con alcune differenze:

- I passaggi da 1 a 7 sono gli stessi.
- Nel passaggio 8 usare lo schema di esempio seguente per il trigger della richiesta HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  I passaggi 9 e 10 sono gli stessi.
-  Per i passaggi da 11 a 14, eseguire le operazioni seguenti:

   1. Selezionare **+** **Nuovo passaggio** e quindi scegliere **Aggiungi una condizione**. Impostare le condizioni seguenti in modo che l'app per la logica venga eseguita solo quando i dati di input corrispondono ai valori seguenti.  Quando si immette il valore di versione nella casella di testo, racchiuderlo tra virgolette ("0.1.1") per assicurarsi che venga valutato come stringa e non come tipo numerico.  Il sistema non visualizza le virgolette se si torna alla pagina ma il codice sottostante mantiene il tipo di stringa.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Condizione di payload di integrità dei servizi"](media/action-groups-logic-app/service-health-payload-condition.png "Condizione di payload di integrità dei servizi")

   1. Nella condizione **È true** seguire le istruzioni nei passaggi da 11 a 13 in [Creare un avviso del log attività](#create-an-activity-log-alert-administrative) per aggiungere l'azione Microsoft Teams.

   1. Definire il messaggio usando una combinazione di HTML e contenuto dinamico. Copiare e incollare il contenuto seguente nel campo **Messaggio**. Sostituire i campi `[incidentType]`, `[trackingID]`, `[title]` e `[communication]` con tag di contenuto dinamico con lo stesso nome:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Azione pubblicazione con condizione true di integrità dei servizi"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Azione pubblicazione con condizione true di integrità dei servizi")

   1. Per la condizione **È false** specificare un messaggio utile:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Azione pubblicazione con condizione false di integrità dei servizi"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Azione pubblicazione con condizione false di integrità dei servizi")

- Il passaggio 15 è lo stesso. Seguire le istruzioni per salvare l'app per la logica e aggiornare il gruppo di azioni.

## <a name="create-a-metric-alert"></a>Creare un avviso per la metrica

Il processo di creazione di un avviso per la metrica è simile alla [creazione di un avviso del log attività](#create-an-activity-log-alert-administrative), ma con alcune differenze:

- I passaggi da 1 a 7 sono gli stessi.
- Nel passaggio 8 usare lo schema di esempio seguente per il trigger della richiesta HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

- I passaggi 9 e 10 sono gli stessi.
- Per i passaggi da 11 a 14, eseguire le operazioni seguenti:

   1. Selezionare **+** **Nuovo passaggio** e quindi scegliere **Aggiungi una condizione**. Impostare le condizioni seguenti in modo che l'app per la logica venga eseguita solo quando i dati di input corrispondono ai valori seguenti. Quando si immette il valore di versione nella casella di testo, racchiuderlo tra virgolette ("2.0") per assicurarsi che venga valutato come stringa e non come tipo numerico.  Il sistema non visualizza le virgolette se si torna alla pagina ma il codice sottostante mantiene il tipo di stringa. 
       - `schemaId == AzureMonitorMetricAlert`
       - `version == "2.0"`
       
       !["Condizione di payload dell'avviso per la metrica"](media/action-groups-logic-app/metric-alert-payload-condition.png "Condizione di payload dell'avviso per la metrica")

   1. Nella condizione **È true** aggiungere un ciclo **For each** e l'azione Microsoft Teams. Definire il messaggio usando una combinazione di HTML e contenuto dinamico.

       !["Azione pubblicazione con condizione true dell'avviso per la metrica"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Azione pubblicazione con condizione true dell'avviso per la metrica")

   1. Nella condizione **È false** definire un'azione Microsoft Teams per comunicare che l'avviso per la metrica non corrisponde alle aspettative dell'app per la logica. Includere il payload JSON. Si noti come fare riferimento al contenuto dinamico `triggerBody` nell'espressione `json()`.

       !["Azione pubblicazione con condizione false dell'avviso per la metrica"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Azione pubblicazione con condizione false dell'avviso per la metrica")

- Il passaggio 15 è lo stesso. Seguire le istruzioni per salvare l'app per la logica e aggiornare il gruppo di azioni.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Chiamata di altre applicazioni oltre Microsoft Teams
App per la logica dispone di una serie di connettori diversi che consentono di attivare azioni in un'ampia gamma di applicazioni e database. Slack, SQL Server, Oracle e Salesforce sono solo alcuni esempi. Per altre informazioni sui connettori, vedere [Connettori di App per la logica](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Passaggi successivi
* Leggere una [panoramica degli avvisi del log attività](../../azure-monitor/platform/alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi quando viene inviata una notifica sull'integrità dei servizi di Azure](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Altre informazioni sui [gruppi di azione](../../azure-monitor/platform/action-groups.md).
