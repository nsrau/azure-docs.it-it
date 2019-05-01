---
title: Preparare per la migrazione di avvisi classici di monitoraggio di Azure tramite l'aggiornamento di App per la logica e i runbook
description: Informazioni su come modificare i webhook, App per la logica e i runbook per prepararsi alla migrazione volontaria.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 347c89991cbb4d28b46eafff0a783148793ad2f7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727496"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparare l'App per la logica e i runbook per la migrazione delle regole di avviso classiche

Come [annunciate in precedenza](monitoring-classic-retirement.md), gli avvisi classici in Monitoraggio di Azure verranno ritirati nel luglio 2019. Uno strumento di migrazione è disponibile nel portale di Azure per i clienti che usano le regole di avviso di classiche e che desiderano migrazione si attivano automaticamente.

Se si sceglie di migrare volontariamente le regole di avviso classiche alle nuove regole di avviso, tenere presente che esistono alcune differenze tra i due sistemi. Questo articolo illustra tali differenze e come è possibile preparare per la modifica.

## <a name="api-changes"></a>Modifiche all'API

Le API che creano e gestiscono le regole di avviso di classiche (`microsoft.insights/alertrules`) sono diverse dalle API che creano e gestiscono i nuovi avvisi delle metriche (`microsoft.insights/metricalerts`). Se è a livello di programmazione creare e gestire le regole di avviso di classiche oggi, aggiornare gli script di distribuzione per lavorare con le nuove API.

Nella tabella seguente è un riferimento a interfacce di programmazione per gli avvisi sia classiche e nuove:

|         |Avvisi classici  |Nuovi avvisi delle metriche |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Interfaccia della riga di comando di Azure     | [avviso di monitoraggio AZ](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [avviso delle metriche di monitoraggio AZ](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Riferimento](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |      |
| Modello di Azure Resource Manager | [Per gli avvisi classici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Per i nuovi avvisi delle metriche](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Modifiche di payload di notifica

Il formato di payload di notifica è leggermente diverso tra [regole di avviso classiche](alerts-webhooks.md) e [nuovi avvisi delle metriche](alerts-metric-near-real-time.md#payload-schema). Se si dispone di qualsiasi webhook, app per la logica o le azioni di runbook che vengono generate da regole di avviso classiche, è necessario aggiornare gli endpoint di notifica per accettare il formato di payload di nuovi avvisi delle metriche.

Usare la tabella seguente per eseguire il mapping i campi di payload di webhook dal formato classico al nuovo formato:

|  |Avvisi classici  |Nuovi avvisi delle metriche |
|---------|---------|---------|
|L'avviso è stato attivato o è stato risolto?    | **Stato**       | **data.status** |
|Informazioni contestuali relative all'avviso     | **context**        | **data.context**        |
|Timestamp in corrispondenza del quale l'avviso è stato attivato o risolto     | **context.timestamp**       | **data.context.timestamp**        |
| ID regola di avviso | **context.id** | **data.context.id** |
| Nome regola di avviso | **context.name** | **data.context.name** |
| Descrizione della regola di avviso | **context.description** | **data.context.description** |
| Condizione di regola di avviso | **context.condition** | **data.context.condition** |
| Nome metrica | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Aggregazione temporale (come la metrica viene aggregata nell'intervallo di valutazione)| **data.context.condition.timeAggregation** | **data.context.condition.timeAggregation** |
| Periodo di valutazione | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operatore (come il valore della metrica aggregato viene confrontato con la soglia di) | **context.condition.operator** | **data.context.condition.operator** |
| Soglia | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Valore della metrica | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| ID sottoscrizione | **context.subscriptionId** | **data.context.subscriptionId** |
| Gruppo di risorse della risorsa interessata | **context.resourceGroup** | **data.context.resourceGroup** |
| Nome della risorsa interessata | **context.resourceName** | **data.context.resourceName** |
| Tipo della risorsa interessata | **context.resourceType** | **data.context.resourceType** |
| ID risorsa della risorsa interessata | **context.resourceId** | **data.context.resourceId** |
| Collegamento diretto alla pagina di riepilogo delle risorse del portale | **context.portalLink** | **data.context.portalLink** |
| Campi di payload personalizzato da passare all'app per la logica o webhook | **properties** | **data.properties** |

I payload sono simili, come si può notare. Sono disponibili nella sezione seguente:

- Informazioni dettagliate sulla modifica di App per la logica per lavorare con il nuovo formato.
- Un esempio di runbook che consente di analizzare il payload della notifica per nuovi avvisi.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modificare un'app per la logica per ricevere una notifica di avviso metrica

Se si usa App per la logica con gli avvisi classici, è necessario modificare il codice di app per la logica per analizzare il nuovo payload avvisi delle metriche. A tale scopo, seguire questa procedura:

1. Creare una nuova app per la logica.

1. Usare il modello "Azure Monitor – metriche avviso gestore". Questo modello ha un **richiesta HTTP** trigger con lo schema appropriato definito.

    ![modello di app per la logica](media/alerts-migration/logic-app-template.png "modello di avviso metrica")

1. Aggiungere un'azione per ospitare la logica di elaborazione.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Usare un runbook di automazione che riceve una notifica di avviso metrica

Nell'esempio seguente viene fornito codice di PowerShell da usare nel runbook. Questo codice consente di analizzare il payload per le regole di avviso delle metriche classiche e nuove regole di avviso delle metriche.

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

Per un esempio completo di un runbook che si arresta una macchina virtuale quando viene attivato un avviso, vedere la [documentazione di automazione di Azure](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook).

## <a name="partner-integration-via-webhooks"></a>Integrazione dei partner tramite webhook

La maggior parte degli [partner che si integrano con gli avvisi classici](https://docs.microsoft.com/azure/azure-monitor/platform/partners) supportano già i nuovi avvisi delle metriche attraverso le integrazioni. Integrazioni note che usano già con i nuovi avvisi delle metriche sono:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se si usa un'integrazione di partner che non è elencata qui, verificare con il provider di integrazione che l'integrazione funziona con i nuovi avvisi delle metriche.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Comprendere il funzionamento dello strumento di migrazione](alerts-understand-migration.md)
