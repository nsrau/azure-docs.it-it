---
title: "Schema degli eventi del log attività Azure | Microsoft Docs"
description: "Informazioni sullo schema degli eventi per i dati emessi nel log attività"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 5c96a1cfa56d1535549cb15d5a7bcf03bd11e723
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="azure-activity-log-event-schema"></a>Schema degli eventi del log attività di Azure
Il **log attività di Azure** fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Questo articolo descrive lo schema degli eventi per ogni categoria di dati.

## <a name="administrative"></a>Amministrativo
Questa categoria contiene il record di tutte le operazioni di creazione, aggiornamento, eliminazione e azione eseguite tramite Resource Manager. Tra gli esempi dei tipi di eventi visualizzati in questa categoria sono inclusi "create virtual machine" e "delete network security group". Ogni azione eseguita da un utente o da un'applicazione usando Resource Manager viene modellata come operazione in un determinato tipo di risorsa. Se l'operazione è di tipo scrittura, eliminazione o azione, i record di avvio e riuscita o di non riuscita di tale operazione vengono registrati nella categoria amministrativa. La categoria amministrativa include anche eventuali modifiche al controllo degli accessi in base al ruolo in una sottoscrizione.

### <a name="sample-event"></a>Evento di esempio
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
  "claims": {
    "aud": "https://management.core.windows.net/",
    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
    "iat": "1421876371",
    "nbf": "1421876371",
    "exp": "1421880271",
    "ver": "1.0",
    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
    "puid": "20030000801A118C",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
    "name": "John Smith",
    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
    "appidacr": "2",
    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
    "http://schemas.microsoft.com/claims/authnclassreference": "1"
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
}
```

### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Description |
| --- | --- |
| autorizzazione |BLOB delle proprietà RBAC dell'evento. In genere include le proprietà "action", "role" e "scope". |
| caller |Indirizzo di posta elettronica dell'utente che ha eseguito l'operazione, attestazione UPN o attestazione SPN, a seconda della disponibilità. |
| channels |Uno dei valori seguenti: "Admin" o "Operation". |
| claims |Token JWT usato da Active Directory per autenticare l'utente o l'applicazione per eseguire questa operazione in Resource Manager. |
| correlationId |In genere un GUID in formato stringa. Gli eventi che condividono un elemento correlationId appartengono alla stessa azione. |
| Description |Testo statico che descrive un evento. |
| eventDataId |Identificatore univoco di un evento. |
| httpRequest |BLOB che descrive la richiesta HTTP. In genere include "clientRequestId", "clientIpAddress" e "method" (metodo HTTP, ad esempio PUT). |
| necessario |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose" |
| resourceGroupName |Nome del gruppo di risorse della risorsa interessata. |
| resourceProviderName |Nome del provider di risorse della risorsa interessata. |
| resourceId |ID risorsa della risorsa interessata. |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento. |
| status |Stringa che descrive lo stato dell'operazione. Alcuni dei valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |In genere si tratta del codice di stato HTTP della chiamata REST corrispondente, ma può includere altre stringhe che descrivono uno stato secondario, come i valori comuni seguenti: OK (codice di stato HTTP: 200), Created (codice di stato HTTP: 201), Accepted (codice di stato HTTP: 202), No Content (codice di stato HTTP: 204), Bad Request (codice di stato HTTP: 400), Not Found (codice di stato HTTP: 404), Conflict (codice di stato HTTP: 409), Internal Server Error (codice di stato HTTP: 500), Service Unavailable (codice di stato HTTP: 503), Gateway Timeout (codice di stato HTTP: 504). |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |

## <a name="service-health"></a>Integrità del servizio
Questa categoria contiene il record degli eventi imprevisti di integrità del servizio che si sono verificati in Azure. Un esempio del tipo di evento visualizzato in questa categoria è "SQL Azure in East US is experiencing downtime". Gli eventi di integrità del servizio sono di sei tipi: Action Required, Assisted Recovery, Incident, Maintenance, Information o Security, che vengono visualizzati solo se una risorsa della sottoscrizione è interessata dall'evento.

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
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
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
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
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

### <a name="property-descriptions"></a>Descrizioni delle proprietà
Nome dell'elemento | Descrizione
-------- | -----------
channels | Uno dei valori seguenti: "Admin" o "Operation"
correlationId | In genere un GUID in formato stringa. Gli eventi che appartengono alla stessa azione in genere condividono lo stesso correlationId.
Descrizione | Descrizione dell'evento.
eventDataId | Identificatore univoco di un evento.
eventName | Titolo dell'evento.
necessario | Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose"
resourceProviderName | Nome del provider di risorse della risorsa interessata. Se non è noto, sarà null.
resourceType| Tipo della risorsa interessata. Se non è noto, sarà null.
subStatus | In genere, null per gli eventi di integrità del servizio.
eventTimestamp | Timestamp indicante quando l'evento del log è stato generato e inviato al log attività.
submissionTimestamp |   Timestamp indicante quando l'evento è diventato disponibile nel log attività.
subscriptionId | Sottoscrizione di Azure in cui è stato registrato l'evento.
status | Stringa che descrive lo stato dell'operazione. Alcuni valori comuni sono: Active, Resolved.
operationName | Nome dell'operazione. In genere, Microsoft.ServiceHealth/incident/action.
category | "ServiceHealth"
resourceId | ID risorsa della risorsa interessata, se noto. L'ID sottoscrizione viene fornito diversamente.
Properties.title | Titolo della comunicazione localizzato. L'inglese è la lingua predefinita.
Properties.communication | Dettagli localizzati della comunicazione con markup HTML. L'inglese è la lingua predefinita.
Properties.incidentType | Valori possibili: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | Identifica l'evento imprevisto a cui è associato l'evento. Usare questa proprietà per correlare gli eventi relativi a un evento imprevisto.
Properties.impactedServices | BLOB JSON preceduto da un carattere di escape che descrive i servizi e le aree su cui ha effetto l'evento imprevisto. Un elenco di servizi, ognuno dei quali ha un nome di servizio e un elenco delle aree interessate, ognuna con un nome di area.
Properties.defaultLanguageTitle | La comunicazione in inglese
Properties.defaultLanguageContent | La comunicazione in inglese con markup HTML o come testo normale
Properties.stage | I valori possibili di AssistedRecovery, ActionRequired, Information, Incident, Security sono Active e Resolved. Per la manutenzione i valori possibili sono: Active, Planned, InProgress, Canceled, Rescheduled, Resolved, Complete
Properties.communicationId | La comunicazione a cui è associato l'evento.

## <a name="alert"></a>Avviso
Questa categoria contiene il record di tutte le attivazioni degli avvisi di Azure. Un esempio del tipo di evento visualizzato in questa categoria è "CPU % on myVM has been over 80 for the past 5 minutes". In diversi sistemi Azure il concetto di avviso prevede la possibilità di definire una regola di qualche tipo e di ricevere una notifica quando le condizioni corrispondono a tale regola. Ogni volta che un tipo di avviso di Azure supportato viene "attivato" o vengono soddisfatte le condizioni per generare una notifica, viene anche eseguito il push di un record dell'attivazione in questa categoria del log attività.

### <a name="sample-event"></a>Evento di esempio

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
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
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
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
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Descrizione |
| --- | --- |
| caller | Sempre Microsoft.Insights/alertRules |
| channels | Sempre "Admin, Operation" |
| claims | BLOB JSON con il nome dell'entità servizio (SPN) o il tipo di risorsa del motore degli avvisi. |
| correlationId | GUID in formato stringa. |
| Descrizione |Testo statico che descrive l'evento dell'avviso. |
| eventDataId |Identificatore univoco dell'evento dell'avviso. |
| necessario |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose" |
| resourceGroupName |Nome del gruppo di risorse della risorsa interessata, se si tratta di un avviso per la metrica. Per gli altri tipi di avvisi, è il nome del gruppo di risorse contenente l'avviso stesso. |
| resourceProviderName |Nome del provider di risorse della risorsa interessata, se si tratta di un avviso per la metrica. Per gli altri tipi di avvisi, è il nome del provider di risorse per l'avviso stesso. |
| resourceId | ID della risorsa interessata, se si tratta di un avviso per la metrica. Per gli altri tipi di avvisi, è l'ID della risorsa dell'avviso stessa. |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento. |
| status |Stringa che descrive lo stato dell'operazione. Alcuni dei valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | In genere null per gli avvisi. |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |

### <a name="properties-field-per-alert-type"></a>Campo delle proprietà per ogni tipo di avviso
Il campo delle proprietà conterrà valori diversi a seconda dell'origine dell'evento dell'avviso. Due comuni provider di eventi di avviso sono gli avvisi e gli avvisi delle metriche del log attività.

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
| properties.Threshold | Valore della soglia usato nella valutazione della regola di avviso per la metrica. |
| properties.WindowSizeInMinutes | Dimensioni della finestra usate nella valutazione della regola di avviso per la metrica. |
| properties.Aggregation | Tipo di aggregazione definito nella regola di avviso per la metrica. |
| properties.Operator | Operatore condizionale usato nella valutazione della regola di avviso per la metrica. |
| properties.MetricName | Nome della metrica usato nella valutazione della regola di avviso per la metrica. |
| properties.MetricUnit | Unità della metrica usata nella valutazione della regola di avviso per la metrica. |

## <a name="autoscale"></a>Autoscale
Questa categoria contiene il record degli eventi correlati all'operazione del motore di ridimensionamento automatico in base alle impostazioni di scalabilità automatica definite nella sottoscrizione. Un esempio del tipo di evento visualizzato in questa categoria è "Autoscale scale up action failed". Con il ridimensionamento automatico è possibile aumentare o ridurre automaticamente il numero di istanze in un tipo di risorsa supportato in base all'ora del giorno e/o ai dati di caricamento (metrica) usando un'impostazione di ridimensionamento automatico. Quando vengono soddisfatte le condizioni per aumentare o ridurre le prestazioni, gli eventi di avvio riusciti o quelli non riusciti verranno registrati in questa categoria.

### <a name="sample-event"></a>Evento di esempio
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
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
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
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
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>Descrizioni delle proprietà
| Nome dell'elemento | Descrizione |
| --- | --- |
| caller | Sempre Microsoft.Insights/autoscaleSettings |
| channels | Sempre "Admin, Operation" |
| claims | BLOB JSON con il nome dell'entità servizio (SPN) o il tipo di risorsa del motore di ridimensionamento automatico. |
| correlationId | GUID in formato stringa. |
| Descrizione |Testo statico che descrive l'evento di ridimensionamento automatico. |
| eventDataId |Identificatore univoco dell'evento di ridimensionamento automatico. |
| necessario |Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose" |
| resourceGroupName |Nome del gruppo di risorse dell'impostazione di ridimensionamento automatico. |
| resourceProviderName |Nome del provider di risorse dell'impostazione di ridimensionamento automatico. |
| resourceId |ID risorsa dell'impostazione di scalabilità automatica. |
| operationId |GUID condiviso tra gli eventi che corrispondono a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Set di coppie `<Key, Value>`, ovvero un dizionario, che descrive i dettagli dell'evento. |
| properties.Description | Descrizione dettagliata delle operazioni eseguite dal motore di ridimensionamento automatico. |
| properties.ResourceName | ID della risorsa interessata (risorsa in cui è in corso l'azione di ridimensionamento) |
| properties.OldInstancesCount | Numero di istanze prima che venisse eseguita l'azione di ridimensionamento automatico. |
| properties.NewInstancesCount | Numero di istanze dopo che è stata eseguita l'azione di ridimensionamento automatico. |
| properties.LastScaleActionTime | Timestamp indicante quando si è verificata l'azione di ridimensionamento automatico. |
| status |Stringa che descrive lo stato dell'operazione. Alcuni dei valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | In genere null per il ridimensionamento automatico. |
| eventTimestamp |Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento. |
| submissionTimestamp |Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query. |
| subscriptionId |ID sottoscrizione di Azure. |

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sul log attività (in precedenza, log di controllo)](monitoring-overview-activity-logs.md)
* [Trasmettere il log attività di Azure a Hub eventi](monitoring-stream-activity-logs-event-hubs.md)

