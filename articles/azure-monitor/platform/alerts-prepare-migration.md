---
title: Preparare la migrazione degli avvisi classici di Monitoraggio di Azure aggiornando le app per la logica e i runbookPrepare for Azure Monitor classic alerts migration by updating your logic apps and runbooks
description: Informazioni su come modificare webhook, app per la logica e runbook per prepararsi alla migrazione volontaria.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 9219e105acb98424939030af76b526d475585619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665593"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Preparare le app per la logica e manuali operativi per la migrazione delle regole di avviso classiche

Come [annunciato in precedenza,](monitoring-classic-retirement.md)gli avvisi classici in Monitoraggio di Azure vengono ritirati a settembre 2019 (originariamente luglio 2019). Uno strumento di migrazione è disponibile nel portale di Azure per i clienti che usano le regole di avviso classiche e che vogliono attivare la migrazione autonomamente.

> [!NOTE]
> A causa del ritardo nell'implementazione dello strumento di migrazione, la data di pensionamento per la migrazione degli avvisi classici è stata estesa al 31 agosto 2019 dalla data originariamente annunciata del 30 giugno 2019.

Se si sceglie di eseguire volontariamente la migrazione delle regole di avviso classiche a nuove regole di avviso, tenere presente che esistono alcune differenze tra i due sistemi. In questo articolo vengono illustrate queste differenze e come prepararsi per la modifica.

## <a name="api-changes"></a>Modifiche all'API

Le API che creano e gestiscono`microsoft.insights/alertrules`le regole di avviso classiche ( ) sono`microsoft.insights/metricalerts`diverse dalle API che creano e gestiscono nuovi avvisi di metrica ( ). Se si creano e gestiscono oggi le regole di avviso classiche a livello di codice, aggiornare gli script di distribuzione in modo che funzionino con le nuove API.

La tabella seguente è un riferimento alle interfacce a livello di codice per gli avvisi classici e nuovi:

|         |Avvisi classici  |Nuovi avvisi di metrica |
|---------|---------|---------|
|API REST     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts (informazioni in base alle informazioni dettagliate/metriche)](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Interfaccia della riga di comando di Azure     | [az monitor avviso](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [Az avviso sulle metriche del monitor](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Riferimento](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Riferimento](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Modello di Azure Resource Manager | [Per gli avvisi classici](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Per nuovi avvisi di metrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Modifiche al payload delle notifiche

Il formato del payload di notifica è leggermente diverso tra le regole di [avviso classiche](alerts-webhooks.md) e i [nuovi avvisi di metrica.](alerts-metric-near-real-time.md#payload-schema) Se sono presenti azioni webhook, app per la logica o runbook attivate dalle regole di avviso classiche, è necessario aggiornare gli endpoint di notifica per accettare il formato di payload dei nuovi avvisi di metrica.

Utilizzare la tabella seguente per mappare i campi payload del webhook dal formato classico al nuovo formato:

|  |Avvisi classici  |Nuovi avvisi di metrica |
|---------|---------|---------|
|L'avviso è stato attivato o risolto?    | **status**       | **data.status** |
|Informazioni contestuali sull'avviso     | **context**        | **data.contesto**        |
|Indicatore di data e ora in corrispondenza del quale l'avviso è stato attivato o risolto     | **context.timestamp**       | **data.context.timestamp**        |
| ID regola di avviso | **context.id** | **data.context.id** |
| Nome regola di avviso | **context.name** | **data.context.name** |
| Descrizione della regola di avviso | **contesto.descrizione** | **dati.contesto.descrizione** |
| Condizione regola di avviso | **context.condition** | **data.context.condition** |
| Nome metrica | **context.condition.metricName** | **data.context.condition.allOf[0].nomeMetric** |
| Aggregazione temporale (come la metrica viene aggregata nella finestra di valutazione)| **context.condition.timeAggregation** | **context.condition.timeAggregation** |
| Periodo di valutazione | **context.condition.windowSize** | **data.context.condition.windowSize** |
| Operatore (come viene confrontato il valore metrico aggregato con la soglia) | **context.condition.operator** | **data.context.condition.operator** |
| Soglia | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Valore metrico | **context.condition.metricValue (contesto.condizione.valoremetrico)** | **data.context.condition.allOf[0].metricValue** |
| ID sottoscrizione | **context.subscriptionId** | **data.context.subscriptionId** |
| Gruppo di risorse della risorsa interessata | **context.resourceGroup (gruppodi risorse)** | **data.context.resourceGroup** |
| Nome della risorsa interessata | **context.resourceName (nomecontesto)** | **data.contesto.resourceName** |
| Tipo della risorsa interessata | **context.resourceType** | **data.context.resourceType** |
| ID risorsa della risorsa interessata | **context.resourceId (identità)** | **data.context.resourceId** |
| Collegamento diretto alla pagina di riepilogo delle risorse del portale | **context.portalLink (collegamento a contesto)** | **data.contesto.portaleLink** |
| Campi payload personalizzati da passare all'app webhook o per la logica | **Proprietà** | **data.properties** |

I carichi utili sono simili, come si può vedere. La seguente sezione offre:

- Dettagli sulla modifica delle app per la logica per l'utilizzo con il nuovo formato.
- Esempio di runbook che analizza il payload di notifica per i nuovi avvisi.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modificare un'app per la logica per ricevere una notifica di avviso metricaModify a logic app to receive a metric alert notification

Se usi app per la logica con avvisi classici, devi modificare il codice dell'app per la logica per analizzare il nuovo payload degli avvisi di metrica. A tale scopo, seguire questa procedura:

1. Creare una nuova app per la logica.

1. Usare il modello "Monitoraggio di Azure - Gestore avvisi metriche". Questo modello dispone di un trigger di **richiesta HTTP** con lo schema appropriato definito.

    ![logic-app-template](media/alerts-migration/logic-app-template.png "Modello di avviso metrico")

1. Aggiungere un'azione per ospitare la logica di elaborazione.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Usare un runbook di automazione che riceve una notifica di avviso metricaUse an automation runbook that receives a metric alert notification

L'esempio seguente fornisce il codice di PowerShell da usare nel runbook. Questo codice può analizzare i payload sia per le regole di avviso delle metriche classiche che per le nuove regole di avviso delle metriche.

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

Per un esempio completo di un runbook che arresta una macchina virtuale quando viene attivato un avviso, vedere la documentazione di [Automazione di Azure.For](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)a full example of a runbook that stops a virtual machine when an alert is triggered, see the Azure Automation documentation.

## <a name="partner-integration-via-webhooks"></a>Integrazione dei partner tramite webhook

La maggior parte dei [nostri partner che si integrano con gli avvisi classici](https://docs.microsoft.com/azure/azure-monitor/platform/partners) supportano già gli avvisi di metrica più recenti attraverso le loro integrazioni. Le integrazioni note che già funzionano con i nuovi avvisi di metrica sono:Known integrations that already work with new metric alerts are:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Segno4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Se si usa un'integrazione partner non elencata qui, verificare con il provider di integrazione che l'integrazione funziona con nuovi avvisi di metrica.

## <a name="next-steps"></a>Passaggi successivi

- [Come usare lo strumento di migrazione](alerts-using-migration-tool.md)
- [Informazioni sul funzionamento dello strumento di migrazione](alerts-understand-migration.md)
