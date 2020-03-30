---
title: Inviare notifiche di integrità del servizio di Azure tramite webhookSend Azure Service Health notifications via webhooks
description: Inviare notifiche personalizzate sugli eventi di integrità del servizio al sistema di gestione dei problemi esistente.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062851"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Usare un webhook per configurare le notifiche di integrità per i sistemi di gestione dei problemiUse a webhook to configure health notifications for problem management systems

Questo articolo illustra come configurare gli avvisi di integrità del servizio di Azure per inviare dati tramite webhook al sistema di notifica esistente.

È possibile configurare gli avvisi di integrità del servizio per inviare una notifica tramite SMS o messaggio di posta elettronica quando si verifica un evento imprevisto del servizio di Azure.You can configure Service Health alerts to notify you by text mail or email when an Azure service incident affects you.

Ma si potrebbe già avere un sistema di notifica esterno esistente in atto che si preferisce utilizzare. Questo articolo identifica le parti più importanti del payload del webhook. Viene descritto come creare avvisi personalizzati per notificare quando si verificano problemi di servizio pertinenti.

Se si vuole usare un'integrazione preconfigurata, vedere:
* [Configurare avvisi con ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurare avvisi con PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurare avvisi con OpsGenie](service-health-alert-webhook-opsgenie.md)

**Guarda un video introduttivo:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Configurare una notifica personalizzata utilizzando il payload webhook di integrità del servizioConfigure a custom notification by using the Service Health webhook payload
Per configurare la propria integrazione webhook personalizzata, è necessario analizzare il payload JSON inviato tramite la notifica di integrità del servizio.

Vedere un payload webhook [di esempio.](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth`

È possibile verificare che si tratti di `context.eventSource == "ServiceHealth"`un avviso di integrità del servizio esaminando . Le seguenti proprietà sono le più rilevanti:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Creare un collegamento al dashboard Integrità dei servizi per un evento imprevistoCreate a link to the Service Health dashboard for an incident
È possibile creare un collegamento diretto al dashboard Integrità dei servizi in un dispositivo desktop o mobile generando un URL specializzato. Usare *trackingId* e le prime tre e le ultime tre cifre di subscriptionId in questo formato:Use the trackingId and the first three and last three digits of your *subscriptionId* in this format:

<i></i>https://app.azure.com/h/*&lt;trackingId&gt;*/*&lt;prime three and last&gt; three digits of subscriptionId*

Ad esempio, se *subscriptionId* è bba14129-e895-429b-8809-278e836ecdb3 e *trackingId* è 0DET-URB, l'URL di integrità del servizio è:

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Utilizzare il livello per rilevare la gravità del problema
Dal livello di gravità più basso a quello più alto, la proprietà **level** nel payload può essere *Informativa*, *Avviso*, *Errore*o *Critico*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analizzare i servizi interessati per determinare l'ambito dell'evento imprevistoParse the impacted services to determine the incident scope
Gli avvisi di integrità del servizio possono informare l'utente sui problemi in più aree e servizi. Per ottenere i dettagli completi, è `impactedServices`necessario analizzare il valore di .

Il contenuto contenuto all'interno è una stringa [JSON](https://json.org/) con caratteri di escape che, quando non viene sottoposta a escape, contiene un altro oggetto JSON che può essere analizzato regolarmente. Ad esempio:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Diventa:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Questo esempio mostra i problemi per:
- "Avvisi & metriche" in Australia Est e Australia Sud-Est.
- "App Service" in Australia Sud-Est.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Testare l'integrazione webhook tramite una richiesta HTTP POSTTest your webhook integration via an HTTP POST request

A tale scopo, seguire questa procedura:

1. Creare il payload di integrità del servizio che si desidera inviare. Vedere un esempio di payload webhook di integrità del servizio in Webhook per avvisi del log attività di Azure.See an example service health webhook payload at [Webhooks for Azure activity log alerts](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Creare una richiesta HTTP POST nel modo seguente:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Si dovrebbe ricevere una risposta "2XX - Successful".

1. Passare a [PagerDuty](https://www.pagerduty.com/) per confermare che l'integrazione è stata configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- Esaminare lo schema webhook degli avvisi del [log attività.](../azure-monitor/platform/activity-log-alerts-webhook.md) 
- Informazioni sulle notifiche di integrità del [servizio](../azure-monitor/platform/service-notifications.md).
- Ulteriori informazioni sui [gruppi di azioni](../azure-monitor/platform/action-groups.md).