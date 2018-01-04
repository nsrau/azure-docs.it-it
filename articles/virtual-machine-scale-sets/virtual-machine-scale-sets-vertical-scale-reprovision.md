---
title: "Ridimensionare verticalmente set di scalabilità di macchine virtuali di Azure | Microsoft Docs"
description: "Come eseguire la scalabilità verticale di una macchina virtuale in risposta agli avvisi di monitoraggio tramite Automazione di Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: negat
ms.openlocfilehash: 6e4733e023d1dc27fb099216f9afea07fe07446c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Imposta scalabilità verticale automatica con scalabilità della macchina virtuale
In questo articolo viene descritto come ridimensionare in verticale i [set di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/) di Azure con o senza un nuovo provisioning. Per la scalabilità verticale di macchine virtuali che non sono presenti nel set di scalabilità, fare riferimento a [scalare verticalmente macchina virtuale di Azure con automazione di Azure](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per ridimensionamento verticale, detto anche *aumento delle prestazioni* e *riduzione delle prestazioni*, si intende l'aumento o la riduzione delle dimensioni delle macchine virtuali (VM) in risposta a un carico di lavoro. Confrontare questo comportamento con [scalabilità orizzontale](virtual-machine-scale-sets-autoscale-overview.md), noto anche come *scalabilità* e *ridimensionare in*, in cui è stato modificato il numero di macchine virtuali a seconda del carico di lavoro.

Per nuovo provisioning si intende la rimozione di una VM esistente e la relativa sostituzione con una nuova. Quando si aumenta o diminuisce le dimensioni delle macchine virtuali in una scala di macchina virtuale impostato, in alcuni casi in cui che si desidera ridimensiona una macchina virtuale esistente e mantenere i dati, mentre in altri casi è necessario distribuire nuove macchine virtuali della nuova dimensione. Questo documento illustra entrambi i casi.

Il ridimensionamento verticale, ovvero l'aumento o la riduzione delle prestazioni, può risultare utile quando:

* Un servizio basato su macchine virtuali è sottoutilizzato, ad esempio nel fine settimana. La riduzione delle dimensioni delle VM può ridurre i costi mensili.
* L'aumento delle dimensioni delle VM consente di soddisfare una maggiore richiesta senza creare altre macchine virtuali.

È possibile impostare la scalabilità verticale deve per essere attivata in base alle metrici avvisi basati su dagli elementi del set di scalabilità della macchina virtuale. Quando viene attivato l'avviso, viene generato un webhook che attiva un runbook che è possibile ridimensionare la scala è impostato su o giù. Il ridimensionamento verticale può essere configurato seguendo questa procedura:

1. Creare un account di Automazione di Azure con funzionalità RunAs.
2. Importare runbook scalabilità verticale di automazione di Azure per il set di scalabilità di macchine virtuali nella sottoscrizione.
3. Aggiungere un webhook al runbook.
4. Aggiungere un avviso per la scalabilità della macchina virtuale impostata utilizzando una notifica webhook.

> [!NOTE]
> Il ridimensionamento verticale può avvenire solo entro determinati intervalli di dimensioni delle VM. Confrontare le specifiche di ogni dimensione prima di decidere il tipo di ridimensionamento (un numero più alto non sempre indica dimensioni della VM più grandi). È possibile scegliere di applicare il ridimensionamento tra le seguenti coppie di dimensioni:
> 
> | coppie di ridimensionamento di dimensioni delle macchine virtuali |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Creare un account di Automazione di Azure con funzionalità RunAs
La prima cosa che è necessario eseguire è creare un account di automazione di Azure che ospita i runbook consentono di distribuire le istanze di set di scalabilità macchina virtuale. Recentemente [automazione di Azure](https://azure.microsoft.com/services/automation/) è stata introdotta la funzionalità "Esegui come account" che consente l'impostazione di entità servizio per eseguire automaticamente i runbook per conto dell'utente. Per altre informazioni, vedere:

* [Autenticare runbook con account RunAs di Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importare i runbook di ridimensionamento verticale di Automazione di Azure nella sottoscrizione
I runbook necessari per scalare verticalmente il set di scalabilità della macchina virtuale sono già pubblicati nella raccolta di Runbook di automazione di Azure. Per importarli nella sottoscrizione seguire la procedura descritta in questo articolo:

* [Raccolte di runbook e moduli per l'automazione di Azure](../automation/automation-runbook-gallery.md)

Scegliere l'opzione Esplora raccolta dal menu Runbook:

![Runbook da importare][runbooks]

I runbook da importare sono visualizzati nell'immagine seguente: Selezionare il runbook in base al tipo di ridimensionamento con o senza un nuovo provisioning che si vuole usare:

![Raccolta di runbook][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Aggiungere un webhook al runbook
Dopo aver importato i runbook, aggiungere un webhook al runbook in modo che può essere attivata da un avviso generato da un set di scalabilità della macchina virtuale. Informazioni dettagliate sulla creazione di un webhook per il runbook sono disponibili in questo articolo:

* [Webhook di Automazione di Azure](../automation/automation-webhooks.md)

> [!NOTE]
> Assicurarsi di copiare il webhook URI prima di chiudere la finestra di dialogo webhook quanto è necessario questo indirizzo, nella sezione successiva.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Aggiungere un avviso per il set di scalabilità della macchina virtuale
Di seguito viene impostato uno script di PowerShell che illustra come aggiungere un avviso per un scalabilità della macchina virtuale. Vedere l'articolo seguente per ottenere il nome della metrica in base alla quale attivare l'avviso: [Azure Monitor autoscaling common metrics](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md) (Metriche comuni per il ridimensionamento automatico di Monitoraggio di Azure).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> È consigliabile configurare un intervallo di tempo ragionevole per l'avviso per evitare di attivare troppo spesso il ridimensionamento verticale e l'eventuale interruzione del servizio associato. Considerare un intervallo minimo di 20-30 minuti. Prendere in considerazione il ridimensionamento orizzontale se è necessario evitare qualsiasi interruzione.
> 
> 

Per ulteriori informazioni su come creare gli avvisi, vedere gli articoli seguenti:

* [Esempi di avvio rapido di Azure PowerShell di monitoraggio](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Esempi di avvio rapido di Azure CLI multipiattaforma di monitoraggio](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Summary
Questo articolo ha illustrato semplici esempi di ridimensionamento verticale. Con questi blocchi predefiniti, ovvero account di automazione, runbook, webhook e avvisi, è possibile connettere una vasta gamma di eventi con un set di azioni personalizzato.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
