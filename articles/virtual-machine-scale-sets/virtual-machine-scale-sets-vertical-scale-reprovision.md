<properties
	pageTitle="Ridimensionare verticalmente set di scalabilità di macchine virtuali di Azure | Microsoft Azure"
	description="Come eseguire la scalabilità verticale di una macchina virtuale in risposta agli avvisi di monitoraggio tramite Automazione di Azure"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="madhana"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2016"
	ms.author="guybo"/>

# Ridimensionamento automatico verticale con set di scalabilità di macchine virtuali

Questo articolo descrive come ridimensionare verticalmente i [set di macchine virtuali ](https://azure.microsoft.com/services/virtual-machine-scale-sets/) di Azure con o senza un nuovo provisioning Per il ridimensionamento verticale delle macchine virtuali non incluse nel set di scalabilità, vedere l'articolo relativo al [ridimensionamento verticale di macchine virtuali di Azure con Automazione di Azure](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md).

Per ridimensionamento verticale, detto anche _aumento delle prestazioni_ e _riduzione delle prestazioni_, si intende l'aumento o la riduzione delle dimensioni delle macchine virtuali (VM) in risposta a un carico di lavoro. Confrontare questo concetto con il [ridimensionamento orizzontale](./virtual-machine-scale-sets-autoscale-overview.md), detto anche _aumento del numero di istanze_ e _riduzione del numero di istanze_, in cui il numero di VM viene modificato in base al carico di lavoro.

Per nuovo provisioning si intende la rimozione di una VM esistente e la relativa sostituzione con una nuova. Quando si aumentano o riducono le dimensioni delle macchine virtuali in un set di scalabilità di macchine virtuali, in alcuni casi può rendersi necessario ridimensionare le VM esistenti e mantenere i dati, mentre in altri casi è necessario distribuire nuove VM con le nuove dimensioni. Questo documento illustra entrambi i casi.

Il ridimensionamento verticale, ovvero l'aumento o la riduzione delle prestazioni, può risultare utile quando:

- Un servizio basato su macchine virtuali è sottoutilizzato, ad esempio nel fine settimana. La riduzione delle dimensioni delle VM può ridurre i costi mensili.
- L'aumento delle dimensioni delle VM consente di soddisfare una maggiore richiesta senza creare altre macchine virtuali.

È possibile configurare l'attivazione del ridimensionamento verticale secondo le metriche basate sugli avvisi del set di scalabilità di macchine virtuali. Quando viene attivato l'avviso, genera un webhook che attiva un runbook in grado di aumentare o ridurre le prestazioni del set di scalabilità. Il ridimensionamento verticale può essere configurato seguendo questa procedura:

1. Creare un account di Automazione di Azure con funzionalità RunAs.
2. Importare i runbook di scalabilità verticale di Automazione di Azure per i set di scalabilità di macchine virtuali nella sottoscrizione.
3. Aggiungere un webhook al runbook.
4. Aggiungere un avviso per il set di scalabilità di macchine virtuali con una notifica di un webhook.

> [AZURE.NOTE] Il ridimensionamento verticale può avvenire solo entro determinati intervalli di dimensioni delle VM. È possibile scegliere di applicare il ridimensionamento tra le seguenti coppie di dimensioni:

>| coppie di ridimensionamento di dimensioni delle macchine virtuali | |
|---|---|
| Basic\_A0 | Basic\_A4 |
| Standard\_A0 | Standard\_A4 |
| Standard\_A5 | Standard\_A7 |
| Standard\_A8 | Standard\_A9 |
| Standard\_A10 | Standard\_A11 |
| Standard\_D1 | Standard\_D4 |
| Standard\_D11 | Standard\_D14 |
| Standard\_DS1 | Standard\_DS4 |
| Standard\_DS11 | Standard\_DS14 |
| Standard\_D1v2 | Standard\_D5v2 |
| Standard\_D11v2 | Standard\_D14v2 |
| Standard\_G1 | Standard\_G5 |
| Standard\_GS1 | Standard\_GS5 |

## Creare un account di Automazione di Azure con funzionalità RunAs

La prima operazione da eseguire è creare l'account di Automazione di Azure che ospiterà i runbook usati per ridimensionare le istanze del set di scalabilità di macchine virtuali. [Automazione di Azure](https://azure.microsoft.com/services/automation/) ha introdotto di recente la funzionalità "Account RunAs", che semplifica molto la configurazione dell'entità servizio per l'esecuzione automatica di runbook per conto dell'utente. Altre informazioni sono disponibili nell'articolo seguente.

* [Autenticare runbook con account RunAs di Azure](../automation/automation-sec-configure-azure-runas-account.md)

## Importare i runbook di ridimensionamento verticale di Automazione di Azure nella sottoscrizione

I runbook necessari per il ridimensionamento verticale del set di scalabilità di macchine virtuali sono già stati pubblicati nella raccolta dei runbook di Automazione di Azure. Per importarli nella sottoscrizione seguire la procedura descritta in questo articolo:

* [Raccolte di runbook e moduli per l'automazione di Azure](../automation/automation-runbook-gallery.md)

Scegliere l'opzione Esplora raccolta dal menu Runbook:

![Runbook da importare][runbooks]

I runbook da importare sono visualizzati nell'immagine seguente: Selezionare il runbook in base al tipo di ridimensionamento con o senza un nuovo provisioning che si vuole usare:

![Raccolta di runbook][gallery]

## Aggiungere un webhook al runbook

Dopo avere importato i runbook, è necessario aggiungere un webhook al runbook in modo che possa essere attivato da un set di scalabilità di macchine virtuali. Informazioni dettagliate sulla creazione di un webhook per il runbook sono disponibili in questo articolo:

* [Webhook di Automazione di Azure](../automation/automation-webhooks.md)

> [AZURE.NOTE] Assicurarsi di copiare l'URI del webhook prima di chiudere la finestra di dialogo del webhook, perché sarà necessario nella sezione successiva.

## Aggiungere un avviso al set di scalabilità di macchine virtuali

Di seguito è riportato uno script di PowerShell che mostra come aggiungere un avviso a un set di scalabilità di macchine virtuali. Vedere l'articolo seguente per ottenere il nome della metrica in base alla quale attivare l'avviso: [Metriche comuni per la scalabilità automatica di Azure Insights](../azure-portal/insights-autoscale-common-metrics.md).

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

> [AZURE.NOTE] È consigliabile configurare un intervallo di tempo ragionevole per l'avviso per evitare di attivare troppo spesso il ridimensionamento verticale e l'eventuale interruzione del servizio associato. Considerare un intervallo minimo di 20-30 minuti. Prendere in considerazione il ridimensionamento orizzontale se è necessario evitare qualsiasi interruzione.

Per altre informazioni su come creare gli avvisi, vedere gli articoli seguenti:

* [Esempi di avvio rapido di PowerShell in Azure Insights](../azure-portal/insights-powershell-samples.md)
* [Esempi di avvio rapido dell'interfaccia della riga di comando multipiattaforma di Azure Insights](../azure-portal/insights-cli-samples.md)

## Riepilogo

Questo articolo ha illustrato semplici esempi di ridimensionamento verticale. Con questi blocchi predefiniti, ovvero account di automazione, runbook, webhook e avvisi, è possibile connettere una vasta gamma di eventi con un set di azioni personalizzato.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png

<!---HONumber=AcomDC_0420_2016-->