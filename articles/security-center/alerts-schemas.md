---
title: Schemi per gli avvisi del Centro sicurezza di AzureSchemas for the Azure Security Center alerts
description: Questo articolo descrive i diversi schemi usati dal Centro sicurezza di Azure per gli avvisi di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062958"
---
# <a name="security-alerts-schemas"></a>Schemi degli avvisi di sicurezzaSecurity alerts schemas

Gli utenti del livello standard del Centro sicurezza di Azure ricevono avvisi di sicurezza quando il Centro sicurezza rileva le minacce alle proprie risorse.

È possibile visualizzare questi avvisi di sicurezza nelle pagine di Protezione dalle minacce del Centro sicurezza di Azure o tramite strumenti esterni, ad esempio:You can view these security alerts in Azure Security Center's **Threat Protection** pages, or through external tools such as:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - SIEM nativo del cloud di Microsoft.Azure Sentinel - Microsoft's cloud-native SIEM. Sentinel Connector riceve avvisi dal Centro sicurezza di Azure e li invia all'area di lavoro di Log Analytics per Azure Sentinel.The Sentinel Connector gets alerts from Azure Security Center and sends them to the [Log Analytics workspace](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) for Azure Sentinel.
- SIEM di terze parti: usare gli strumenti di [esportazione continua](continuous-export.md) del Centro sicurezza per inviare dati agli hub eventi di [Azure.](https://docs.microsoft.com/azure/event-hubs/) Quindi integrare i dati dell'Hub eventi con un SIEM di terze parti.
- [API REST:](https://docs.microsoft.com/rest/api/securitycenter/) se si usa l'API REST per accedere agli avvisi, vedere la [documentazione dell'API degli avvisi online.](https://docs.microsoft.com/rest/api/securitycenter/alerts)

Se si utilizzano metodi a livello di codice per utilizzare gli avvisi, è necessario lo schema corretto per trovare i campi rilevanti per l'utente. Inoltre, se si esegue l'esportazione in un hub eventi o si tenta di attivare l'automazione del flusso di lavoro con connettori HTTP generici, usare gli schemi per analizzare correttamente gli oggetti JSON.

>[!IMPORTANT]
> Lo schema è leggermente diverso per ognuno di questi scenari, quindi assicurarsi di selezionare la scheda pertinente di seguito.


## <a name="the-schemas"></a>Gli schemi 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automazione del flusso di lavoro ed esportazione continua nell'hub eventi](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>JSON di esempio per gli avvisi inviati ad app per la logica, all'hub eventi e ai SIEM di terze partiSample JSON for alerts sent to Logic Apps, Event Hub, and third-party SIEMs

Di seguito è riportato lo schema degli eventi di avviso passati a:

- Istanze dell'app per la logica di Azure configurate nell'automazione del flusso di lavoro del Centro sicurezzaAzure Logic App instances that were configured in Security Center's workflow automation
- Hub eventi di Azure con la funzionalità di esportazione continua del Centro sicurezzaAzure Event Hub using Security Center's continuous export feature

Per ulteriori informazioni sulla funzionalità di automazione del flusso di lavoro, vedere [Automatizzare le risposte ad avvisi e suggerimenti.](workflow-automation.md)
Per ulteriori informazioni sull'esportazione continua, vedere [Esportare avvisi e suggerimenti](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Aree di lavoro di Azure Sentinel e Log AnalyticsAzure Sentinel and Log Analytics workspaces](#tab/schema-sentinel)

The Sentinel Connector gets alerts from Azure Security Center and sends them to the Log Analytics Workspace for Azure Sentinel. 

Per creare un caso Sentinel o un evento imprevisto usando gli avvisi del Centro sicurezza, è necessario lo schema per gli avvisi illustrati di seguito. 

Per altre informazioni su Azure Sentinel, vedere [la documentazione](https://docs.microsoft.com/azure/sentinel/).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure Activity Log](#tab/schema-activitylog)

Azure Security Center audits generated Security alerts as events in Azure Activity Log.

È possibile visualizzare gli eventi degli avvisi di sicurezza nel Registro attività cercando l'evento Attiva avviso come illustrato di seguito:You can view the security alerts events in Activity Log by searching for the Activate Alert event as shown:

[![Ricerca nell'elenco delle attività per l'evento Attiva avviso](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>JSON di esempio per gli avvisi inviati al log attività di AzureSample JSON for alerts sent to Azure Activity Log

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Il modello di dati dello schema

|Campo|Descrizione|
|----|----|
|**Canali**|Costante, "Operazione"|
|**Correlationid**|ID avviso del Centro sicurezza di AzureThe Azure Security Center alert ID|
|**Descrizione**|Descrizione dell'avviso|
|**eventDataId**|Vedere correlationId|
|**Eventname**|I sottocampi value e localizedValue contengono il nome visualizzato dell'avviso|
|**Categoria**|I sottocampi value e localizedValue sono costanti - "Security"|
|**eventTimestamp**|Timestamp UTC per la generazione dell'avvisoUTC time when the alert was generated|
|**id**|L'ID avviso completo|
|**Livello**|Costante, "Informativo"|
|**operationId (ID operazione)**|Vedere correlationId|
|**Nomeoperazione**|Il campo del valore è costante - "Microsoft.Security/locations/alerts/activate/action" e il valore localizzato sarà "Attiva avviso" (può essere localizzato in base alle impostazioni locali dell'utente)|
|**ResourceGroupName (NomeGruppoRisorsa)**|Includerà il nome del gruppo di risorse|
|**resourceProviderName**|I sottocampi value e localizedValue sono costanti - "Microsoft.Security"|
|**Resourcetype**|I sottocampi value e localizedValue sono costanti - "Microsoft.Security/locations/alerts"|
|**Resourceid**|ID risorsa di Azure completoThe fully qualified Azure resource ID|
|**status**|I sottocampi value e localizedValue sono costanti - "Active"|
|**subStatus (Statosecondario)**|I sottocampi value e localizedValue sono vuoti|
|**submissionTimestamp**|Il timestamp UTC dell'invio dell'evento al registro attività|
|**Subscriptionid**|ID sottoscrizione della risorsa compromessa|
|**Proprietà**|Un contenitore JSON di proprietà aggiuntive relative all'avviso. Questi possono cambiare da un avviso all'altro, tuttavia, i seguenti campi verranno visualizzati in tutti gli avvisi:<br>- gravità: la gravità dell'attacco<br>- compromisedEntity: il nome della risorsa compromessa<br>- remediationSteps: Serie di passaggi di correzione da eseguire<br>- intento: l'intento kill-chain dell'avviso. Le finalità possibili sono documentate nella [tabella Intenzioni](alerts-reference.md#intentions)|
|**relatedEvents**|Costante - matrice vuota|
|||





### <a name="ms-graph-api"></a>[MS Graph API](#tab/schema-graphapi)

Microsoft Graph è il gateway per i dati e l'intelligenza in Microsoft 365. Fornisce un modello di programmabilità unificato che è possibile utilizzare per accedere all'enorme quantità di dati in Office 365, Windows 10 ed Enterprise Mobility - Security. Usa la ricchezza di dati in Microsoft Graph per creare app per organizzazioni e consumatori che interagiscono con milioni di utenti.

Lo schema e una rappresentazione JSON per gli avvisi di sicurezza inviati a MS Graph sono disponibili nella documentazione di [Microsoft Graph.](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0)

---


## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive gli schemi utilizzati dagli strumenti di protezione dalle minacce del Centro sicurezza di Azure per l'invio delle informazioni sugli avvisi di sicurezza.

Per altre informazioni sulle modalità di accesso agli avvisi di sicurezza dall'esterno del Centro sicurezza, vedere le pagine seguenti:For more information on the ways to access security alerts from outside Security Center, see the following pages:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) - SIEM nativo per il cloud di Microsoft
- Hub eventi di Azure: servizio di inserimento dati completamente gestito e in tempo reale di MicrosoftAzure [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) - Microsoft's fully managed, real-time data ingestion service
- Funzionalità di [esportazione continua](continuous-export.md) del Centro sicurezza
- [Aree di lavoro di Log Analytics:](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) Monitoraggio di Azure archivia i dati di log in un'area di lavoro di Log Analytics, un contenitore che include dati e informazioni di configurazioneLog Analytics workspaces - Azure Monitor stores log data in a Log Analytics workspace, a container that includes data and configuration information
