---
title: Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure
description: Questo articolo illustra come inviare lo stato e i flussi del processo del runbook ai log di Monitoraggio di Azure.
services: automation
ms.subservice: process-automation
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 6dcd2005971927de30ca96173cb2bdb063e46663
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397432"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure

Automazione di Azure può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics. Questo processo non comporta il collegamento dell'area di lavoro ed è completamente indipendente. I log e i flussi di processo sono visibili nel portale di Azure o con PowerShell per i singoli processi e ciò consente di eseguire analisi semplici. Con i log di Monitoraggio di Azure ora è possibile:

* Ottenere informazioni dettagliate sullo stato dei processi di Automazione.
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso.
* Scrivere query avanzate nei flussi del processo.
* Correlare i processi tra account di Automazione.
* Usare visualizzazioni personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato dei processi del runbook e altri indicatori chiave o metriche correlati.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare a inviare i log di Automazione ai log di Monitoraggio di Azure sono necessari gli elementi seguenti:

* La versione più recente di [Azure PowerShell](/powershell/azure/).

* Un'area di lavoro Log Analytics e l'ID di risorsa. Per altre informazioni, vedere [Introduzione ai log di Monitoraggio di Azure](../azure-monitor/overview.md).

* ID risorsa dell'account di automazione di Azure.

## <a name="how-to-find-resource-ids"></a>Come trovare gli ID risorsa

1. Usare il comando seguente per trovare l'ID risorsa per l'account di Automazione di Azure:

    ```powershell-interactive
    # Find the ResourceId for the Automation account
    Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
    ```

2. Copiare il valore per **resourceId**.

3. Usare il comando seguente per trovare l'ID risorsa dell'area di lavoro Log Analytics:

    ```powershell-interactive
    # Find the ResourceId for the Log Analytics workspace
    Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
    ```

4. Copiare il valore per **resourceId**.

Per restituire i risultati di un gruppo di risorse specifico, includere il `-ResourceGroupName` parametro. Per ulteriori informazioni, vedere [Get-AzResource](/powershell/module/az.resources/get-azresource).

Se sono disponibili più account di Automazione o aree di lavoro nell'output dei comandi precedenti, è possibile trovare il nome e altre proprietà correlate che fanno parte dell'ID di risorsa completo dell'account di Automazione eseguendo queste operazioni:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel portale di Azure selezionare l'account di Automazione nella pagina **Account di automazione**.
1. Nella pagina dell'account di Automazione selezionato selezionare **Proprietà** in **Impostazioni account**.
1. Nella pagina **Proprietà**  notare i dettagli riportati di seguito.

    ![Proprietà dell'account di Automazione](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="configure-diagnostic-settings"></a>Configurare le impostazioni di diagnostica

Le impostazioni di diagnostica di automazione supportano l'invio dei log di piattaforma e dei dati delle metriche seguenti:

* JobLogs
* JobStreams
* DSCNodeStatus
* Metriche-Totale processi, totale esecuzioni computer distribuzione aggiornamenti, totale esecuzioni di aggiornamenti distribuzione

Per iniziare a inviare i log di automazione ai log di monitoraggio di Azure, vedere [creare le impostazioni di diagnostica](../azure-monitor/platform/diagnostic-settings.md) per comprendere la funzionalità e i metodi disponibili per configurare le impostazioni di diagnostica per inviare i log di piattaforma.

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

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Visualizzare i log di Automazione nei log di Monitoraggio di Azure

Ora che si è iniziato a inviare i flussi del processo di automazione e i log ai log di monitoraggio di Azure, è possibile vedere cosa è possibile fare con questi log all'interno dei log di monitoraggio di Azure.

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

Quando si esegue il debug di un processo, è consigliabile esaminarne anche i flussi. La query seguente mostra tutti i flussi per un singolo processo con GUID `2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0` :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0"
| sort by TimeGenerated asc
| project ResultDescription
```

### <a name="view-historical-job-status"></a>Visualizzare lo stato cronologico del processo

È infine consigliabile visualizzare la cronologia dei processi nel tempo. È possibile usare questa query per cercare lo stato dei processi nel tempo.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started"
| summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)
```

![Grafico dello stato cronologico del processo di Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)

### <a name="filter-job-status-output-converted-into-a-json-object"></a>Filtrare l'output dello stato del processo convertito in un oggetto JSON

Recentemente è stato modificato il comportamento di scrittura dei dati del log di automazione `AzureDiagnostics` nella tabella nel servizio log Analytics, in cui non suddivide più le proprietà JSON in campi distinti. Se il Runbook è stato configurato per formattare gli oggetti nel flusso di output in formato JSON come colonne separate, è necessario riconfigurare le query per analizzare tale campo in un oggetto JSON per accedere a tali proprietà. Questa operazione viene eseguita usando [parseJSON](../azure-monitor/log-query/json-data-structures.md#parsejson) per accedere a un elemento JSON specifico in un percorso noto.

Ad esempio, un Runbook formatta la proprietà *ResultDescription* nel flusso di output in formato JSON con più campi. Per cercare lo stato dei processi che si trovano in uno stato di errore, come specificato in un campo denominato **stato**, usare questa query di esempio per eseguire una ricerca nel *ResultDescription* con lo stato **non riuscito**:

```kusto
AzureDiagnostics
| where Category == 'JobStreams'
| extend jsonResourceDescription = parse_json(ResultDescription)
| where jsonResourceDescription.Status == 'Failed'
```

![Log Analytics formato JSON del flusso di processi cronologici](media/automation-manage-send-joblogs-log-analytics/job-status-format-json.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare query di ricerca e rivedere i log dei processi di Automazione con i log di Monitoraggio di Azure, vedere [Ricerche nei log di Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).
* Per informazioni su come creare e recuperare messaggi di errore e di output dai runbook, vedere [Monitorare l'output dei runbook](automation-runbook-output-and-messages.md).
* Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md).
* Per altre informazioni sui log di Monitoraggio di Azure e sulle origini di raccolta dati, vedere [Panoramica della raccolta di dati di archiviazione di Azure nei log di Monitoraggio di Azure](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).
* Per informazioni sulla risoluzione dei problemi di Log Analytics, vedere [Risoluzione dei problemi se Log Analytics non sta più raccogliendo dati](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
