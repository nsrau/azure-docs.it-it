---
title: "Chiamare un webhook negli avvisi dei log attività di Azure | Documentazione Microsoft"
description: "Instradare gli eventi del log attività ad altri servizi per azioni personalizzate. Inviare ad esempio SMS, registrare i bug o inviare notifiche al team tramite servizio di chat o messaggistica."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 64d333d1-7f37-4a00-9d16-dda6e69a113b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: johnkem
ms.openlocfilehash: 08467aed4e1601b32598fc42515d9c38b601a9d4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="call-a-webhook-on-azure-activity-log-alerts"></a>Chiamare un webhook negli avvisi dei log attività di Azure
I webhook consentono di instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le azioni personalizzate. È possibile usare un webhook in un avviso per instradarlo a servizi che inviano SMS, registrano bug, inviano notifiche a un team tramite servizi di messaggistica/chat o eseguono un numero qualsiasi di altre azioni. Questo articolo descrive come impostare un webhook da chiamare quando viene generato un avviso dei log attività di Azure. L'articolo illustra anche il modo in cui il payload per il protocollo HTTP POST viene percepito da un webhook. Per informazioni sulla configurazione e lo schema di un avviso relativo alle metriche di Azure, [vedere invece questa pagina](insights-webhooks-alerts.md). È anche possibile impostare un avviso del registro attività per l'invio di un messaggio di posta all'attivazione.

> [!NOTE]
> Questa funzionalità è attualmente disponibile in anteprima e verrà rimossa in futuro.
>
>

È possibile configurare un avviso del log attività usando i [cmdlet di Azure PowerShell](insights-powershell-samples.md#create-metric-alerts), l'[interfaccia della riga di comando multipiattaforma](insights-cli-samples.md#work-with-alerts) o l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx). Attualmente, non è possibile usare il portale di Azure per la configurazione.

## <a name="authenticating-the-webhook"></a>Autenticazione del webhook
L'autenticazione del webhook può essere eseguita con uno di questi metodi:

1. **Autorizzazione basata su token**: l'URI del webhook viene salvato con un ID token, ad esempio `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorizzazione di base**: l'URI del webhook viene salvato con nome utente e password, ad esempio `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schema del payload
L'operazione POST contiene il seguente payload e schema JSON per tutti gli avvisi basati sul registro attività. Questo schema è simile a quello usato per gli avvisi basati su metriche.

```json
{
    "WebhookName": "Alert1515526229589",
    "RequestBody": {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "authorization": {
                        "action": "Microsoft.Compute/virtualMachines/deallocate/action",
                        "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1"
                    },
                    "channels": "Operation",
                    "claims": {
                        "aud": "https://management.core.windows.net/",
                        "iss": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "iat": "1234567890",
                        "nbf": "1234567890",
                        "exp": "1234567890",
                        "aio": "Y2NgYBD8ZLlhu27JU6WZsXemMIvVAAA=",
                        "appid": "00000000-0000-0000-0000-000000000000",
                        "appidacr": "2",
                        "e_exp": "262800",
                        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/00000000-0000-0000-0000-000000000000/",
                        "http://schemas.microsoft.com/identity/claims/objectidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "00000000-0000-0000-0000-000000000000",
                        "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                        "uti": "XnCk46TrDkOQXwo49Y8fAA",
                        "ver": "1.0"
                    },
                    "caller": "00000000-0000-0000-0000-000000000000",
                    "correlationId": "00000000-0000-0000-0000-000000000000",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-01-09T20:11:25.8410967+00:00",
                    "eventDataId": "00000000-0000-0000-0000-000000000000",
                    "level": "Informational",
                    "operationName": "Microsoft.Compute/virtualMachines/deallocate/action",
                    "operationId": "00000000-0000-0000-0000-000000000000",
                    "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
                    "resourceGroupName": "ContosoVM",
                    "resourceProviderName": "Microsoft.Compute",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "submissionTimestamp": "2018-01-09T20:11:40.2986126+00:00",
                    "resourceType": "Microsoft.Compute/virtualMachines"
                }
            },
            "properties": {}
        }
    },
    "RequestHeader": {
        "Expect": "100-continue",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "IcMBroadcaster/1.0",
        "X-CorrelationContext": "RkkKACgAAAACAAAAEADlBbM7x86VTrHdQ2JlmlxoAQAQALwazYvJ/INPskb8S5QzgDk=",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

| Nome dell'elemento | DESCRIZIONE |
| --- | --- |
| status |Usato per avvisi relativi alle metriche. Sempre impostato su "Activated" per gli avvisi del registro attività. |
| context |Contesto dell'evento. |
| activityLog | Proprietà del log dell'evento.|
| autorizzazione |Proprietà di Controllo degli accessi in base al ruolo dell'evento. Includono in genere "action", "role" e "scope". |
| action | Azione acquisita dall'avviso. |
| scope | Ambito dell'avviso (ad es. risorsa).|
| channels | Operazione |
| claims | Raccolta di informazioni correlate alle attestazioni. |
| caller |GUID o nome dell'utente che ha eseguito l'operazione, attestazione UPN o attestazione SPN, a seconda della disponibilità. Può essere null per alcune chiamate di sistema. |
| correlationId |In genere un GUID in formato stringa. Gli eventi con correlationId appartengono alla stessa azione di livello superiore e in genere condividono un elemento correlationId. |
| description |Descrizione dell'avviso definita durante la creazione dell'avviso. |
| eventSource |Nome del servizio o dell'infrastruttura di Azure che ha generato l'evento. |
| eventTimestamp |Ora in cui si è verificato l'evento. |
| eventDataId |Identificatore univoco dell'evento. |
| level |Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose." |
| operationName |Nome dell'operazione. |
| operationId |In genere un GUID condiviso tra gli eventi corrispondenti a una singola operazione. |
| ResourceId |ID risorsa della risorsa interessata. |
| resourceGroupName |Nome del gruppo di risorse della risorsa interessata |
| resourceProviderName |Provider della risorsa interessata. |
| status |Stringa. Stato dell'operazione. I valori comuni includono: "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved". |
| subStatus |In genere include il codice di stato HTTP della chiamata REST corrispondente. Può includere anche altre stringhe che descrivono uno stato secondario. I valori di stato secondario comuni includono: OK (codice di stato HTTP: 200), Created (codice di stato HTTP: 201), Accepted (codice di stato HTTP: 202), No Content (codice di stato HTTP: 204), Bad Request (codice di stato HTTP: 400), Not Found (codice di stato HTTP: 404), Conflict (codice di stato HTTP: 409), Internal Server Error (codice di stato HTTP: 500), Service Unavailable (codice di stato HTTP: 503), Gateway Timeout (codice di stato HTTP: 504) |
| subscriptionId |ID sottoscrizione di Azure. |
| submissionTimestamp |Data e ora in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta. |
| resourceType | Tipo di risorsa che ha generato l'evento.|
| properties |Set di coppie `<Key, Value>`, ad esempio `Dictionary<String, String>`, contenente i dettagli relativi all'evento. |

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sul log attività](monitoring-overview-activity-logs.md)
* [Eseguire gli script di Automazione di Azure (runbook) sugli avvisi di Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Usare l'app per la logica per inviare SMS tramite Twilio da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del registro attività.
* [Usare l'app per la logica per inviare un messaggio Slack da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del registro attività.
* [Usare l'app per la logica per inviare un messaggio a una coda di Azure da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del registro attività.
