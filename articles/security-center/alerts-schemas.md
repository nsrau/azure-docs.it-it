---
title: Schemi per gli avvisi del Centro sicurezza di Azure
description: Questo articolo descrive i diversi schemi usati dal centro sicurezza di Azure per gli avvisi di sicurezza.
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
# <a name="security-alerts-schemas"></a>Schemi degli avvisi di sicurezza

Gli utenti del livello standard del Centro sicurezza di Azure ricevono avvisi di sicurezza quando il Centro sicurezza rileva le minacce per le risorse.

È possibile visualizzare questi avvisi di sicurezza nelle pagine di **protezione dalle minacce** del Centro sicurezza di Azure o tramite strumenti esterni, ad esempio:

- [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) , il sistema Siem nativo di Microsoft. Il connettore Sentinel riceve gli avvisi dal centro sicurezza di Azure e li invia all' [area di lavoro log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) per Azure Sentinel.
- SIEM di terze parti: usare gli strumenti di [esportazione continua](continuous-export.md) del Centro sicurezza per inviare dati a [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/). Integrare quindi i dati dell'hub eventi con SIEM di terze parti.
- [API REST](https://docs.microsoft.com/rest/api/securitycenter/) : se si usa l'API REST per accedere agli avvisi, vedere la documentazione dell'API per gli [avvisi online](https://docs.microsoft.com/rest/api/securitycenter/alerts).

Se si usano metodi a livello di codice per utilizzare gli avvisi, sarà necessario lo schema corretto per trovare i campi pertinenti. Inoltre, se si esporta in un hub eventi o si tenta di attivare l'automazione del flusso di lavoro con connettori HTTP generici, usare gli schemi per analizzare correttamente gli oggetti JSON.

>[!IMPORTANT]
> Lo schema è leggermente diverso per ognuno di questi scenari, quindi assicurarsi di selezionare la scheda pertinente sotto.


## <a name="the-schemas"></a>Schemi 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automazione del flusso di lavoro ed esportazione continua nell'hub eventi](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Esempio di JSON per gli avvisi inviati ad app per la logica, a hub eventi e a SIEM di terze parti

Di seguito è riportato lo schema degli eventi di avviso passati a:

- Istanze di app per la logica di Azure configurate nell'automazione dei flussi di lavoro del Centro sicurezza
- Hub eventi di Azure con la funzionalità di esportazione continua del Centro sicurezza

Per altre informazioni sulla funzionalità di automazione del flusso di lavoro, vedere [automatizzare le risposte agli avvisi e alle raccomandazioni](workflow-automation.md).
Per altre informazioni sull'esportazione continua, vedere [esportare avvisi e consigli](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel e le aree di lavoro Log Analytics](#tab/schema-sentinel)

Il connettore Sentinel riceve gli avvisi dal centro sicurezza di Azure e li invia all'area di lavoro Log Analytics per Azure Sentinel. 

Per creare un evento o un evento imprevisto di Sentinel usando gli avvisi del Centro sicurezza, è necessario lo schema per gli avvisi riportati di seguito. 

Per ulteriori informazioni su Azure Sentinel, vedere [la documentazione](https://docs.microsoft.com/azure/sentinel/)di.

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure Activity Log](#tab/schema-activitylog)

Il Centro sicurezza di Azure controlla gli avvisi di sicurezza generati come eventi nel log attività di Azure.

Per visualizzare gli eventi di avviso di sicurezza nel log attività, cercare l'evento Activate Alert come illustrato:

[![Ricerca dell'evento Activate Alert nel log attività](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Esempio di JSON per gli avvisi inviati al log attività di Azure

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

### <a name="the-data-model-of-the-schema"></a>Modello di dati dello schema

|Campo|Descrizione|
|----|----|
|**canali**|Costante, "Operation"|
|**correlationId**|ID avviso del Centro sicurezza di Azure|
|**Descrizione**|Descrizione dell'avviso|
|**eventDataId**|Vedere correlationId|
|**eventName**|I sottocampi valore e localizedValue contengono il nome visualizzato dell'avviso|
|**category**|I sottocampi value e localizedValue sono Constant-"Security"|
|**eventTimestamp**|Timestamp UTC per la generazione dell'avviso|
|**id**|ID di avviso completo|
|**livello**|Costante, "informativo"|
|**operationId**|Vedere correlationId|
|**operationName**|Il campo del valore è Constant-"Microsoft. Security/locations/Alerts/Activate/Action" e il valore localizzato sarà "Activate Alert" (che potrebbe essere localizzato in modo analogo alle impostazioni locali dell'utente)|
|**resourceGroupName**|Includerà il nome del gruppo di risorse|
|**resourceProviderName**|I sottocampi value e localizedValue sono Constant-"Microsoft. Security"|
|**resourceType**|I sottocampi valore e localizedValue sono costanti-"Microsoft. Security/locations/Alerts"|
|**resourceId**|ID di risorsa di Azure completo|
|**Stato**|I sottocampi value e localizedValue sono Constant-"Active"|
|**Stato secondario**|Il valore e i sottocampi localizedValue sono vuoti|
|**submissionTimestamp**|Timestamp UTC dell'invio di eventi al log attività|
|**subscriptionId**|ID sottoscrizione della risorsa compromessa|
|**properties**|Un contenitore JSON di proprietà aggiuntive relative all'avviso. Questi possono variare da un avviso all'altro. Tuttavia, i campi seguenti verranno visualizzati in tutti gli avvisi:<br>-gravità: gravità dell'attacco<br>-compromisedEntity: nome della risorsa compromessa<br>-Correzione: matrice di passaggi di correzione da eseguire<br>-Intent: finalità della catena di uccisione dell'avviso. I possibili Intent sono documentati nella [tabella Intentions](alerts-reference.md#intentions)|
|**relatedEvents**|Costante-matrice vuota|
|||





### <a name="ms-graph-api"></a>[API Graph MS](#tab/schema-graphapi)

Microsoft Graph è il gateway per dati e intelligence in Microsoft 365. Fornisce un modello di programmabilità unificato che è possibile usare per accedere a una grande quantità di dati in Office 365, Windows 10 e Enterprise Mobility + Security. Usa la vasta gamma di dati in Microsoft Graph per creare app per organizzazioni e consumatori che interagiscono con milioni di utenti.

Lo schema e una rappresentazione JSON per gli avvisi di sicurezza inviati a MS Graph sono disponibili nella [documentazione di Microsoft Graph](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0).

---


## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive gli schemi usati dagli strumenti di protezione dalle minacce del Centro sicurezza di Azure per l'invio di informazioni sugli avvisi di sicurezza.

Per ulteriori informazioni sulle modalità di accesso agli avvisi di sicurezza dall'esterno del Centro sicurezza, vedere le pagine seguenti:

- [Sentinel di Azure](https://docs.microsoft.com/azure/sentinel/) -Siem nativo di Microsoft Cloud
- [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/) : servizio di inserimento dati in tempo reale e completamente gestito di Microsoft
- [Funzionalità di esportazione continua](continuous-export.md) del Centro sicurezza
- [Log Analytics aree di lavoro](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) : monitoraggio di Azure archivia i dati di log in un'area di lavoro log Analytics, un contenitore che include dati e informazioni di configurazione
