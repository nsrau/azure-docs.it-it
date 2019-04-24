---
title: Preparare per la migrazione di avvisi classici di monitoraggio di Azure tramite l'aggiornamento di App per la logica e i runbook
description: Informazioni su come modificare i webhook, app per la logica e i runbook per prepararsi alla migrazione volontaria.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 3c47404826d5055d4a82d4842523f790fb11f000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60346879"
---
# <a name="prepare-your-logic-apps-and-run-books-for-classic-alert-rules-migration"></a>Preparare le App per la logica ed eseguire documentazione per la migrazione delle regole di avviso di classico

Come [annunciate in precedenza](monitoring-classic-retirement.md), gli avvisi classici in Monitoraggio di Azure verranno ritirati nel luglio 2019. Lo strumento di migrazione per attivare volontariamente la migrazione è disponibile nel portale di Azure e viene implementata per i clienti che usano le regole di avviso di classiche.

Se si sceglie di migrare volontariamente le regole di avviso classiche alle nuove regole di avviso, esistono alcune differenze tra i due sistemi che è necessario essere consapevoli di. Questo articolo illustra le differenze tra i due sistemi e come è possibile preparare per la modifica.

## <a name="api-changes"></a>Modifiche all'API

Le API usate per creare/gestire le regole di avviso di classiche (`microsoft.insights/alertrules`) sono diverse dalle API usate per creare/gestire i nuovi avvisi delle metriche (`microsoft.insights/metricalerts`). Se a livello di programmazione creare/gestire le regole di avviso di classiche oggi, aggiornare gli script di distribuzione per lavorare con le nuove API.

Nella tabella seguente fornisce un riferimento a interfacce di programmazione per gli avvisi sia classici e nuovi.

|         |Avvisi classici  |Nuovi avvisi delle metriche |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Interfaccia della riga di comando di Azure     | [avviso di monitoraggio AZ](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [avviso delle metriche di monitoraggio AZ](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Riferimento](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Modello di Azure Resource Manager | [Per gli avvisi classici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Per i nuovi avvisi delle metriche](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Modifiche di payload di notifica

Il formato di payload di notifica è leggermente diverso tra [regole di avviso classiche](alerts-webhooks.md) e [nuovi avvisi delle metriche](alerts-metric-near-real-time.md#payload-schema). Se si dispone di un webhook, app per la logica o le azioni di runbook che sia attivate da regole di avviso classiche, è necessario aggiornare gli endpoint di notifica per accettare il formato di payload di nuovi avvisi delle metriche.

Eseguire il mapping tra i payload di webhook di regola di avviso classico e il nuovo payload del webhook degli avvisi delle metriche, è possibile utilizzare la tabella seguente.

|  |Avvisi classici  |Nuovi avvisi delle metriche |
|---------|---------|---------|
|L'avviso è stato attivato o è stato risolto     | status       | data.status |
|Informazioni contestuali relative all'avviso     | context        | data.context        |
|Timestamp in corrispondenza del quale l'avviso è stato attivato o risolto      | context.timestamp       | data.context.timestamp        |
| ID regola di avviso | context.id | data.context.id |
| Nome regola di avviso | context.name | data.context.name |
| Descrizione della regola di avviso | context.description | data.context.description |
| Condizione di regola di avviso | context.condition | data.context.condition|
| Nome metrica | context.condition.metricName| data.context.condition.allOf[0].metricName|
| Aggregazione temporale (come la metrica viene aggregata nell'intervallo di valutazione)|data.context.condition.timeAggregation|data.context.condition.timeAggregation|
| Periodo di valutazione | context.condition.windowSize | data.context.condition.windowSize|
| Operatore (come il valore della metrica aggregato viene confrontato con la soglia di) | context.condition.operator | data.context.condition.operator|
| Soglia | context.condition.threshold| data.context.condition.allOf[0].threshold|
| Valore della metrica | context.condition.metricValue | data.context.condition.allOf[0].metricValue|
| ID sottoscrizione | context.subscriptionId | data.context.subscriptionId|
| Gruppo di risorse della risorsa interessata | context.resourceGroup | data.context.resourceGroup|
| Nome della risorsa interessata | context.resourceName | data.context.resourceName |
| Tipo della risorsa interessata | context.resourceType | data.context.resourceType |
|  ID risorsa della risorsa interessata | context.resourceId | data.context.resourceId |
| Un collegamento diretto alla pagina di riepilogo delle risorse del portale | context.portalLink | data.context.portalLink|
| Campi di payload personalizzato da passare all'app per la logica o webhook | properties |data.properties |

Come può notare, entrambi i payload sono simili. Sezione seguente include informazioni dettagliate sulle App per la logica di esempio e un runbook di esempio per analizzare il payload della notifica per nuovi avvisi.

## <a name="using-a-logic-app-that-receives-a-metric-alert-notification"></a>Usando un'app per la logica che riceve una notifica di avviso metrica

Se si usa App per la logica con gli avvisi classici, è necessario modificare l'app per la logica per analizzare il nuovo payload avvisi delle metriche.

1. Creare una nuova app per la logica.

2. Usare il modello "Azure Monitor – metriche avviso gestore". Questo modello ha un **richiesta HTTP** trigger con lo schema appropriato definito

    ![modello di app per la logica](media/alerts-migration/logic-app-template.png "modello di avviso metrica")

3. Aggiungere un'azione per ospitare la logica di elaborazione.

## <a name="using-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Uso di un runbook di automazione che riceve una notifica di avviso metrica

L'esempio seguente fornisce il codice PowerShell che può essere usato nel runbook che consente di analizzare il payload per le regole di avviso delle metriche classiche e nuove regole di avviso delle metriche.

```PS
## Sample PowerShell code to be used in a runbook to handle parsing of both classic and new metric alerts

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

    # Identify if the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new Metric Alert schema
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic Metric Alert schema
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is not either a classic metric alert or a new metric alert
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    #parse fields related to resource affected
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

Vedere un esempio completo di un runbook che si arresta una macchina virtuale quando viene attivato un avviso [documentazione di automazione di Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integrazione dei partner tramite webhook

La maggior parte degli [partner che si integrano con gli avvisi classici](https://docs.microsoft.com/azure/azure-monitor/platform/partners) supportano già i nuovi avvisi delle metriche attraverso le integrazioni. Integrazioni note che usano già con i nuovi avvisi delle metriche sono elencate di seguito.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se si usa un'integrazione di partner che non è elencata qui, verificare con il provider di integrazione che l'integrazione funziona con i nuovi avvisi delle metriche.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Comprendere il funzionamento dello strumento di migrazione](alerts-understand-migration.md)
