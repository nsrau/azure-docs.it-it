---
title: Configurare le notifiche di integrità dei servizi di Azure per i sistemi di gestione dei problemi esistenti usando un webhook
description: Inviare notifiche personalizzate sugli eventi di integrità del servizio al sistema di gestione dei problemi esistente.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 3/27/2018
ms.openlocfilehash: 8f84b43519c197797b39397cfd15c4f90444177c
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854375"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Usare un webhook per configurare le notifiche di integrità per i sistemi di gestione dei problemi

Questo articolo illustra come configurare gli avvisi di integrità dei servizi di Azure per inviare dati tramite webhook al sistema di notifica esistente.

È possibile configurare gli avvisi di integrità del servizio per ricevere notifiche tramite SMS o messaggi di posta elettronica quando si verifica un evento imprevisto di servizio di Azure.

Tuttavia, è possibile che si disponga già di un sistema di notifica esterno esistente che si preferisce usare. Questo articolo identifica le parti più importanti del payload del webhook. Viene descritto come creare avvisi personalizzati per ricevere una notifica quando si verificano problemi relativi al servizio.

Se si vuole usare un'integrazione preconfigurata, vedere:
* [Configurare avvisi con ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurare avvisi con PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurare avvisi con OpsGenie](service-health-alert-webhook-opsgenie.md)

**Guarda un video introduttivo:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Configurare una notifica personalizzata usando il payload del webhook di integrità del servizio
Per configurare l'integrazione di Webhook personalizzata, è necessario analizzare il payload JSON inviato tramite la notifica sull'integrità del servizio.

Vedere [un payload di esempio](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` del webhook.

È possibile verificare che si tratta di un avviso di integrità del servizio `context.eventSource == "ServiceHealth"`osservando. Di seguito sono riportate le proprietà più rilevanti:
- **Data. Context. activityLog. status**
- **Data. Context. activityLog. Level**
- **Data. Context. activityLog. subscriptionId**
- **Data. Context. activityLog. Properties. title**
- **Data. Context. activityLog. Properties. impactStartTime**
- **Data. Context. activityLog. Properties. Communication**
- **Data. Context. activityLog. Properties. impactedServices**
- **Data. Context. activityLog. Properties. trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Creare un collegamento al dashboard di integrità dei servizi per un evento imprevisto
È possibile creare un collegamento diretto al dashboard per l'integrità dei servizi in un desktop o in un dispositivo mobile generando un URL specifico. Usare il *trackingId* e le prime tre e ultime tre cifre di *SubscriptionId* nel formato seguente:

https<i></i>://app.Azure.com/h/ *&lt;trackingId&gt;* /*prime tre e ultime tre cifre di SubscriptionID&gt; &lt;*

Ad esempio, se *SubscriptionId* è Bba14129-E895-429B-8809-278e836ecdb3 e *trackingId* è 0DET-Urb, l'URL di integrità del servizio è:

https<i></i>://app.Azure.com/h/0DET-Urb/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Usare il livello per rilevare la gravità del problema
Dal più basso al più alto livello di gravità, la proprietà **Level** nel payload può essere informativa, *avviso*, *errore*o *critico*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analizzare i servizi interessati per determinare l'ambito dell'evento imprevisto
Gli avvisi di integrità del servizio possono fornire informazioni sui problemi in più aree e servizi. Per ottenere i dettagli completi, è necessario analizzare il valore di `impactedServices`.

Il contenuto all'interno di è una stringa [JSON](https://json.org/) con escape che, quando senza caratteri di escape, contiene un altro oggetto JSON che può essere analizzato regolarmente. Ad esempio:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

diventa

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

In questo esempio vengono illustrati i problemi relativi a:
- "Avvisi & metriche" in Australia orientale e Australia sudorientale.
- "Servizio app" in Australia sudorientale.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Testare l'integrazione del webhook tramite una richiesta HTTP POST

Attenersi ai passaggi riportati di seguito.

1. Creare il payload di integrità del servizio che si desidera inviare. Vedere un esempio di payload del webhook di integrità del servizio in [webhook per gli avvisi del log attività di Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Creare una richiesta HTTP POST nel modo seguente:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Si dovrebbe ricevere una risposta "2XX-successful".

1. Passare a [PagerDuty](https://www.pagerduty.com/) per confermare che l'integrazione è stata configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- Esaminare lo [schema webhook degli avvisi del log attività](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Informazioni sulle [notifiche per l'integrità del servizio](../azure-monitor/platform/service-notifications.md).
- Altre informazioni sui [gruppi di azione](../azure-monitor/platform/action-groups.md).