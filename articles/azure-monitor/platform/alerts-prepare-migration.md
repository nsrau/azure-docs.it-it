---
title: Aggiornare le app per la logica & manuali operativi per la migrazione degli avvisi
description: Informazioni su come modificare i webhook, le app per la logica e manuali operativi per prepararsi alla migrazione volontaria.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 09db7684c84bbde038c67f9ccfb3f27f6b61bee6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539550"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparare le app per la logica e manuali operativi per la migrazione delle regole di avviso classiche

Come [annunciato in precedenza](monitoring-classic-retirement.md), gli avvisi classici in monitoraggio di Azure verranno ritirati a settembre 2019 (in origine il 2019 luglio). Uno strumento di migrazione è disponibile nel portale di Azure ai clienti che utilizzano le regole di avviso classiche e che desiderano attivare la migrazione autonomamente.

> [!NOTE]
> A causa del ritardo nell'implementazione dello strumento di migrazione, la data di ritiro per la migrazione degli avvisi classica è stata estesa al 31 agosto 2019 dalla data di annuncio iniziale del 30 giugno 2019.

Se si sceglie di migrare volontariamente le regole di avviso classiche alle nuove regole di avviso, tenere presente che esistono alcune differenze tra i due sistemi. In questo articolo vengono illustrate le differenze e il modo in cui è possibile prepararsi per la modifica.

## <a name="api-changes"></a>Modifiche all'API

Le API che creano e gestiscono le regole di avviso classiche ( `microsoft.insights/alertrules` ) sono diverse dalle API che creano e gestiscono nuovi avvisi delle metriche ( `microsoft.insights/metricalerts` ). Se si creano e si gestiscono le regole di avviso classiche a livello di codice, aggiornare gli script di distribuzione in modo che funzionino con le nuove API.

La tabella seguente è un riferimento alle interfacce programmatiche per gli avvisi classici e nuovi:

| Tipo di script di distribuzione | Avvisi classici | Nuovi avvisi delle metriche |
| ---------------------- | -------------- | ----------------- |
|API REST     | [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules)         | [Microsoft. Insights/metricalerts](/rest/api/monitor/metricalerts)       |
|Interfaccia della riga di comando di Azure     | [az monitor alert](/cli/azure/monitor/alert?view=azure-cli-latest)        | [AZ monitor Metrics-avviso](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Riferimento](/powershell/module/az.monitor/add-azmetricalertrule)       |  [Riferimento](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Modello di Azure Resource Manager | [Per gli avvisi classici](./alerts-enable-template.md)|[Per i nuovi avvisi delle metriche](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>Modifiche payload notifica

Il formato del payload di notifica è leggermente diverso tra [le regole di avviso classiche](alerts-webhooks.md) e i [nuovi avvisi delle metriche](alerts-metric-near-real-time.md#payload-schema). Se sono presenti azioni webhook, app per la logica o Runbook attivate dalle regole di avviso classiche, è necessario aggiornare gli endpoint di notifica per accettare il formato di payload dei nuovi avvisi delle metriche.

Usare la tabella seguente per eseguire il mapping dei campi di payload del webhook dal formato classico al nuovo formato:

| Tipo di endpoint di notifica | Avvisi classici | Nuovi avvisi delle metriche |
| -------------------------- | -------------- | ----------------- |
|L'avviso è stato attivato o risolto?    | **Stato**       | **Data. stato** |
|Informazioni contestuali sull'avviso     | **context**        | **Data. Context**        |
|Timestamp in cui l'avviso è stato attivato o risolto     | **context. timestamp**       | **Data. Context. timestamp**        |
| ID regola di avviso | **context.id** | **data.context.id** |
| Nome regola di avviso | **context.name** | **data.context.name** |
| Descrizione della regola di avviso | **context. Description** | **Data. Context. Description** |
| Condizione della regola di avviso | **context. Condition** | **Data. Context. Condition** |
| Nome metrica | **context. Condition. metricaname** | **Data. Context. Condition. allOf [0]. metricname** |
| Aggregazione temporale (modalità di aggregazione della metrica sulla finestra di valutazione)| **context. Condition. timeAggregation** | **context. Condition. timeAggregation** |
| Periodo di valutazione | **context. Condition. windowSize** | **Data. Context. Condition. windowSize** |
| Operatore (come il valore della metrica aggregata viene confrontato con la soglia) | **context. Condition. operator** | **Data. Context. Condition. operator** |
| Soglia | **context. Condition. Threshold** | **Data. Context. Condition. allOf [0]. Threshold** |
| Valore metrica | **context. Condition. metricValue** | **Data. Context. Condition. allOf [0]. metricValue** |
| ID sottoscrizione | **context. subscriptionId** | **Data. Context. subscriptionId** |
| Gruppo di risorse della risorsa interessata | **context. resourceGroup** | **Data. Context. resourceGroup** |
| Nome della risorsa interessata | **context. ResourceName** | **Data. Context. ResourceName** |
| Tipo di risorsa interessata | **context. resourceType** | **Data. Context. resourceType** |
| ID risorsa della risorsa interessata | **context. resourceId** | **Data. Context. resourceId** |
| Collegamento diretto alla pagina di riepilogo delle risorse del portale | **context. portalLink** | **Data. Context. portalLink** |
| Campi di payload personalizzati da passare al webhook o all'app per la logica | **properties** | **Data. Properties** |

I payload sono simili, come si può vedere. La sezione seguente offre:

- Informazioni dettagliate sulla modifica delle app per la logica per l'uso con il nuovo formato.
- Esempio Runbook che analizza il payload delle notifiche per i nuovi avvisi.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modificare un'app per la logica per ricevere una notifica di avviso per la metrica

Se si usano app per la logica con avvisi classici, è necessario modificare il codice dell'app per la logica per analizzare il nuovo payload degli avvisi delle metriche. A tale scopo, seguire questa procedura:

1. Creare una nuova app per la logica.

1. Usare il modello "monitoraggio di Azure-gestore avvisi metriche". Questo modello include un trigger di **richiesta http** con lo schema appropriato definito.

    ![logica-app-modello](media/alerts-migration/logic-app-template.png "Modello di avviso della metrica")

1. Aggiungere un'azione per ospitare la logica di elaborazione.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Usare un Runbook di automazione che riceve una notifica di avviso per la metrica

Nell'esempio seguente viene fornito il codice di PowerShell da usare in Runbook. Questo codice può analizzare i payload per le regole di avviso delle metriche classiche e per le nuove regole di avviso della metrica.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Per un esempio completo di Runbook che interrompe una macchina virtuale quando viene attivato un avviso, vedere la documentazione di [automazione di Azure](../../automation/automation-create-alert-triggered-runbook.md).

## <a name="partner-integration-via-webhooks"></a>Integrazione di partner tramite webhook

La maggior parte dei [partner che si integrano con gli avvisi classici](./partners.md) supporta già avvisi di metrica più recenti tramite le loro integrazioni. Le integrazioni note che già funzionano con nuovi avvisi delle metriche sono:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se si usa un'integrazione partner non elencata, verificare con il provider di integrazione che l'integrazione funzioni con i nuovi avvisi delle metriche.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Informazioni sul funzionamento dello strumento di migrazione](alerts-understand-migration.md)
