---
title: "Informazioni sullo schema webhook degli avvisi del log attività | Microsoft Docs"
description: "Informazioni sullo schema del formato JSON che viene pubblicato in un URL del webhook all'attivazione di un avviso del log attività."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: johnkem
ms.openlocfilehash: 75c71bcd16573d4f4dd3377c623aa9b414aa3906
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Webhook per gli avvisi del log attività di Azure
Nella definizione di un gruppo di azione è possibile configurare gli endpoint webhook in modo da ricevere le notifiche per gli avvisi del log attività. Con i webhook è possibile instradare queste notifiche ad altri sistemi per la post-elaborazione o azioni personalizzate. L'articolo illustra anche il modo in cui il payload per il protocollo HTTP POST viene percepito da un webhook.

Per altre informazioni sugli avvisi del log di attività, vedere come [creare gli avvisi del log attività di Azure](monitoring-activity-log-alerts.md).

Per informazioni su gruppi di azioni, vedere come [creare gruppi di azioni](monitoring-action-groups.md).

## <a name="authenticate-the-webhook"></a>Autenticazione del webhook
Facoltativamente il webhook può usare l'autorizzazione basata su token per l'autenticazione. L'URI del webhook viene salvato con un ID token, ad esempio `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`.

## <a name="payload-schema"></a>Schema del payload
Il payload JSON contenuto nell'operazione POST varia a seconda del campo data.context.activityLog.eventSource del payload.

###<a name="common"></a>Comune
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```
###<a name="administrative"></a>Amministrativo
```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}

```
###<a name="servicehealth"></a>ServiceHealth
```json
{
    "schemaId": "unknown",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "properties": {
                    "title": "...",
                    "service": "...",
                    "region": "...",
                    "communication": "...",
                    "incidentType": "Incident",
                    "trackingId": "...",
                    "groupId": "...",
                    "impactStartTime": "3/29/2017 3:43:21 PM",
                    "impactMitigationTime": "3/29/2017 3:43:21 PM",
                    "eventCreationTime": "3/29/2017 3:43:21 PM",
                    "impactedServices": "[{...}]",
                    "defaultLanguageTitle": "...",
                    "defaultLanguageContent": "...",
                    "stage": "Active",
                    "communicationId": "...",
                    "version": "0.1"
                }
            }
        },
        "properties": {}
    }
}
```

Per i dettagli su schemi specifici relativi agli avvisi del log attività per le notifiche sull'integrità del servizio, vedere [Notifiche sull'integrità del servizio](monitoring-service-notifications.md).

Per i dettagli su schemi specifici relativi a tutti gli altri avvisi del log attività, vedere [Panoramica del log attività di Azure](monitoring-overview-activity-logs.md).

| Nome dell'elemento | Descrizione |
| --- | --- |
| status |Usato per avvisi relativi alle metriche. Sempre impostato su "Activated" per gli avvisi del registro attività. |
| context |Contesto dell'evento. |
| resourceProviderName |Provider della risorsa interessata. |
| conditionType |Sempre "Event". |
| name |Nome della regola di avviso. |
| id |ID risorsa dell'avviso. |
| description |Descrizione dell'avviso impostata al momento della creazione dell'avviso. |
| subscriptionId |ID sottoscrizione di Azure. |
| timestamp |Data e ora in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta. |
| resourceId |ID risorsa della risorsa interessata. |
| resourceGroupName |Nome del gruppo di risorse della risorsa interessata. |
| properties |Set di coppie `<Key, Value>` (cioè `Dictionary<String, String>`), inclusi dettagli relativi all'evento. |
| event |Elemento contenente i metadati relativi all'evento. |
| autorizzazione |Proprietà di controllo degli accessi in base al ruolo per l'evento. Queste proprietà includono in genere action, role e scope. |
| category |Categoria dell'evento. I valori supportati includono Administrative, Alert, Security, ServiceHealth e Recommendation. |
| caller |Indirizzo di posta elettronica dell'utente che ha eseguito l'operazione, attestazione UPN o attestazione SPN, a seconda della disponibilità. Può essere null per alcune chiamate di sistema. |
| correlationId |In genere un GUID in formato stringa. Gli eventi con correlationId appartengono alla stessa azione di livello superiore e in genere condividono un elemento correlationId. |
| eventDescription |Testo statico che descrive l'evento. |
| eventDataId |Identificatore univoco dell'evento. |
| eventSource |Nome del servizio o dell'infrastruttura di Azure che ha generato l'evento. |
| httpRequest |La richiesta in genere include clientRequestId, clientIpAddress e method HTTP, ad esempio PUT. |
| level |Uno dei valori seguenti: Critical, Error, Warning, Informational e Verbose. |
| operationId |In genere un GUID condiviso tra gli eventi corrispondenti a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Proprietà dell'evento. |
| status |Stringa. Stato dell'operazione. I valori comuni includono: Started, In Progress, Succeeded, Failed, Active e Resolved. |
| subStatus |In genere include il codice di stato HTTP della chiamata REST corrispondente. Può includere anche altre stringhe che descrivono uno stato secondario. I valori di stato secondario comuni includono OK (codice di stato HTTP: 200), Created (codice di stato HTTP: 201), Accepted (codice di stato HTTP: 202), No Content (codice di stato HTTP: 204), Bad Request (codice di stato HTTP: 400), Not Found (codice di stato HTTP: 404), Conflict (codice di stato HTTP: 409), Internal Server Error (codice di stato HTTP: 500), Service Unavailable (codice di stato HTTP: 503), Gateway Timeout (codice di stato HTTP: 504). |

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sul log attività](monitoring-overview-activity-logs.md).
* [Eseguire gli script di Automazione di Azure (runbook) sugli avvisi di Azure](http://go.microsoft.com/fwlink/?LinkId=627081).
* [Usare un'app per la logica per inviare SMS tramite Twilio da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del log attività.
* [Usare un'app per la logica per inviare un messaggio Slack da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del log attività.
* [Usare un'app per la logica per inviare un messaggio a una coda di Azure da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del log attività.
