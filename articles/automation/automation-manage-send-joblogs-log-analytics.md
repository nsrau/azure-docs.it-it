---
title: Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure
description: Questo articolo illustra come inviare lo stato e i flussi del processo del runbook ai log di Monitoraggio di Azure.
services: automation
ms.subservice: process-automation
ms.date: 05/22/2020
ms.topic: conceptual
ms.openlocfilehash: ba498fe9f70664a801172a6ff3705ac41a6371ef
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835249"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure

Automazione di Azure può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics. Questo processo non comporta il collegamento dell'area di lavoro ed è completamente indipendente. I log e i flussi di processo sono visibili nel portale di Azure o con PowerShell per i singoli processi e ciò consente di eseguire analisi semplici. Con i log di Monitoraggio di Azure ora è possibile:

* Ottenere informazioni dettagliate sullo stato dei processi di Automazione.
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso.
* Scrivere query avanzate nei flussi del processo.
* Correlare i processi tra account di Automazione.
* Usare visualizzazioni personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato dei processi del runbook e altri indicatori chiave o metriche correlati.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Prerequisiti e considerazioni sulla distribuzione

Per iniziare a inviare i log di Automazione ai log di Monitoraggio di Azure sono necessari gli elementi seguenti:

* La versione più recente di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Un'area di lavoro Log Analytics. Per altre informazioni, vedere [Introduzione ai log di Monitoraggio di Azure](../log-analytics/log-analytics-get-started.md).
* L'ID risorsa dell'account di Automazione di Azure.

Usare il comando seguente per trovare l'ID risorsa per l'account di Automazione di Azure:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Per trovare l'ID risorsa per l'area di lavoro Log Analytics, eseguire questo comando PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se sono disponibili più account di Automazione o aree di lavoro nell'output dei comandi precedenti, è possibile trovare il nome e altre proprietà correlate che fanno parte dell'ID di risorsa completo dell'account di Automazione eseguendo queste operazioni:

1. Nel portale di Azure selezionare l'account di Automazione nella pagina **Account di automazione**. 
2. Nella pagina dell'account di Automazione selezionato selezionare **Proprietà** in **Impostazioni account**.  
3. Nella pagina **Proprietà**  notare i dettagli riportati di seguito.

    ![Proprietà dell'account di Automazione](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Record di log di Monitoraggio di Azure

La diagnostica di Automazione di Azure crea due tipi di record nei log di Monitoraggio di Azure, contrassegnati come `AzureDiagnostics`. Le tabelle nelle sezioni successive sono esempi di record generati da Automazione di Azure e dei tipi di dati visualizzati nei risultati della ricerca log.

### <a name="job-logs"></a>Log di processo

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chiamante che ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| JobId_g |GUID che identifica il processo del runbook. |
| ResultType |Lo stato del processo di runbook. I valori possibili sono:<br>- Nuovo<br>- Creato<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato |
| Category | La classificazione del tipo di dati. Per Automazione, il valore è JobLogs. |
| OperationName | Tipo di operazione eseguita in Azure. Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di Automazione. |
| SourceSystem | Sistema usato dai log di Monitoraggio di Azure per raccogliere i dati. Il valore è sempre Azure per Diagnostica di Azure. |
| ResultDescription |Stato del risultato del processo del runbook. I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato |
| CorrelationId |GUID di correlazione del processo del runbook. |
| ResourceId |Specifica l'ID risorsa dell'account di Automazione di Azure del runbook. |
| SubscriptionId | GUID della sottoscrizione di Azure per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse per l'account di Automazione. |
| ResourceProvider | Provider di risorse. Il valore è MICROSOFT.AUTOMATION. |
| ResourceType | Tipo di risorsa. Il valore è AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Flussi di processo
| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chiamante che ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| StreamType_s |Il tipo di flusso del processo. I valori possibili sono:<br>\- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| JobId_g |GUID che identifica il processo del runbook. |
| ResultType |Lo stato del processo di runbook. I valori possibili sono:<br>- In corso |
| Category | La classificazione del tipo di dati. Per Automazione, il valore è JobStreams. |
| OperationName | Tipo di operazione eseguita in Azure. Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di Automazione. |
| SourceSystem | Sistema usato dai log di Monitoraggio di Azure per raccogliere i dati. Il valore è sempre Azure per Diagnostica di Azure. |
| ResultDescription |Descrizione che include il flusso di output dal runbook. |
| CorrelationId |GUID di correlazione del processo del runbook. |
| ResourceId |Specifica l'ID risorsa dell'account di Automazione di Azure del runbook. |
| SubscriptionId | GUID della sottoscrizione di Azure per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse per l'account di Automazione. |
| ResourceProvider | Provider di risorse. Il valore è MICROSOFT.AUTOMATION. |
| ResourceType | Tipo di risorsa. Il valore è AUTOMATIONACCOUNTS. |

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurare l'integrazione con i log di Monitoraggio di Azure

1. Nel computer locale avviare Windows PowerShell dalla schermata **Start**.
2. Eseguire questi comandi di PowerShell e sostituire il valore di `$automationAccountId` e `$workspaceId` con i valori della sezione precedente.

   ```powershell-interactive
   $workspaceId = "resource ID of the log analytics workspace"
   $automationAccountId = "resource ID of your Automation account"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Dopo aver eseguito questo script, potrebbe essere necessaria un'ora per iniziare a visualizzare i record nei log di Monitoraggio di Azure per i nuovi elementi `JobLogs` o `JobStreams` in fase di scrittura.

Per visualizzare i log, eseguire questa query nella ricerca log di Log Analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificare la configurazione

Per verificare che l'account di Automazione invii i log all'area di lavoro Log Analytics, accertarsi che la diagnostica sia configurata correttamente nell'account di Automazione usando il comando di PowerShell seguente.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Nell'output verificare quanto segue:

* In `Logs` il valore per `Enabled` è True.
* `WorkspaceId` è impostato sul valore `ResourceId` per l'area di lavoro Log Analytics.

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Visualizzare i log di Automazione nei log di Monitoraggio di Azure

Dopo avere avviato l'invio di log di processo di Automazione ai log di Automazione di Azure, si vedrà quali operazioni è possibile eseguire con questi log nei log di Monitoraggio di Azure.

Per visualizzare i log eseguire questa query: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Inviare un messaggio di posta elettronica quando un processo del runbook non riesce o viene sospeso

I passaggi seguenti illustrano come configurare gli avvisi in Monitoraggio di Azure per ricevere una notifica quando si verificano problemi con un processo del runbook.

Per creare una regola di avviso è necessario creare prima di tutto una ricerca log per trovare i record del processo del runbook che dovranno richiamare l'avviso. Fare clic su pulsante **Avviso** per creare e configurare la regola di avviso.

1. Nella pagina Panoramica dell'area di lavoro Log Analytics fare clic su **Visualizza i log**.

2. Creare una query di ricerca log per l'avviso digitando i criteri di ricerca seguenti nel campo query: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>È anche possibile raggruppare per nome runbook usando: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione. Si può trovare il nome dell'account di Automazione nel campo `Resource` nella ricerca di `JobLogs`.

3. Per aprire la schermata **Creazione regola** fare clic su **Nuova regola di avviso** nella parte superiore della pagina. Per altre informazioni sulle opzioni per la configurazione dell'avviso, vedere [Avvisi di log in Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Trovare tutti i processi completati con errori

Oltre agli avvisi per gli errori, è possibile determinare quando un processo del runbook presenta un errore non irreversibile. In questi casi PowerShell produce un flusso di errore, ma gli errori non irreversibili non comportano la sospensione o l'esito negativo del processo.

1. Nell'area di lavoro Log Analytics fare clic su **Log**.

2. Nel campo della query digitare `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.

3. Fare clic sul pulsante **Cerca**.

### <a name="view-job-streams-for-a-job"></a>Visualizzare flussi del processo per un processo

Quando si esegue il debug di un processo, è consigliabile esaminarne anche i flussi. La query seguente illustra tutti i flussi per un singolo processo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visualizzare lo stato cronologico del processo

È infine consigliabile visualizzare la cronologia dei processi nel tempo. È possibile usare questa query per cercare lo stato dei processi nel tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Grafico dello stato cronologico del processo di Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Rimuovere le impostazioni di diagnostica

Per rimuovere l'impostazione di diagnostica dall'account di Automazione, eseguire questo comando:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare query di ricerca e rivedere i log dei processi di Automazione con i log di Monitoraggio di Azure, vedere [Ricerche nei log di Monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md).
* Per informazioni su come creare e recuperare messaggi di errore e di output dai runbook, vedere [Monitorare l'output dei runbook](automation-runbook-output-and-messages.md).
* Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md).
* Per altre informazioni sui log di Monitoraggio di Azure e sulle origini di raccolta dati, vedere [Panoramica della raccolta di dati di archiviazione di Azure nei log di Monitoraggio di Azure](../azure-monitor/platform/collect-azure-metrics-logs.md).
* Per informazioni sulla risoluzione dei problemi di Log Analytics, vedere [Risoluzione dei problemi se Log Analytics non sta più raccogliendo dati](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).