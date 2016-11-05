---
title: Configurare webhook negli avvisi del registro attività di Azure | Microsoft Docs
description: 'Informazioni su come usare gli avvisi del registro attività per chiamare webhook. '
author: kamathashwin
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: ashwink

---
# Configurare un webhook in un avviso del registro attività di Azure
I webhook consentono di instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le azioni personalizzate. È possibile usare un webhook in un avviso per instradarlo a servizi che inviano SMS, registrano bug, inviano notifiche a un team tramite servizi di messaggistica/chat o eseguono un numero qualsiasi di altre azioni. Questo articolo descrive come impostare un webhook in un avviso del registro attività di Azure e illustra il payload per l'esecuzione di un'azione HTTP POST in un webhook. Per informazioni sulla configurazione e lo schema di un avviso relativo alle metriche di Azure, [vedere invece questa pagina](../monitoring-and-diagnostics/insights-webhooks-alerts.md). È anche possibile impostare un avviso del registro attività per l'invio di un messaggio di posta all'attivazione.

> [!NOTE]
> Questa funzionalità è attualmente in anteprima, quindi la qualità e le prestazioni saranno variabili.
> 
> 

È possibile configurare un avviso del registro attività usando i [cmdlet di Azure PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md#create-alert-rules), l'[interfaccia della riga di comando multipiattaforma](../monitoring-and-diagnostics/insights-cli-samples.md#work-with-alerts) o l'[API REST di Insights](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## Autenticazione del webhook
L'autenticazione del webhook può essere eseguita con uno di questi metodi:

1. **Autorizzazione basata su token**: l'URI del webhook viene salvato con un ID token, ad esempio `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorizzazione di base**: l'URI del webhook viene salvato con nome utente e password, ad esempio `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## Schema del payload
L'operazione POST contiene il seguente payload e schema JSON per tutti gli avvisi basati sul registro attività. Questo schema è simile a quello usato per gli avvisi basati su metriche.

```
{
        "status": "Activated",
        "context": {
                "resourceProviderName": "Microsoft.Web",
                "event": {
                        "$type": "Microsoft.WindowsAzure.Management.Monitoring.Automation.Notifications.GenericNotifications.Datacontracts.InstanceEventContext, Microsoft.WindowsAzure.Management.Mon.Automation",
                        "authorization": {
                                "action": "Microsoft.Web/sites/start/action",
                                "scope": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest"
                        },
                        "eventDataId": "327caaca-08d7-41b1-86d8-27d0a7adb92d",
                        "category": "Administrative",
                        "caller": "myname@mycompany.com",
                        "httpRequest": {
                                "clientRequestId": "f58cead8-c9ed-43af-8710-55e64def208d",
                                "clientIpAddress": "104.43.166.155",
                                "method": "POST"
                        },
                        "status": "Succeeded",
                        "subStatus": "OK",
                        "level": "Informational",
                        "correlationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "eventDescription": "",
                        "operationName": "Microsoft.Web/sites/start/action",
                        "operationId": "4a40beaa-6a63-4d92-85c4-923a25abb590",
                        "properties": {
                                "$type": "Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage",
                                "statusCode": "OK",
                                "serviceRequestId": "f7716681-496a-4f5c-8d14-d564bcf54714"
                        }
                },
                "timestamp": "Friday, March 11, 2016 9:13:23 PM",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/alertonevent2",
                "name": "alertonevent2",
                "description": "test alert on event start",
                "conditionType": "Event",
                "subscriptionId": "s1",
                "resourceId": "/subscriptions/s1/resourcegroups/rg1/providers/Microsoft.Web/sites/leoalerttest",
                "resourceGroupName": "rg1"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```

| Nome dell'elemento | Descrizione |
| --- | --- |
| status |Usato per avvisi relativi alle metriche. Sempre impostato su "Activated" per gli avvisi del registro attività. |
| context |Contesto dell'evento. |
| resourceProviderName |Provider della risorsa interessata. |
| conditionType |Sempre "Event". |
| name |Nome della regola di avviso. |
| id |ID risorsa dell'avviso. |
| description |Descrizione dell'avviso definita durante la creazione dell'avviso. |
| subscriptionId |ID sottoscrizione di Azure. |
| timestamp |Data e ora in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta. |
| resourceId |ID risorsa della risorsa interessata. |
| resourceGroupName |Nome del gruppo di risorse della risorsa interessata |
| properties |Set di coppie `<Key, Value>`, ad esempio `Dictionary<String, String>`, contenente i dettagli relativi all'evento. |
| event |Elemento contenente i metadati relativi all'evento. |
| autorizzazione |Proprietà di Controllo degli accessi in base al ruolo dell'evento. Includono in genere "action", "role" e "scope". |
| category |Categoria dell'evento. I valori supportati includono Administrative, Alert, Security, ServiceHealth, Recommendation. |
| caller |Indirizzo di posta elettronica dell'utente che ha eseguito l'operazione, attestazione UPN o attestazione SPN, a seconda della disponibilità. Può essere null per alcune chiamate di sistema. |
| correlationId |In genere un GUID in formato stringa. Gli eventi con correlationId appartengono alla stessa azione di livello superiore e in genere condividono un elemento correlationId. |
| eventDescription |Testo statico che descrive l'evento. |
| eventDataId |Identificatore univoco dell'evento. |
| eventSource |Nome del servizio o dell'infrastruttura di Azure che ha generato l'evento. |
| httpRequest |In genere include "clientRequestId", "clientIpAddress" e "method" (metodo HTTP, ad esempio PUT). |
| level |Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose". |
| operationId |In genere un GUID condiviso tra gli eventi corrispondenti a una singola operazione. |
| operationName |Nome dell'operazione. |
| properties |Proprietà dell'evento. |
| status |Stringa. Stato dell'operazione. I valori comuni includono: "Started", "In Progress", "Succeeded", "Failed", "Active", "Resolved". |
| subStatus |In genere include il codice di stato HTTP della chiamata REST corrispondente. Può includere anche altre stringhe che descrivono uno stato secondario. I valori di stato secondario comuni includono: OK (codice di stato HTTP: 200), Created (codice di stato HTTP: 201), Accepted (codice di stato HTTP: 202), No Content (codice di stato HTTP: 204), Bad Request (codice di stato HTTP: 400), Not Found (codice di stato HTTP: 404), Conflict (codice di stato HTTP: 409), Internal Server Error (codice di stato HTTP: 500), Service Unavailable (codice di stato HTTP: 503), Gateway Timeout (codice di stato HTTP: 504) |

## Passaggi successivi
* [Altre informazioni sul registro attività](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)
* [Eseguire gli script di Automazione di Azure (runbook) sugli avvisi di Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Usare l'app per la logica per inviare SMS tramite Twilio da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del registro attività.
* [Usare l'app per la logica per inviare un messaggio Slack da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del registro attività.
* [Usare l'app per la logica per inviare un messaggio a una coda di Azure da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Questo esempio si riferisce agli avvisi relativi alle metriche, ma può essere modificato per funzionare con un avviso del registro attività.

<!---HONumber=AcomDC_0921_2016-->