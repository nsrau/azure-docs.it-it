---
title: Schema degli eventi del log attività di Azure
description: Informazioni sullo schema degli eventi per i dati emessi nel log attività
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: abe2ed0d50ce26ddebeeeccb87c49fc20db43b2a
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515386"
---
# <a name="azure-activity-log-event-schema"></a>Schema degli eventi del log attività di Azure
Il **log attività di Azure** fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Questo articolo descrive lo schema degli eventi per ogni categoria di dati. Lo schema dei dati varia a seconda che si stiano leggendo i dati nel portale, in PowerShell, nell'interfaccia della riga di comando o direttamente tramite l'API REST rispetto allo [streaming dei dati nella risorsa di archiviazione o in Hub eventi usando un profilo di log](activity-log-export.md). Gli esempi seguenti mostrano lo schema reso disponibile tramite il portale, PowerShell, l'interfaccia della riga di comando e l'API REST. Un mapping di queste proprietà allo [schema di log di diagnostica di Azure](diagnostic-logs-schema.md) è disponibile alla fine dell'articolo.

## <a name="administrative"></a>Administrative
Questa categoria contiene il record di tutte le operazioni di creazione, aggiornamento, eliminazione e azione eseguite tramite Resource Manager. Tra gli esempi dei tipi di eventi visualizzati in questa categoria sono inclusi "create virtual machine" e "delete network security group". Ogni azione eseguita da un utente o da un'applicazione usando Resource Manager viene modellata come operazione in un determinato tipo di risorsa. Se l'operazione è di tipo scrittura, eliminazione o azione, i record di avvio e riuscita o di non riuscita di tale operazione vengono registrati nella categoria Administrative. Questa categoria include anche eventuali modifiche al controllo degli accessi in base al ruolo in una sottoscrizione.

### <a name="sample-event"></a>Evento di esempio
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Descrizione |
| --- | --- |
| authorization |BLOB delle proprietà RBAC dell'evento. In genere include le proprietà "action", "role" e "scope". |
| caller |Indirizzo di posta elettronica dell'utente che ha eseguito l'operazione, attestazione UPN o attestazione SPN, a seconda della disponibilità. |
| channels |Uno dei valori seguenti: "Admin", "Operation" |
| claims |Token JWT usato da Active Directory per l'autenticazione dell'utente o dall'applicazione per eseguire questa operazione in Resource Manager. |
| correlationId |In genere un GUID in formato stringa. Gli eventi che condividono un elemento correlationId appartengono alla stessa azione. |
| description |Testo statico che descrive un evento. |
| eventDataId |Identificatore univoco di un evento. |
| eventName | Nome descrittivo dell'evento amministrativo. |
| category | Sempre "amministrativo" |
| httpRequest |BLOB che descrive la richiesta HTTP. In genere include "clientRequestId", "clientIpAddress" e "method" (metodo HTTP, ad esempio PUT). |
| level |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning" e "Informational" |
| resourceGroupName |Nome del gruppo di risorse della risorsa interessata. |
| resourceProviderName |Nome del provider di risorse della risorsa interessata. |
| resourceType | Tipo di risorsa interessato da un evento amministrativo. |
| resourceId |ID della risorsa interessata. |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento. |
| status |Stringa che descrive lo stato dell'operazione. Alcuni valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |In genere il codice di stato HTTP della chiamata REST corrispondente, ma può includere anche altre stringhe che descrivono uno stato secondario, come i valori seguenti: OK (codice di stato HTTP: 200), Creata (codice di stato HTTP: 201), Accettata (codice di stato HTTP: 202), Nessun contenuto (codice di stato HTTP: 204), Richiesta non valida (codice di stato HTTP: 400), Non trovata (codice di stato HTTP: 404), Conflitto (codice di stato HTTP: 409), Errore interno del server (codice di stato HTTP: 500), Servizio non disponibile (codice di stato HTTP: 503), Timeout gateway (codice di stato HTTP: 504). |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |

## <a name="service-health"></a>Service Health
Questa categoria contiene il record degli eventi imprevisti di integrità dei servizi che si sono verificati in Azure. Un esempio del tipo di evento visualizzato in questa categoria è "SQL Azure in East US is experiencing downtime". Gli eventi di integrità dei servizi sono di cinque tipi: Action Required, Assisted Recovery, Incident, Maintenance, Information o Security, che vengono visualizzati solo se una risorsa della sottoscrizione è interessata dall'evento.

### <a name="sample-event"></a>Evento di esempio
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Per la documentazione relativa ai valori nelle proprietà fare riferimento all'articolo sulle [notifiche sull'integrità dei servizi](./../../azure-monitor/platform/service-notifications.md).

## <a name="resource-health"></a>Resource Health
Questa categoria contiene il record degli eventi di integrità delle risorse che si sono verificati nelle risorse di Azure. Un esempio del tipo di evento visualizzato in questa categoria è "Virtual Machine health status changed to unavailable". Gli eventi di integrità delle risorse possono rappresentare uno dei quattro stati di integrità: Available, Unavailable, Degraded e Unknown. Inoltre, gli eventi di integrità delle risorse possono essere classificati come avviati dalla piattaforma o avviati dall'utente.

### <a name="sample-event"></a>Evento di esempio

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Descrizione |
| --- | --- |
| channels | Sempre "Admin, Operation" |
| correlationId | GUID in formato stringa. |
| description |Testo statico che descrive l'evento dell'avviso. |
| eventDataId |Identificatore univoco dell'evento dell'avviso. |
| category | Sempre "ResourceHealth" |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| level |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose" |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| resourceGroupName |Nome del gruppo di risorse che contiene la risorsa. |
| resourceProviderName |Sempre "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | Tipo di risorsa che è stata interessata da un evento di integrità delle risorse. |
| resourceId | Nome dell'ID risorsa della risorsa interessata. |
| status |Stringa che descrive lo stato dell'evento di integrità. I valori possibili sono: Active, Resolved, InProgress, Updated. |
| subStatus | In genere il valore Null per gli avvisi. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento.|
| Properties.title | Stringa descrittiva che descrive lo stato di integrità della risorsa. |
| properties.details | Stringa descrittiva che descrive più dettagliatamente l'evento. |
| properties.currentHealthStatus | Lo stato di integrità corrente della risorsa. Uno dei valori seguenti: "Available", "Unavailable", "Degraded" e "Unknown". |
| properties.previousHealthStatus | Lo stato di integrità precedente della risorsa. Uno dei valori seguenti: "Available", "Unavailable", "Degraded" e "Unknown". |
| properties.type | Descrizione del tipo di evento di integrità delle risorse. |
| properties.cause | Descrizione della causa dell'evento di integrità delle risorse. Può essere "UserInitiated" o "PlatformInitiated". |


## <a name="alert"></a>Alert
Questa categoria contiene il record di tutte le attivazioni degli avvisi di Azure. Un esempio del tipo di evento visualizzato in questa categoria è "CPU % on myVM has been over 80 for the past 5 minutes". In diversi sistemi Azure il concetto di avviso prevede la possibilità di definire una regola di qualche tipo e di ricevere una notifica quando le condizioni corrispondono a tale regola. Ogni volta che un tipo di avviso di Azure supportato viene "attivato" o vengono soddisfatte le condizioni per generare una notifica, viene anche eseguito il push di un record dell'attivazione in questa categoria del log attività.

### <a name="sample-event"></a>Evento di esempio

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Descrizione |
| --- | --- |
| caller | Sempre Microsoft.Insights/alertRules |
| channels | Sempre "Admin, Operation" |
| claims | BLOB JSON con il nome dell'entità servizio (SPN) o il tipo di risorsa del motore degli avvisi. |
| correlationId | GUID in formato stringa. |
| description |Testo statico che descrive l'evento dell'avviso. |
| eventDataId |Identificatore univoco dell'evento dell'avviso. |
| category | Sempre "avviso" |
| level |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning" e "Informational" |
| resourceGroupName |Nome del gruppo di risorse della risorsa interessata, se si tratta di un avviso per la metrica. Per gli altri tipi di avvisi, è il nome del gruppo di risorse contenente l'avviso stesso. |
| resourceProviderName |Nome del provider di risorse della risorsa interessata, se si tratta di un avviso per la metrica. Per gli altri tipi di avvisi, è il nome del provider di risorse per l'avviso stesso. |
| resourceId | ID della risorsa interessata, se si tratta di un avviso per la metrica. Per gli altri tipi di avvisi, è l'ID della risorsa dell'avviso stessa. |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento. |
| status |Stringa che descrive lo stato dell'operazione. Alcuni valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | In genere il valore Null per gli avvisi. |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |

### <a name="properties-field-per-alert-type"></a>Campo delle proprietà per tipo di avviso
Il campo delle proprietà conterrà valori diversi a seconda dell'origine dell'evento dell'avviso. Due comuni provider di eventi di avviso sono gli avvisi delle metriche e gli avvisi del log attività.

#### <a name="properties-for-activity-log-alerts"></a>Proprietà degli avvisi del log attività
| Nome dell'elemento | Descrizione |
| --- | --- |
| properties.subscriptionId | ID della sottoscrizione dall'evento del log attività che ha causato l'attivazione di questa regola di avviso del log attività. |
| properties.eventDataId | ID dei dati dell'evento dall'evento del log attività che ha causato l'attivazione di questa regola di avviso del log attività. |
| properties.resourceGroup | Gruppo di risorse dall'evento del log attività che ha causato l'attivazione di questa regola di avviso del log attività. |
| properties.resourceId | ID della risorsa dall'evento del log attività che ha causato l'attivazione di questa regola di avviso del log attività. |
| properties.eventTimestamp | Timestamp dell'evento del log attività che ha causato l'attivazione di questa regola di avviso del log attività. |
| properties.operationName | Nome dell'operazione dall'evento del log attività che ha causato l'attivazione di questa regola di avviso del log attività. |
| properties.status | Stato dall'evento del log attività che ha causato l'attivazione di questa regola di avviso del log attività.|

#### <a name="properties-for-metric-alerts"></a>Proprietà degli avvisi delle metriche
| Nome dell'elemento | Descrizione |
| --- | --- |
| properties.RuleUri | ID risorsa della regola di avviso per la metrica. |
| properties.RuleName | Nome della regola di avviso per la metrica. |
| properties.RuleDescription | Descrizione della regola di avviso per la metrica (definita nella regola di avviso). |
| properties.Threshold | Valore soglia usato nella valutazione della regola di avviso per la metrica. |
| properties.WindowSizeInMinutes | Dimensione dell'intervallo usata nella valutazione della regola di avviso per la metrica. |
| properties.Aggregation | Tipo di aggregazione definito nella regola di avviso per la metrica. |
| properties.Operator | Operatore condizionale usato nella valutazione della regola di avviso per la metrica. |
| properties.MetricName | Nome della metrica usato nella valutazione della regola di avviso per la metrica. |
| properties.MetricUnit | Unità della metrica usata nella valutazione della regola di avviso per la metrica. |

## <a name="autoscale"></a>Autoscale
Questa categoria contiene il record degli eventi correlati all'operazione del motore di ridimensionamento automatico in base alle impostazioni di scalabilità automatica definite nella sottoscrizione. Un esempio del tipo di evento visualizzato in questa categoria è "Autoscale scale up action failed". Con il ridimensionamento automatico è possibile aumentare o ridurre automaticamente il numero di istanze in un tipo di risorsa supportato in base all'ora del giorno e/o ai dati di caricamento (metrica) usando un'impostazione di ridimensionamento automatico. Quando vengono soddisfatte le condizioni per aumentare o ridurre le prestazioni, gli eventi avviati e riusciti o quelli non riusciti verranno registrati in questa categoria.

### <a name="sample-event"></a>Evento di esempio
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Descrizione |
| --- | --- |
| caller | Sempre Microsoft.Insights/autoscaleSettings |
| channels | Sempre "Admin, Operation" |
| claims | BLOB JSON con il nome dell'entità servizio (SPN) o il tipo di risorsa del motore di ridimensionamento automatico. |
| correlationId | GUID in formato stringa. |
| description |Testo statico che descrive l'evento di ridimensionamento automatico. |
| eventDataId |Identificatore univoco dell'evento di ridimensionamento automatico. |
| level |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning" e "Informational" |
| resourceGroupName |Nome del gruppo di risorse dell'impostazione di ridimensionamento automatico. |
| resourceProviderName |Nome del provider di risorse dell'impostazione di ridimensionamento automatico. |
| resourceId |ID risorsa dell'impostazione di ridimensionamento automatico. |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento. |
| properties.Description | Descrizione dettagliata delle operazioni eseguite dal motore di ridimensionamento automatico. |
| properties.ResourceName | ID della risorsa interessata (risorsa in cui è in corso l'azione di ridimensionamento). |
| properties.OldInstancesCount | Numero di istanze prima che sia stata eseguita l'azione di ridimensionamento automatico. |
| properties.NewInstancesCount | Numero di istanze dopo che è stata eseguita l'azione di ridimensionamento automatico. |
| properties.LastScaleActionTime | Timestamp indicante quando si è verificata l'azione di ridimensionamento automatico. |
| status |Stringa che descrive lo stato dell'operazione. Alcuni valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | In genere il valore Null per il ridimensionamento automatico. |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |

## <a name="security"></a>Security
Questa categoria contiene il record degli avvisi generati dal Centro sicurezza di Azure. Un esempio del tipo di evento visualizzato in questa categoria è "Suspicious double extension file executed".

### <a name="sample-event"></a>Evento di esempio
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Descrizione |
| --- | --- |
| channels | Sempre "Operation" |
| correlationId | GUID in formato stringa. |
| description |Testo statico che descrive l'evento di sicurezza. |
| eventDataId |Identificatore univoco dell'evento di sicurezza. |
| eventName |Nome descrittivo dell'evento di sicurezza. |
| category | Sempre "sicurezza" |
| id |Identificatore di risorsa univoco dell'evento di sicurezza. |
| level |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning" e "Informational" |
| resourceGroupName |Nome del gruppo di risorse della risorsa. |
| resourceProviderName |Nome del provider di risorse per il Centro sicurezza di Azure. Sempre "Microsoft.Security". |
| resourceType |Tipo di risorsa che ha generato l'evento di sicurezza, ad esempio "Microsoft.Security/locations/alerts". |
| resourceId |ID risorsa dell'avviso di sicurezza. |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento. Queste proprietà variano a seconda del tipo di avviso di sicurezza. Vedere [questa pagina](../../security-center/security-center-alerts-overview.md) per una descrizione dei tipi di avvisi provenienti dal Centro sicurezza. |
| properties.Severity |Livello di gravità. I valori possibili sono "High", "Medium" o "Low". |
| status |Stringa che descrive lo stato dell'operazione. Alcuni valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | In genere il valore Null per gli eventi di sicurezza. |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |

## <a name="recommendation"></a>Recommendation
Questa categoria include il record di tutte le nuove raccomandazioni che vengono generate per i servizi. Un esempio di raccomandazione potrebbe essere "Per una migliore tolleranza di errore, usare i set di disponibilità". Esistono quattro tipi di eventi di raccomandazione che possono essere generati: High Availability, Performance, Security e Cost Optimization. 

### <a name="sample-event"></a>Evento di esempio
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Descrizione |
| --- | --- |
| channels | Sempre "Operation" |
| correlationId | GUID in formato stringa. |
| description |Testo statico che descrive l'evento di raccomandazione. |
| eventDataId | Identificatore univoco dell'evento di raccomandazione. |
| category | Sempre "Recommendation" |
| id |Identificatore univoco di risorsa dell'evento di raccomandazione. |
| level |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning" e "Informational" |
| operationName |Nome dell'operazione.  Sempre "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Nome del gruppo di risorse della risorsa. |
| resourceProviderName |Nome del provider di risorse per la risorsa a cui si applica la raccomandazione, ad esempio "MICROSOFT.COMPUTE" |
| resourceType |Nome del tipo di risorsa per la risorsa a cui si applica la raccomandazione, ad esempio "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |ID della risorsa a cui si applica la raccomandazione. |
| status | Sempre "Active" |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli della raccomandazione.|
| properties.recommendationSchemaVersion| Versione dello schema delle proprietà della raccomandazione pubblicata nella voce del log attività |
| properties.recommendationCategory | Categoria della raccomandazione. I valori possibili sono "High Availability", "Performance", "Security" e "Cost" |
| properties.recommendationImpact| Impatto della raccomandazione. I valori possibili sono "High", "Medium", "Low" |
| properties.recommendationRisk| Rischio della raccomandazione. I valori possibili sono "Error", "Warning", "None" |

## <a name="policy"></a>Policy

Questa categoria include i record di tutte le operazioni relative ad azioni effetto eseguite da [Criteri di Azure](../../governance/policy/overview.md). Tra gli esempi dei tipi di eventi visualizzati in questa categoria sono inclusi _Audit_ e _Deny_. Ogni azione eseguita da Criteri viene modellata come operazione su una risorsa.

### <a name="sample-policy-event"></a>Evento di Criteri di esempio

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Descrizioni delle proprietà degli eventi di Criteri

| Nome dell'elemento | Descrizione |
| --- | --- |
| authorization | Matrice delle proprietà RBAC dell'evento. Per le nuove risorse, questa è l'azione e l'ambito della richiesta che ha attivato la valutazione. Per le risorse esistenti, l'azione è "Microsoft.Resources/checkPolicyCompliance/read". |
| caller | Per le nuove risorse, l'identità che ha avviato una distribuzione. Per le risorse esistenti, il GUID di Microsoft Azure Policy Insights RP. |
| channels | Gli eventi di Criteri usano solo il canale "Operation". |
| claims | Token JWT usato da Active Directory per l'autenticazione dell'utente o dall'applicazione per eseguire questa operazione in Resource Manager. |
| correlationId | In genere un GUID in formato stringa. Gli eventi che condividono un elemento correlationId appartengono alla stessa azione. |
| description | Questo campo è vuoto per gli eventi di Criteri. |
| eventDataId | Identificatore univoco di un evento. |
| eventName | Può essere "BeginRequest" o "EndRequest". "BeginRequest" viene usato per le valutazioni auditIfNotExists e deployIfNotExists ritardate e quando un effetto deployIfNotExists avvia una distribuzione di modello. Tutte le altre operazioni restituiscono "EndRequest". |
| category | Dichiara l'evento del log attività come appartenente a "Policy". |
| eventTimestamp | Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| id | Identificatore univoco dell'evento sulla risorsa specifica. |
| level | Livello dell'evento. Audit usa "Warning" e Deny usa "Error". Un errore auditIfNotExists o deployIfNotExists può generare "Warning" o "Error", a seconda della gravità. Tutti gli altri eventi di Criteri usano "Informational". |
| operationId | GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName | Nome dell'operazione, direttamente correlato all'effetto di Criteri. |
| resourceGroupName | Nome del gruppo di risorse della risorsa valutata. |
| resourceProviderName | Nome del provider di risorse per la risorsa valutata. |
| resourceType | Per le nuove risorse, è il tipo che viene valutato. Per le risorse esistenti, restituisce "Microsoft.Resources/checkPolicyCompliance". |
| resourceId | ID della risorsa valutata. |
| status | Stringa che descrive lo stato del risultato della valutazione di Criteri. La maggior parte delle valutazioni di Criteri restituisce "Succeeded", ma un effetto Deny restituisce "Failed". Anche gli errori in auditIfNotExists o deployIfNotExists restituiscono "Failed". |
| subStatus | Questo campo è vuoto per gli eventi di Criteri. |
| submissionTimestamp | Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId | ID sottoscrizione di Azure. |
| properties.isComplianceCheck | Restituisce "False" quando viene distribuita una nuova risorsa o quando vengono aggiornate le proprietà di Resource Manager della risorsa esistente. Tutti gli altri [trigger di valutazione](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) hanno come risultato "True". |
| properties.resourceLocation | Area di Azure della risorsa che viene valutata. |
| properties.ancestors | Elenco delimitato da virgole dei gruppi di gestione padre ordinati a partire dall'elemento padre diretto fino a quello più lontano. |
| properties.policies | Include informazioni sulla definizione, l'assegnazione, l'effetto e i parametri dei criteri da cui risulta questa valutazione di Criteri. |
| relatedEvents | Questo campo è vuoto per gli eventi di Criteri. |

## <a name="mapping-to-diagnostic-logs-schema"></a>Mapping allo schema di log di diagnostica

Quando si esegue lo streaming del log attività di Azure a un account di archiviazione o a uno spazio dei nomi di Hub eventi, i dati seguono lo [schema di log di diagnostica di Azure](./diagnostic-logs-schema.md). Di seguito è riportato il mapping delle proprietà dallo schema riportato sopra allo schema di log di diagnostica:

| Proprietà dello schema di log di diagnostica | Proprietà dello schema API REST del log attività | Note |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType, resourceGroupName sono tutti dedotti da resourceId. |
| operationName | operationName.value |  |
| category | Parte del nome dell'operazione | Suddivisione del tipo di operazione: "Write"/"Delete"/"Action" |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMs | N/D | Sempre 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | attestazioni e proprietà di autorizzazione |  |
| Level | Level |  |
| location | N/D | Posizione in cui è stato elaborato l'evento. *Non si tratta del percorso della risorsa, ma piuttosto della posizione in cui è stato elaborato l'evento. Questa proprietà verrà rimossa in un aggiornamento futuro.* |
| Properties | properties.eventProperties |  |
| properties.eventCategory | category | Se properties.eventCategory non è presente, la categoria è "Administrative" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sul log attività](activity-logs-overview.md)
* [Esportare il log attività in archiviazione di Azure o in hub eventi](activity-log-export.md)

