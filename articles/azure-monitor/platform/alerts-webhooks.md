---
title: Chiamare un webhook con un avviso di metrica classico in monitoraggio di Azure
description: Informazioni su come reindirizzare gli avvisi delle metriche di Azure ad altri sistemi non Azure.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 88de4464e5b95b49e76e5d9c4f7dc0d6732076e1
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286169"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Chiamare un webhook con un avviso di metrica classico in monitoraggio di Azure
È possibile usare i webhook per instradare le notifiche di avviso di Azure ad altri sistemi per la post-elaborazione o le azioni personalizzate. È possibile usare un webhook in un avviso per instradarlo a servizi che inviano SMS, registrano bug, inviano notifiche a un team tramite chat o servizi di messaggistica oppure per varie altre azioni. 

Questo articolo descrive come impostare un webhook per un avviso di metrica di Azure. L'articolo illustra anche le caratteristiche del payload per un'operazione HTTP POST a un webhook. Per informazioni sulla configurazione e lo schema per un avviso del log attività di Azure (avviso per eventi), vedere [Chiamare un webhook negli avvisi dei log attività di Azure](alerts-log-webhook.md).

Gli avvisi di Azure usano HTTP POST per inviare il contenuto degli avvisi in formato JSON a un URI webhook specificato durante la creazione dell'avviso. Lo schema è definito più avanti in questo articolo. L'URI deve essere un endpoint HTTP o HTTPS valido. Azure inserisce una voce per ogni richiesta quando viene attivato un avviso.

## <a name="configure-webhooks-via-the-azure-portal"></a>Configurare webhook tramite il portale di Azure
Per aggiungere o aggiornare l'URI del webhook, nel [portale di Azure](https://portal.azure.com/) passare alla schermata di **creazione/aggiornamento degli avvisi**.

![Riquadro Aggiungi una regola di avviso](./media/alerts-webhooks/Alertwebhook.png)

È anche possibile configurare un avviso da inserire nell'URI di un webhook usando i [cmdlet di Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), l'[interfaccia della riga di comando multipiattaforma](../../azure-monitor/platform/cli-samples.md#work-with-alerts) o l'[API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autenticazione del webhook
È possibile autenticare il webhook usando l'autorizzazione basata su token. L'URI del webhook viene salvato con un ID token. Ad esempio: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Schema del payload
L'operazione POST contiene il payload e lo schema JSON seguenti per tutti gli avvisi basati su metriche:

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


| Campo | Mandatory | Set di valori fisso | note |
|:--- |:--- |:--- |:--- |
| status |S |Activated, Resolved |Stato dell'avviso in base alle condizioni impostate. |
| context |S | |Contesto dell'avviso. |
| timestamp |S | |Ora in cui è stato attivato l'avviso. |
| id |S | |Ogni regola di avviso ha un ID univoco. |
| Nome |S | |Nome dell'avviso. |
| Descrizione |S | |Descrizione dell'avviso. |
| conditionType |S |Metric, Event |Sono supportati due tipi di avviso: metrica ed evento. Gli avvisi di metrica sono basati su una condizione di metrica. Gli avvisi di eventi sono basati su un evento nel log attività. Usare questo valore per controllare se l'avviso è basato su una metrica o su un evento. |
| condition |S | |Campi specifici da controllare in base al valore di **conditionType**. |
| metricName |Per avvisi di metrica | |Nome della metrica che definisce l'oggetto monitorato dalla regola. |
| metricUnit |Per avvisi di metrica |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |Unità consentita nella metrica. Vedere i [valori consentiti](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Per avvisi di metrica | |Valore effettivo della metrica che ha generato l'avviso. |
| threshold |Per avvisi di metrica | |Valore soglia al quale viene attivato l'avviso. |
| windowSize |Per avvisi di metrica | |Periodo di tempo usato per monitorare l'attività degli avvisi in base alla soglia. Il valore deve essere compreso tra 5 minuti e 1 giorno. Il valore deve essere nel formato di durata ISO 8601. |
| timeAggregation |Per avvisi di metrica |Average, Last, Maximum, Minimum, None, Total |Definisce come i dati raccolti devono essere combinati nel tempo. Il valore predefinito è "Average". Vedere i [valori consentiti](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Per avvisi di metrica | |Operatore usato per confrontare i dati di metrica attuali con la soglia impostata. |
| subscriptionId |S | |ID sottoscrizione di Azure. |
| resourceGroupName |S | |Nome del gruppo di risorse per la risorsa interessata. |
| resourceName |S | |Nome della risorsa interessata. |
| resourceType |S | |Tipo della risorsa interessata. |
| resourceId |S | |ID della risorsa interessata. |
| resourceRegion |S | |Area o posizione della risorsa interessata. |
| portalLink |S | |Collegamento diretto alla pagina di riepilogo delle risorse del portale. |
| properties |N |Facoltativo |Set di coppie chiave/valore contenente i dettagli sull'evento. Ad esempio `Dictionary<String, String>`. Il campo properties è facoltativo. In un flusso di lavoro basato su un'interfaccia utente personalizzata o un'app per la logica, gli utenti possono immettere una coppia chiave/valore che può essere passata tramite il payload. Un metodo alternativo per passare le proprietà personalizzate al webhook è rappresentato dall'URI del webhook stesso (sotto forma di parametri di query). |

> [!NOTE]
> È possibile impostare il campo delle **proprietà** solo tramite le [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sugli avvisi di Azure e sui webhook, vedere il video sull' [integrazione degli avvisi di Azure con PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Informazioni su come [eseguire gli script di Automazione di Azure (runbook) sugli avvisi di Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Informazioni su come [usare un'app per la logica per inviare SMS tramite Twilio da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Informazioni su come [usare un'app per la logica per inviare un messaggio Slack da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Informazioni su come [usare un'app per la logica per inviare un messaggio a una coda di Azure da un avviso di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

