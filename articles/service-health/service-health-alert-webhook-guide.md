---
title: "Configurare le notifiche di integrità per i sistemi di gestione dei problemi esistenti usando un webhook | Microsoft Docs"
description: "Inviare notifiche personalizzate sugli eventi di integrità al sistema di gestione dei problemi esistente."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: b6a5f61f61675b825dcfe9c706c80944f5890538
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>Configurare le notifiche di integrità per i sistemi di gestione dei problemi esistenti usando un webhook

Questo articolo illustra come configurare gli avvisi di integrità del servizio in modo che inviino dati tramite webhook al sistema di notifica esistente.

Attualmente è possibile configurare gli avvisi di integrità del servizio in modo che, quando si verifica un evento imprevisto del servizio di Azure, si riceva una notifica tramite un SMS o un messaggio di posta elettronica.
Si potrebbe tuttavia avere già un sistema di notifica esterno che si vuole usare.
Questo documento illustra le parti più importanti del payload del webhook e come è possibile creare avvisi personalizzati da ricevere quando si verificano problemi del servizio.

Se si desidera usare una funzionalità di integrazione preconfigurata, vedere la procedura per:
* [Configurare avvisi con ServiceNow](service-health-alert-webhook-servicenow.md)
* [Configurare avvisi con PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Configurare avvisi con OpsGenie](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>Configurazione di una notifica personalizzata usando il payload di webhook di integrità del servizio
Se si desidera configurare una propria integrazione di webhook personalizzata, è necessario analizzare il payload JSON che viene inviato durante le notifiche di integrità del servizio.

Consultare [questo articolo per vedere un esempio](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) di come si presenta il payload webhook `Service Health`.

È possibile rilevare che si tratta di un avviso di integrità del servizio osservando `context.eventSource == "ServiceHealth"`. Da qui, le proprietà più importanti da inserire sono:
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-azure-service-health-for-an-incident"></a>Creazione di un collegamento diretto a integrità del servizio di Azure per un evento imprevisto
È possibile creare un collegamento diretto all'evento imprevisto di integrità del servizio di Azure personalizzato sul desktop o sul cellulare tramite la generazione di un URL specifico. Usare il `trackingId`, oltre alle prime e ultime tre cifre del proprio `subscriptionId`, in modo da formare:
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

Ad esempio, se il proprio `subscriptionId` è `bba14129-e895-429b-8809-278e836ecdb3` e il `trackingId` è `0DET-URB`, l'URL di integrità del servizio di Azure personalizzato è:

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>Uso del livello per rilevare la gravità del problema
Dalla gravità minima a quella massima, la proprietà `level` del payload può avere uno dei seguenti valori: `Informational`, `Warning`, `Error` e `Critical`.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>Analisi dei servizi interessati per comprendere l'ambito completo dell'evento imprevisto
Gli avvisi di integrità del servizio possono dare informazioni sui problemi di più aree e servizi. Per ottenere i dettagli completi, è necessario analizzare il valore di `impactedServices`.
Il contenuto interno è una stringa [JSON con escape](http://json.org/); quando è senza escape, contiene un altro oggetto JSON che può essere analizzato regolarmente.

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

Ciò indica che ci sono problemi con "avvisi e metriche" sia nell'area Australia orientale che sud-orientale, nonché problemi con il "servizio App" in Australia sud-orientale.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Test dell'integrazione del webhook tramite una richiesta HTTP POST
1. Creare il payload di integrità del servizio che si vuole inviare. È possibile trovare un payload di esempio del webhook di integrità del servizio in [Webhook per gli avvisi del log attività di Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Creare una richiesta HTTP POST nel modo seguente:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Si riceverà una risposta `2XX - Successful`.

4. Passare a [PagerDuty](https://www.pagerduty.com/) per confermare che l'integrazione è stata configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- Esaminare lo [schema webhook degli avvisi del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Informazioni sulle [notifiche per l'integrità del servizio](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Altre informazioni sui [gruppi di azione](../monitoring-and-diagnostics/monitoring-action-groups.md).