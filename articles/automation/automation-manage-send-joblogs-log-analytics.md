---
title: Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure
description: Questo articolo illustra come inviare flussi di lavoro e lo stato dei processi dei processi dei processi ai log di Monitoraggio di Azure per fornire informazioni dettagliate e gestione aggiuntive.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457689"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Inoltrare lo stato dei processi e i flussi di lavoro dall'automazione ai log di Monitoraggio di AzureForward job status and job streams from Automation to Azure Monitor logs

Automazione può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics. Questo processo non comporta il collegamento dell'area di lavoro ed è completamente indipendente. I log e i flussi di processo sono visibili nel portale di Azure o con PowerShell per i singoli processi e ciò consente di eseguire analisi semplici. Ora con i log di Monitoraggio di Azure è possibile:Now with Azure Monitor logs you can:

* Ottieni informazioni dettagliate sullo stato dei tuoi lavori di automazione.
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso.
* Scrivere query avanzate nei flussi del processo.
* Correlare i processi tra account di Automazione.
* Usa visualizzazioni personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato del processo del runbook e altri indicatori o metriche chiave correlati.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="prerequisites-and-deployment-considerations"></a>Prerequisiti e considerazioni sulla distribuzione

Per iniziare a inviare i log di Automazione ai log di Monitoraggio di Azure, è necessario:To start sending your Automation logs to Azure Monitor logs, you need:

* L'ultima versione di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Un'area di lavoro Log Analytics. Per altre informazioni, vedere [Introduzione ai log](../log-analytics/log-analytics-get-started.md)di Monitoraggio di Azure.For more information, see Get started with Azure Monitor logs .
* ID risorsa per l'account di Automazione di Azure.The resource ID for your Azure Automation account.

Usare il comando seguente per trovare l'ID risorsa per l'account di Automazione di Azure:Use the following command to find the resource ID for your Azure Automation account:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Per trovare l'ID risorsa per l'area di lavoro di Log Analytics, eseguire il comando di PowerShell seguente:To find the resource ID for your Log Analytics workspace, run the following PowerShell command:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se nell'output dei comandi precedenti sono presente più account o area di lavoro di automazione, trovare il nome necessario per configurare e copiare il valore per l'ID risorsa.

1. Nel portale di Azure selezionare l'account di automazione nel pannello **Account di automazione** e selezionare **Tutte le impostazioni**. 
2. Nel pannello **Tutte le impostazioni,** in **Impostazioni account,** selezionare **Proprietà**.  
3. Nel pannello **Proprietà** prendere nota delle proprietà illustrate di seguito.

    ![Proprietà dell'account di automazione](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Record di log di Monitoraggio di Azure

Diagnostica di automazione di Azure creare due tipi `AzureDiagnostics`di record nei log di Monitoraggio di Azure, contrassegnati come . Le tabelle nelle sezioni successive sono esempi di record generati dall'automazione di Azure e i tipi di dati visualizzati nei risultati della ricerca nei log.

### <a name="job-logs"></a>Log di processo

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chiamante che ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| Tenant_g | GUID che identifica il tenant per il chiamante. |
| JobId_g |GUID che identifica il processo del runbook. |
| ResultType |Lo stato del processo di runbook. I valori possibili sono:<br>- Nuovo<br>- Creato<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato |
| Category | La classificazione del tipo di dati. Per Automazione, il valore è JobLogs. |
| OperationName | Tipo di operazione eseguita in Azure.The type of operation performed in Azure. Per Automazione, il valore è Job. |
| Risorsa | Il nome dell'account di automazione |
| SourceSystem | Sistema usato dai log di Monitoraggio di Azure per raccogliere i dati. Il valore è sempre La diagnostica di Azure per Azure.The value is always Azure for Azure diagnostics. |
| ResultDescription |Stato del risultato del processo del runbook. I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato |
| CorrelationId |GUID di correlazione del processo del runbook. |
| ResourceId |ID risorsa account di Automazione di Azure del runbook. |
| SubscriptionId | GUID della sottoscrizione di Azure per l'account di automazione. |
| ResourceGroup | Nome del gruppo di risorse per l'account di automazione. |
| ResourceProvider | Provider di risorse. Il valore è MICROSOFT. Automazione. |
| ResourceType | Tipo di risorsa. Il valore è AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Flussi di processo
| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chiamante che ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| StreamType_s |Il tipo di flusso del processo. I valori possibili sono:<br>- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato |
| Tenant_g | GUID che identifica il tenant per il chiamante. |
| JobId_g |GUID che identifica il processo del runbook. |
| ResultType |Lo stato del processo di runbook. I valori possibili sono:<br>- In corso |
| Category | La classificazione del tipo di dati. Per Automazione, il valore è JobStreams. |
| OperationName | Tipo di operazione eseguita in Azure.Type of operation performed in Azure. Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di automazione. |
| SourceSystem | Sistema usato dai log di Monitoraggio di Azure per raccogliere i dati. Il valore è sempre La diagnostica di Azure per Azure.The value is always Azure for Azure diagnostics. |
| ResultDescription |Descrizione che include il flusso di output dal runbook. |
| CorrelationId |GUID di correlazione del processo del runbook. |
| ResourceId |ID risorsa account di Automazione di Azure del runbook. |
| SubscriptionId | GUID della sottoscrizione di Azure per l'account di automazione. |
| ResourceGroup | Nome del gruppo di risorse per l'account di automazione. |
| ResourceProvider | Provider di risorse. Il valore è MICROSOFT. Automazione. |
| ResourceType | Tipo di risorsa. Il valore è AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Configurazione dell'integrazione con i log di Monitoraggio di AzureSetting up integration with Azure Monitor logs

1. Nel computer locale avviare Windows PowerShell dalla schermata **Start**.
2. Eseguire i comandi di PowerShell seguenti `[your resource ID]` `[resource ID of the log analytics workspace]` e modificare i valori per e con i valori della sezione precedente.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Dopo aver eseguito questo script, potrebbe essere necessario un'ora `JobLogs` prima `JobStreams` di iniziare a visualizzare i record nei log di Monitoraggio di Azure di nuovo o in fase di scrittura.

Per visualizzare i log, eseguire la query seguente nella ricerca del log di analisi dei log:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificare la configurazione

Per verificare che l'account di automazione invii log all'area di lavoro di Log Analytics, verificare che la diagnostica sia configurata correttamente nell'account di automazione usando il comando di PowerShell seguente.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Nell'output, assicurarsi che:

* In `Logs`, il `Enabled` valore per è True.
* `WorkspaceId`è impostato `ResourceId` sul valore dell'area di lavoro di Log Analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visualizzazione dei log di automazione nei log di Monitoraggio di AzureViewing Automation Logs in Azure Monitor logs

Dopo aver iniziato a inviare i log dei processi di automazione ai log di Monitoraggio di Azure, vedere cosa è possibile fare con questi log all'interno dei log di Monitoraggio di Azure.Now that you started sending your Automation job logs to Azure Monitor logs, let's see what you can do with these logs inside Azure Monitor logs.

Per visualizzare i log eseguire questa query: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Inviare un messaggio di posta elettronica quando un processo del runbook non riesce o viene sospeso

Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con un processo del runbook.

Per creare una regola di avviso, iniziare creando una ricerca log per i record di processo del runbook che devono richiamare l'avviso. Fare clic su pulsante **Avviso** per creare e configurare la regola di avviso.

1. Nella pagina Panoramica dell'area di lavoro di Log Analytics fare clic su **Visualizza log**.
2. Creare una query di ricerca log per l'avviso digitando la ricerca seguente nel campo della query:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Puoi anche raggruppare in base al nome del runbook usando:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione. Il nome dell'account `Resource` di automazione `JobLogs`si trova nel campo nella ricerca di .
3. Per aprire la schermata **Crea regola,** fare clic su **Nuova regola** di avviso nella parte superiore della pagina. Per altre informazioni sulle opzioni per la configurazione dell'avviso, vedere [Avvisi di log in Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Trovare tutti i processi completati con errori

Oltre agli avvisi per gli errori, è possibile determinare quando un processo del runbook presenta un errore non irreversibile. In questi casi, PowerShell genera un flusso di errore, ma gli errori non di terminazione non causano la sospensione o l'esito negativo del processo.

1. Nell'area di lavoro di Log Analytics fare clic su **Registri**.
2. Nel campo della `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`query digitare .
3. Fare clic sul pulsante **Cerca.**

### <a name="view-job-streams-for-a-job"></a>Visualizzare flussi del processo per un processo

Quando si esegue il debug di un processo, è anche possibile esaminare i flussi di lavoro. La query seguente illustra tutti i flussi per un singolo processo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visualizzare lo stato cronologico del processo

Infine, è possibile visualizzare la cronologia dei processi nel tempo. È possibile usare questa query per cercare lo stato dei processi nel tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Grafico dello stato cronologico del processo di Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Rimozione delle impostazioni di diagnostica

Per rimuovere l'impostazione di diagnostica dall'account di automazione, eseguire il comando seguente:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione dei problemi relativi a Log Analytics, vedere [Risoluzione dei problemi relativi alla risoluzione](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)dei dati per cui Log Analytics non raccoglie più dati.
* Per altre informazioni su come creare query di ricerca diverse ed esaminare i log dei processi di automazione con i log di Monitoraggio di Azure, vedere Ricerche nei log nei log di Monitoraggio di Azure.To learn more about how to construct different search queries and review the Automation job logs with Azure Monitor logs, see [Log searches in Azure Monitor logs.](../log-analytics/log-analytics-log-searches.md)
* Per informazioni su come creare e recuperare messaggi di output e di errore dai runbook, vedere Output e messaggi del [Runbook.](automation-runbook-output-and-messages.md)
* Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Verifica di un processo di runbook](automation-runbook-execution.md).
* Per altre informazioni sui log di Monitoraggio di Azure e sulle origini della raccolta dati, vedere Panoramica della raccolta di dati di archiviazione di Azure in Panoramica dei log di Monitoraggio di Azure.To learn more about Azure Monitor logs and data collection sources, see [Collecting Azure storage data in Azure Monitor logs overview.](../azure-monitor/platform/collect-azure-metrics-logs.md)