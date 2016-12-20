---
title: Configurare webhook negli avvisi relativi alle metriche di Azure | Microsoft Docs
description: Reindirizzare gli avvisi di Azure ad altri sistemi non Azure
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 8b3ae540-1d19-4f3d-a635-376042f8a5bb
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 62d6c15b9c360c74dc5d9436833bb4666048e523


---
# <a name="configure-a-webhook-on-an-azure-metric-alert"></a>Configurare un webhook in un avviso relativo alle metriche di Azure
I webhook consentono di instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le azioni personalizzate. È possibile usare un webhook in un avviso per instradarlo a servizi che inviano SMS, registrano bug, inviano notifiche a un team tramite servizi di messaggistica/chat o eseguono un numero qualsiasi di altre azioni. Questo articolo descrive come impostare un webhook in un avviso relativo alle metriche di Azure e illustra il payload per l'esecuzione di un'azione HTTP POST in un webhook. Per informazioni sulla configurazione e lo schema di un avviso del registro attività di Azure (avvisi per eventi), [vedere invece questa pagina](insights-auditlog-to-webhook-email.md).

Gli avvisi di Azure eseguono l'azione HTTP POST per il contenuto degli avvisi in formato JSON, con lo schema definito di seguito, in un URI webhook specificato durante la creazione dell'avviso. L'URI deve essere un endpoint HTTP o HTTPS valido. Azure inserisce una voce per ogni richiesta quando viene attivato un avviso.

## <a name="configuring-webhooks-via-the-portal"></a>Configurazione di webhook tramite il portale
È possibile aggiungere o aggiornare l'URI del webhook nella schermata di creazione/aggiornamento degli avvisi nel [portale](https://portal.azure.com/).

![Aggiungere una regola di avviso](./media/insights-webhooks-alerts/Alertwebhook.png)

È anche possibile configurare un avviso da inserire nell'URI di un webhook usando i [cmdlet di Azure PowerShell](insights-powershell-samples.md#create-alert-rules), l'[interfaccia della riga di comando multipiattaforma](insights-cli-samples.md#work-with-alerts) o l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticating-the-webhook"></a>Autenticazione del webhook
L'autenticazione del webhook può essere eseguita con uno di questi metodi:

1. **Autorizzazione basata su token**: l'URI del webhook viene salvato con un ID token, ad esempio `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`
2. **Autorizzazione di base**: l'URI del webhook viene salvato con nome utente e password, ad esempio `https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar`

## <a name="payload-schema"></a>Schema del payload
L'operazione POST contiene il seguente payload e schema JSON per tutti gli avvisi basati su metriche.

```JSON
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```


| Campo | Mandatory | Set fisso di valori | Note |
|:--- |:--- |:--- |:--- |
| status |S |"Activated", "Resolved" |Stato dell'avviso in base alle condizioni impostate. |
| context |S | |Contesto dell'avviso. |
| timestamp |S | |Ora in cui è stato attivato l'avviso. |
| id |S | |Ogni regola di avviso ha un ID univoco. |
| name |S | |Nome dell'avviso. |
| description |S | |Descrizione dell'avviso. |
| conditionType |S |"Metric", "Event" |Sono supportati due tipi di avviso, uno basato su una condizione di metrica e l'altro basato su un evento nel registro attività. Usare questo valore per verificare se l'avviso si basa sulla metrica o sull'evento. |
| condition |S | |Campi specifici da verificare in base al valore di conditionType. |
| metricName |Per avvisi relativi alle metriche | |Nome della metrica che definisce l'oggetto monitorato dalla regola. |
| metricUnit |Per avvisi relativi alle metriche |"Bytes", "BytesPerSecond", "Count", "CountPerSecond", "Percent", "Seconds" |Unità consentita nella metrica. [I valori consentiti sono elencati qui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Per avvisi relativi alle metriche | |Valore effettivo della metrica che ha generato l'avviso. |
| threshold |Per avvisi relativi alle metriche | |Valore soglia al quale viene attivato l'avviso. |
| windowSize |Per avvisi relativi alle metriche | |Periodo di tempo usato per monitorare l'attività degli avvisi in base alla soglia. Deve essere compreso tra 5 minuti e 1 giorno. Il formato della durata è conforme a ISO 8601. |
| timeAggregation |Per avvisi relativi alle metriche |"Average", "Last", "Maximum", "Minimum", "None", "Total" |Definisce come i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". [I valori consentiti sono elencati qui](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Per avvisi relativi alle metriche | |Operatore usato per confrontare i dati metrici attuali con la soglia impostata. |
| subscriptionId |S | |ID sottoscrizione di Azure. |
| resourceGroupName |S | |Nome del gruppo di risorse della risorsa interessata. |
| resourceName |S | |Nome della risorsa interessata. |
| resourceType |S | |Tipo della risorsa interessata. |
| resourceId |S | |ID risorsa della risorsa interessata. |
| resourceRegion |S | |Area o posizione della risorsa interessata. |
| portalLink |S | |Collegamento diretto alla pagina di riepilogo delle risorse del portale. |
| properties |N |Facoltativo |Set di coppie `<Key, Value>`, ad esempio `Dictionary<String, String>`, contenente i dettagli relativi all'evento. Il campo properties è facoltativo. In un flusso di lavoro basato su un'interfaccia utente personalizzata o un'app per la logica, gli utenti possono immettere una coppia chiave/valori che può essere passata tramite il payload. Il metodo alternativo per passare le proprietà personalizzate al webhook è rappresentato dall'URI del webhook stesso (sotto forma di parametri di query) |

> [!NOTE]
> Il campo properties può essere impostato solo usando l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sugli avvisi di Azure e sui webhook, vedere il video sull' [integrazione degli avvisi di Azure con PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080)
* [Eseguire gli script di Automazione di Azure (runbook) sugli avvisi di Azure](http://go.microsoft.com/fwlink/?LinkId=627081)
* [Usare l'app per la logica per inviare SMS tramite Twilio da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* [Usare l'app per la logica per inviare un messaggio Slack da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
* [Usare l'app per la logica per inviare un messaggio a una coda di Azure da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)




<!--HONumber=Nov16_HO3-->


