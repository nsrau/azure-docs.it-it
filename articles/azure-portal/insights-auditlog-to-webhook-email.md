<properties
	pageTitle="Azure Insights: Usare i log di controllo per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights. | Microsoft Azure"
	description="Informazioni su come usare le voci del log di controllo del servizio per chiamare URL Web o inviare notifiche di posta elettronica in Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Usare i log di controllo per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights

Questo articolo illustra lo schema di payload quando un evento del log di controllo attiva un webhook e descrive come inviare messaggi di posta elettronica tramite lo stesso evento.

>[AZURE.NOTE] Questa funzionalità è attualmente in anteprima. Nei prossimi mesi verranno apportati miglioramenti all'infrastruttura e alle prestazioni degli avvisi relativi agli eventi. In questa versione di anteprima tale funzionalità è accessibile solo tramite Azure PowerShell e l'interfaccia della riga di comando di Azure. L'accesso alla funzionalità tramite il portale di Azure sarà disponibile in un secondo momento.

## Webhook
I webhook consentono di instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le notifiche personalizzate. È ad esempio possibile eseguire l'instradamento degli avvisi a servizi in grado di gestire una richiesta Web in ingresso per inviare SMS, registrare bug o inviare notifiche tramite chat o servizi di messaggistica. L'URI del webhook deve essere un endpoint HTTP o HTTPS valido.

## Email
È possibile inviare messaggi di posta elettronica a qualsiasi indirizzo di posta elettronica valido. Verrà inviata una notifica anche agli amministratori e ai coamministratori della sottoscrizione in cui viene eseguita la regola.

### Regola di posta elettronica di esempio
È necessario impostare una regola di posta elettronica e una regola di webhook e quindi configurarle in modo che si attivino quando si verifica l'evento del log di controllo. È possibile visualizzare un esempio tramite PowerShell nell'articolo relativo agli [esempi della guida introduttiva di PowerShell per Azure Insights](insights-powershell-samples.md#alert-on-audit-log-event).


## Autenticazione
Esistono due tipi di URI di autenticazione:

1. Autenticazione basata su token tramite il salvataggio dell'URI del webhook con un ID token come parametro di query. Ad esempio, https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Autenticazione di base tramite ID utente e password. Ad esempio, https://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## Schema di payload del webhook di notifica degli eventi del log di controllo
Quando diventa disponibile un nuovo evento, l'avviso relativo agli eventi del log di controllo esegue il webhook configurato con i metadati dell'evento nel payload del webhook. L'esempio seguente mostra lo schema di payload del webhook:

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

|Nome dell'elemento|	Descrizione|
|---|---|
|status |Sempre impostato su "attivato"|
|contesto|Contesto dell'evento|
|resourceProviderName|Provider della risorsa interessata|
|conditionType |"Evento"|
|name |Nome della regola di avviso|
|id |ID risorsa dell'avviso|
|description|	Descrizione impostata sull'avviso dall'autore dell'avviso|
|subscriptionId |GUID della sottoscrizione di Azure|
|timestamp|	Timestamp in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento|
|resourceId |URI dell'ID risorsa che identifica in modo univoco la risorsa|
|resourceGroupName|Nome del gruppo risorse della risorsa interessata|
|properties |Set di coppie <Key  Value> (ad esempio, Dictionary<String  String>) contenente i dettagli relativi all'evento|
|event|Elemento contenente i metadati relativi all'evento|
|autorizzazione|Acquisisce le proprietà RBAC dell'evento. In genere includono "action", "role" e "scope".|
|category | Categoria dell'evento. I valori supportati includono: Administrative, Alert, Security, ServiceHealth, Recommendation|
|caller|Indirizzo di posta elettronica dell'utente che ha eseguito l'operazione, l'attestazione UPN o SPN, secondo la disponibilità. Può essere null per alcune chiamate di sistema.|
|correlationId|	In genere un GUID in formato stringa. Gli eventi con correlationId appartengono alla stessa azione di livello superiore e in genere condividono lo stess valore di correlationId.|
|eventDescription |Testo statico che descrive un evento|
|eventDataId|Identificatore univoco di un evento|
|eventSource |Nome del servizio o dell'infrastruttura di Azure che ha generato l'evento|
|httpRequest|	In genere include "clientRequestId", "clientIpAddress" e "method" (metodo HTTP, ad esempio PUT).|
|level|Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose"|
|operationId|In genere un GUID condiviso tra gli eventi corrispondenti a una singola operazione|
|operationName|Nome dell'operazione|
|properties |L'elemento all'interno dell'elemento eventi contiene le proprietà dell'evento.|
|status|Stringa che descrive lo stato dell'operazione. I valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved|
|subStatus|	In genere include il codice di stato HTTP della chiamata REST corrispondente. Può includere anche altre stringhe che descrivono uno stato secondario. I valori di stato secondario comuni includono: OK (codice di stato HTTP: 200), Created (codice di stato HTTP: 201), Accepted (codice di stato HTTP: 202), No Content (codice di stato HTTP: 204), Bad Request (codice di stato HTTP: 400), Not Found (codice di stato HTTP: 404), Conflict (codice di stato HTTP: 409), Internal Server Error (codice di stato HTTP: 500), Service Unavailable (HTTP stato codice: 503), Gateway Timeout (codice di stato HTTP: 504)|

<!---HONumber=AcomDC_0330_2016-->