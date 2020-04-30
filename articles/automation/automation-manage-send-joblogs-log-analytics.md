---
title: Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure
description: Questo articolo illustra come inviare lo stato del processo e i flussi del processo Runbook ai log di monitoraggio di Azure per offrire informazioni e gestione aggiuntive.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457689"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Inviare lo stato e i flussi del processo dall'automazione ai log di monitoraggio di Azure

Automazione può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics. Questo processo non comporta il collegamento dell'area di lavoro ed è completamente indipendente. I log e i flussi di processo sono visibili nel portale di Azure o con PowerShell per i singoli processi e ciò consente di eseguire analisi semplici. Ora con i log di monitoraggio di Azure è possibile:

* Ottenere informazioni dettagliate sullo stato dei processi di automazione.
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso.
* Scrivere query avanzate nei flussi del processo.
* Correlare i processi tra account di Automazione.
* Usare visualizzazioni personalizzate e query di ricerca per visualizzare i risultati di Runbook, lo stato del processo Runbook e altri indicatori chiave o metriche correlati.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="prerequisites-and-deployment-considerations"></a>Prerequisiti e considerazioni sulla distribuzione

Per iniziare a inviare i log di automazione ai log di monitoraggio di Azure, è necessario:

* Versione più recente di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Un'area di lavoro Log Analytics. Per altre informazioni, vedere [Introduzione ai log di monitoraggio di Azure](../log-analytics/log-analytics-get-started.md).
* ID risorsa per l'account di automazione di Azure.

Usare il comando seguente per trovare l'ID risorsa per l'account di automazione di Azure:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Per trovare l'ID risorsa per l'area di lavoro Log Analytics, eseguire il comando PowerShell seguente:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se si dispone di più di un account di automazione o di un'area di lavoro nell'output dei comandi precedenti, trovare il nome che è necessario configurare e copiare il valore per l'ID risorsa.

1. Nella portale di Azure selezionare l'account di automazione nel pannello **account di automazione** e selezionare **tutte le impostazioni**. 
2. Nel pannello **tutte le impostazioni** selezionare **proprietà**in **Impostazioni account**.  
3. Nel pannello **Proprietà** prendere nota delle proprietà illustrate di seguito.

    ![Proprietà dell'account di automazione](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Record di log di Monitoraggio di Azure

Diagnostica di automazione di Azure crea due tipi di record nei log di monitoraggio di `AzureDiagnostics`Azure, contrassegnati come. Le tabelle nelle sezioni successive sono esempi di record generati da automazione di Azure e i tipi di dati visualizzati nei risultati della ricerca nei log.

### <a name="job-logs"></a>Log di processo

| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chiamante che ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| Tenant_g | GUID che identifica il tenant per il chiamante. |
| JobId_g |GUID che identifica il processo Runbook. |
| ResultType |Lo stato del processo di runbook. I valori possibili sono:<br>- Nuovo<br>-Creato<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato |
| Category | La classificazione del tipo di dati. Per Automazione, il valore è JobLogs. |
| OperationName | Tipo di operazione eseguita in Azure. Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di automazione |
| SourceSystem | Sistema usato dai log di monitoraggio di Azure per raccogliere i dati. Il valore è sempre Azure per diagnostica di Azure. |
| ResultDescription |Stato del risultato del processo Runbook. I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato |
| CorrelationId |GUID di correlazione del processo Runbook. |
| ResourceId |ID risorsa dell'account di automazione di Azure di Runbook. |
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
| JobId_g |GUID che identifica il processo Runbook. |
| ResultType |Lo stato del processo di runbook. I valori possibili sono:<br>- In corso |
| Category | La classificazione del tipo di dati. Per Automazione, il valore è JobStreams. |
| OperationName | Tipo di operazione eseguita in Azure. Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di automazione. |
| SourceSystem | Sistema usato dai log di monitoraggio di Azure per raccogliere i dati. Il valore è sempre Azure per diagnostica di Azure. |
| ResultDescription |Descrizione che include il flusso di output di Runbook. |
| CorrelationId |GUID di correlazione del processo Runbook. |
| ResourceId |ID risorsa dell'account di automazione di Azure di Runbook. |
| SubscriptionId | GUID della sottoscrizione di Azure per l'account di automazione. |
| ResourceGroup | Nome del gruppo di risorse per l'account di automazione. |
| ResourceProvider | Provider di risorse. Il valore è MICROSOFT. Automazione. |
| ResourceType | Tipo di risorsa. Il valore è AUTOMATIONACCOUNTS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Configurazione dell'integrazione con i log di monitoraggio di Azure

1. Nel computer locale avviare Windows PowerShell dalla schermata **Start**.
2. Eseguire i comandi di PowerShell seguenti e modificare i valori per `[your resource ID]` e `[resource ID of the log analytics workspace]` con i valori della sezione precedente.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Dopo l'esecuzione di questo script, l'operazione può richiedere un'ora prima di iniziare a visualizzare i record nei log `JobLogs` di `JobStreams` monitoraggio di Azure di nuovi o scritti.

Per visualizzare i log, eseguire la query seguente in ricerca log di log Analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificare la configurazione

Per verificare che l'account di automazione invii i log all'area di lavoro Log Analytics, verificare che la diagnostica sia configurata correttamente nell'account di automazione usando il comando di PowerShell seguente.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Nell'output verificare che:

* In `Logs`il valore per `Enabled` è true.
* `WorkspaceId`viene impostato sul `ResourceId` valore per l'area di lavoro log Analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visualizzazione dei log di automazione nei log di monitoraggio di Azure

Ora che si è iniziato a inviare i log del processo di automazione ai log di monitoraggio di Azure, è possibile vedere cosa è possibile fare con questi log all'interno dei log di monitoraggio di Azure.

Per visualizzare i log eseguire questa query: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Inviare un messaggio di posta elettronica quando un processo del runbook non riesce o viene sospeso

Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con un processo del runbook.

Per creare una regola di avviso, iniziare creando una ricerca log per i record del processo Runbook che devono richiamare l'avviso. Fare clic su pulsante **Avviso** per creare e configurare la regola di avviso.

1. Nella pagina Panoramica dell'area di lavoro Log Analytics fare clic su **Visualizza log**.
2. Creare una query di ricerca log per l'avviso digitando la seguente ricerca nel campo della query:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>È anche possibile raggruppare in base al nome del Runbook usando:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione. Il nome dell'account di automazione si trova `Resource` nel campo nella ricerca di `JobLogs`.
3. Per aprire la schermata **Crea regola** , fare clic su **nuova regola di avviso** nella parte superiore della pagina. Per altre informazioni sulle opzioni per la configurazione dell'avviso, vedere [Avvisi di log in Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Trovare tutti i processi completati con errori

Oltre agli avvisi per gli errori, è possibile determinare quando un processo del runbook presenta un errore non irreversibile. In questi casi, PowerShell genera un flusso di errore, ma gli errori non fatali non provocano la sospensione o l'esito negativo del processo.

1. Nell'area di lavoro Log Analytics fare clic su **log**.
2. Nel campo della query digitare `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.
3. Fare clic sul pulsante **Cerca** .

### <a name="view-job-streams-for-a-job"></a>Visualizzare flussi del processo per un processo

Quando si esegue il debug di un processo, è anche possibile esaminare i flussi del processo. La query seguente illustra tutti i flussi per un singolo processo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Visualizzare lo stato cronologico del processo

Infine, potrebbe essere necessario visualizzare la cronologia dei processi nel tempo. È possibile usare questa query per cercare lo stato dei processi nel tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Grafico dello stato cronologico del processo di Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Rimozione delle impostazioni di diagnostica

Per rimuovere l'impostazione di diagnostica dall'account di automazione, eseguire il comando seguente:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione dei problemi Log Analytics, vedere [risoluzione dei problemi perché log Analytics non raccoglie più dati](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Per altre informazioni su come creare query di ricerca diverse ed esaminare i log dei processi di automazione con i log di monitoraggio di Azure, vedere [ricerche nei log nei log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md).
* Per informazioni su come creare e recuperare l'output e i messaggi di errore da manuali operativi, vedere [output e messaggi di Runbook](automation-runbook-output-and-messages.md).
* Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Verifica di un processo di runbook](automation-runbook-execution.md).
* Per altre informazioni sui log di monitoraggio di Azure e sulle origini di raccolta dati, vedere [raccolta di dati di archiviazione di Azure in log di monitoraggio di Azure](../azure-monitor/platform/collect-azure-metrics-logs.md).