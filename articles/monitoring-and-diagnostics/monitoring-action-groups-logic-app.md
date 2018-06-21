---
title: Come attivare azioni complesse con gli avvisi di Monitoraggio di Azure e i gruppi di azioni
description: Informazioni su come creare un'azione dell'app per la logica per elaborare gli avvisi di Monitoraggio di Azure.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: eafb2bcf0175190748c9dd020051cbebfcaee1fd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263885"
---
# <a name="create-a-logic-app-action"></a>Creare un'azione dell'app per la logica
## <a name="overview"></a>Panoramica ##
Quando si attiva un avviso di Monitoraggio di Azure, viene chiamato un [gruppo di azioni](monitoring-action-groups.md). I gruppi di azioni consentono di attivare una o più azioni per notificare l'avviso agli utenti ed eventualmente correggere il problema.

Questo articolo illustra come configurare e attivare un'app per la logica per creare una conversazione in Microsoft Teams quando viene generato un avviso.

Il processo generale è il seguente:

-   Creare l'app per la logica per il rispettivo tipo di avviso

-   Importare lo schema per il rispettivo tipo di avviso nell'app per la logica

-   Definire il comportamento dell'app per la logica

-   Copiare l'endpoint HTTP dell'app per la logica in un gruppo di azioni di Azure

Il processo è simile se si vuole che l'app per la logica esegua un'azione diversa.

## <a name="create-an-activity-log-alert--administrative"></a>Creare un avviso del log attività - Amministrativo

1.  Fare clic sul pulsante **Crea una risorsa** visualizzato nell'angolo in alto a sinistra nel portale di Azure.

2.  Cercare e selezionare **App per la logica**. Selezionare il pulsante **Create** .

3.  Assegnare un nome all'app per la logica, scegliere un gruppo di risorse e così via.

    ![Finestra di dialogo Crea app per la logica](media/monitoring-action-groups/create-logic-app-dialog.png "Finestra di dialogo Crea app per la logica")

4.  Fare clic sul pulsante Crea per creare l'app per la logica. Verrà visualizzato un popup al termine della creazione dell'app per la logica. Fare clic sul pulsante Launch Resource (Avvia risorsa) per aprire Progettazione app per la logica.

5.  Selezionare il trigger **Alla ricezione di una richiesta HTTP**.

    ![Trigger dell'app per la logica](media/monitoring-action-groups/logic-app-triggers.png "Trigger dell'app per la logica")

6.  Selezionare **Modifica** per modificare il trigger di richiesta HTTP.

    ![Forma del trigger di richiesta HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "Forma del trigger di richiesta HTTP")

7.  Selezionare **Usare il payload di esempio per generare lo schema**.

    ![Pulsante Usare il payload di esempio](media/monitoring-action-groups/use-sample-payload-button.png "Pulsante Usare il payload di esempio")

8.  Copiare e incollare lo schema di esempio seguente nella finestra di dialogo.

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

9. In Progettazione app per la logica verrà visualizzata una nota per ricordare all'utente che la richiesta inviata all'app per la logica deve impostare l'intestazione Content-Type su application/json. Andare avanti e ignorare la finestra di dialogo. L'impostazione verrà configurata correttamente dall'avviso di Monitoraggio di Azure.

    ![Intestazione Content-Type](media/monitoring-action-groups/content-type-header.png "Intestazione Content-Type")

10. Selezionare **+ Nuovo passaggio** e quindi scegliere **Aggiungi un'azione**.

    ![Aggiungi un'azione](media/monitoring-action-groups/add-action.png "Aggiungi un'azione")

11. Cercare e selezionare il connettore Microsoft Teams. Scegliere l'azione **Microsoft Teams – Pubblica messaggio**.

    ![Azioni di Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Azioni di Microsoft Teams")

12. Configurare l'azione di Microsoft Teams. In Progettazione app per la logica verrà chiesto di eseguire l'autenticazione con l'account di Office 365. Scegliere l'**ID team** e l'**ID canale** per l'invio del messaggio.

13. Configurare il **Messaggio** usando una combinazione di testo statico e riferimenti ai \<campi\> nel contenuto dinamico. Tagliare e incollare il testo seguente nel campo Messaggio.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Quindi cercare e sostituire i \<campi\> con i tag di contenuto dinamico con lo stesso nome.

    **[NOTA!]** Sono disponibili due campi dinamici denominati **status**. Aggiungere entrambi i campi di stato al messaggio. Usare quello nel contenitore delle proprietà activityLog ed eliminare l'altro. Se si passa il puntatore sul campo **status**, verrà visualizzato il riferimento di campo completo come illustrato nello screenshot.

    ![Azione di Teams - Pubblica messaggio](media/monitoring-action-groups/teams-action-post-message.png "Azione di Teams - Pubblica messaggio")

14. Salvare l'app per la logica facendo clic sul pulsante Salva nella parte superiore della finestra di progettazione.

15. Fare clic sulla forma della richiesta HTTP per espanderla. Copiare l'URL HTTP POST.

    ![URL HTTP POST](media/monitoring-action-groups/http-post-url.png "URL HTTP POST")

16. Aprire il gruppo di azioni esistente e aggiungere un'azione per fare riferimento all'app per la logica. Se non è presente un gruppo di azioni, vedere <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> per crearne uno. Non dimenticare di salvare le modifiche.

    ![Aggiornare il gruppo di azioni](media/monitoring-action-groups/update-action-group.png "Aggiornare il gruppo di azioni")

Quando un avviso richiama successivamente il gruppo di azioni, viene chiamata l'app per la logica.

## <a name="create-a-service-health-alert"></a>Creare un avviso di integrità dei servizi

Le voci di integrità dei servizi fanno parte del log attività, pertanto il processo è analogo con le modifiche seguenti.

1.  I passaggi da 1 a 7 sono gli stessi.
2.  Usare lo schema di esempio seguente per il trigger HTTP al passaggio 8.

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

3.  I passaggi 9-10 sono gli stessi.
4.  Dal passaggio 11 in avanti usare la procedura seguente.
5.  Fare clic sul pulsante **+ Nuovo passaggio** e scegliere **Aggiungi una condizione**. Impostare le condizioni seguenti per assicurarsi che l'app per la logica venga eseguita solo quando i dati di input corrispondono a questi valori:
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - version == 0.1.1

    !["Condizione di payload di integrità dei servizi"](media/monitoring-action-groups/service-health-payload-condition.png "Condizione di payload di integrità dei servizi")

6. Nella condizione **È true** aggiungere l'azione di Microsoft Teams usando i passaggi da 11 a 13 dell'esempio precedente.

7. Definire il messaggio usando una combinazione di HTML e [contenuto dinamico]. Copiare e incollare il contenuto riportato di seguito nel campo del messaggio. Sostituire i campi [incidentType], [trackingID], [title] e [communication] con i tag di contenuto dinamico con lo stesso nome.

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    !["Azione pubblicazione con condizione true di integrità dei servizi"](media/monitoring-action-groups/service-health-true-condition-post-action.png "Azione pubblicazione con condizione true di integrità dei servizi")

8. Per la condizione **È false** specificare un messaggio utile.

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    !["Azione pubblicazione con condizione false di integrità dei servizi"](media/monitoring-action-groups/service-health-false-condition-post-action.png "Azione pubblicazione con condizione false di integrità dei servizi")

9.  Seguire i passaggi 15–16 dell'esempio precedente per salvare l'app per la logica e aggiornare il gruppo di azioni.

## <a name="metric-alert"></a>Avviso per la metrica

1.  I passaggi da 1 a 7 sono gli stessi del primo esempio.
2.  Usare lo schema di esempio seguente per il trigger HTTP al passaggio 8.

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

3.  I passaggi 9-10 sono gli stessi.
4.  Dal passaggio 11 in avanti usare la procedura seguente.
5.  Fare clic sul pulsante **+ Nuovo passaggio** e scegliere **Aggiungi una condizione**. Impostare le condizioni seguenti per assicurarsi che l'app per la logica venga eseguita solo quando i dati di input corrispondono a questi valori:

    - schemaId == AzureMonitorMetricAlert
    - version == 2.0

    !["Condizione di payload dell'avviso per la metrica"](media/monitoring-action-groups/metric-alert-payload-condition.png "Condizione di payload dell'avviso per la metrica")

6.  Nella condizione **È true** verranno aggiunte una forma **For each** e l'azione di Microsoft Teams e il messaggio sarà definito usando una combinazione di HTML e [contenuto dinamico].

    !["Azione pubblicazione con condizione true dell'avviso per la metrica"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "Azione pubblicazione con condizione true dell'avviso per la metrica")

7.  Nella forma **È false** definire un'azione di Microsoft Teams che comunichi che l'avviso per la metrica non corrisponde alle aspettative dell'app per la logica e includere il payload JSON. Si noti come viene fatto riferimento al contenuto dinamico triggerBody nell'espressione json().

    !["Azione pubblicazione con condizione false dell'avviso per la metrica"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "Azione pubblicazione con condizione false dell'avviso per la metrica")

8.  Seguire i passaggi 15–16 del primo esempio per salvare l'app per la logica e aggiornare il gruppo di azioni.

## <a name="next-steps"></a>Passaggi successivi ##
* Leggere una [panoramica degli avvisi del log attività](monitoring-overview-alerts.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md).
* Altre informazioni sui [gruppi di azione](monitoring-action-groups.md)