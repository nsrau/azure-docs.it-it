---
title: Inoltrare i dati dei processi di Automazione di Azure a OMS Log Analytics | Documentazione Microsoft
description: "Questo articolo illustra come inviare lo stato e i flussi del processo del runbook a Log Analytics di Microsoft Operations Management Suite per fornire informazioni e funzionalità di gestione aggiuntive."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 2c25403a4bb2acd81061cc5dfdfd460c48a244bc
ms.lasthandoff: 04/27/2017


---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Inoltrare lo stato e i flussi del processo da Automazione a Log Analytics (OMS)
Automazione può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics di Microsoft Operations Management Suite (OMS).  I log e i flussi di processo sono visibili nel portale di Azure o con PowerShell per i singoli processi e ciò consente di eseguire analisi semplici. Con Log Analytics è ora possibile:

* Ottenere informazioni dettagliate sui processi di Automazione.
* Attivare un messaggio e-mail o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso.
* Scrivere query avanzate nei flussi del processo.
* Correlare i processi tra account di Automazione.
* Visualizzare la cronologia dei processi nel tempo.     

## <a name="prerequisites-and-deployment-considerations"></a>Prerequisiti e considerazioni sulla distribuzione
Per iniziare a inviare i log di Automazione a Log Analytics, sono necessari gli elementi seguenti:

1. La versione di novembre 2016 o versioni successive di [Azure PowerShell](/powershell/azure/overview) (v 2.3.0).
2. Un'area di lavoro di Log Analytics. Per altre informazioni, vedere [Introduzione a Log Analytics](../log-analytics/log-analytics-get-started.md).
3. Il valore ResourceId dell'account di Automazione di Azure.

Per trovare il valore ResourceId dell'account di Automazione di Azure e l'area di lavoro di Log Analytics eseguire questo comando PowerShell:

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se si hanno più account di automazione o aree di lavoro, nell'output dei comandi precedenti trovare il *nome* necessario per configurare e copiare il valore di *ResourceId*.

Per trovare il *nome* dell'account di Automazione, nel portale di Azure selezionare l'account di Automazione dal pannello **Account di Automazione** e selezionare **Tutte le impostazioni**.  Nel pannello **Tutte le impostazioni** selezionare **Proprietà** in **Impostazioni account**.  Nel pannello **Proprietà** è possibile prendere nota di questi valori.<br> ![Proprietà dell'account di Automazione](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Configurare l'integrazione con Log Analytics
1. Nel computer locale avviare **Windows PowerShell** dalla schermata **Start**.  
2. Copiare e incollare il comando PowerShell seguente e modificare il valore per `$workspaceId` e `$automationAccountId`.  Per il parametro `-Environment`, i valori validi sono *AzureCloud* o *AzureUSGovernment*, a seconda dell'ambiente cloud usato.     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment}
   }

# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

Dopo aver eseguito questo script, i record in Log Analytics verranno visualizzati entro 10 minuti dalla scrittura di nuovi log o flussi di processo.

Per visualizzare i log eseguire questa query: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificare la configurazione
Per verificare che l'account di Automazione invii i log all'area di lavoro di Log Analytics, accertarsi che la diagnostica sia impostata correttamente nell'account di Automazione usando il comando PowerShell seguente:

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment}
   }
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO"

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Nell'output verificare quanto segue:
+ In *Logs*, il valore per *Enabled* deve essere impostato su *True*
+ Il valore di *WorkspaceId* deve essere impostato sul valore ResourceId dell'area di lavoro di Log Analytics


## <a name="log-analytics-records"></a>Record di Log Analytics
La diagnostica di Automazione di Azure crea due tipi di record in Log Analytics.

### <a name="job-logs"></a>Log del processo
| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chi ha avviato l'operazione.  I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| JobId_g |Il GUID che rappresenta l'ID del processo del runbook. |
| ResultType |Lo stato del processo di runbook.  I valori possibili sono:<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato |
| Categoria | La classificazione del tipo di dati.  Per Automazione, il valore è JobLogs. |
| OperationName | Specifica il tipo di operazione eseguita in Azure.  Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di Automazione |
| SourceSystem | Modo in cui Log Analytics ha raccolto i dati. È sempre *Azure* per la diagnostica di Azure. |
| ResultDescription |Descrive lo stato del risultato del processo di runbook.  I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato |
| CorrelationId |Il GUID che rappresenta l'ID di correlazione del processo di runbook. |
| ResourceId |Specifica l'ID risorsa dell'account di Automazione di Azure del runbook. |
| SubscriptionId | ID della sottoscrizione di Azure (GUID) per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Flussi del processo
| Proprietà | Descrizione |
| --- | --- |
| TimeGenerated |Data e ora di esecuzione del processo del runbook. |
| RunbookName_s |Il nome del runbook. |
| Caller_s |Chi ha avviato l'operazione.  I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati. |
| StreamType_s |Il tipo di flusso del processo. I valori possibili sono:<br>- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato |
| Tenant_g | GUID che identifica il tenant del chiamante. |
| JobId_g |Il GUID che rappresenta l'ID del processo del runbook. |
| ResultType |Lo stato del processo di runbook.  I valori possibili sono:<br>- In corso |
| Categoria | La classificazione del tipo di dati.  Per Automazione, il valore è JobStreams. |
| OperationName | Specifica il tipo di operazione eseguita in Azure.  Per Automazione, il valore è Job. |
| Risorsa | Nome dell'account di Automazione |
| SourceSystem | Modo in cui Log Analytics ha raccolto i dati. È sempre *Azure* per la diagnostica di Azure. |
| ResultDescription |Include il flusso di output dal runbook. |
| CorrelationId |Il GUID che rappresenta l'ID di correlazione del processo di runbook. |
| ResourceId |Specifica l'ID risorsa dell'account di Automazione di Azure del runbook. |
| SubscriptionId | ID della sottoscrizione di Azure (GUID) per l'account di Automazione. |
| ResourceGroup | Nome del gruppo di risorse dell'account di Automazione. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Visualizzazione dei log di Automazione in Log Analytics
Dopo avere avviato l'invio di log di processo di Automazione a Log Analytics, si vedrà quali operazioni è possibile eseguire con questi log in Log Analytics.

Per visualizzare i log eseguire questa query: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Inviare un messaggio di posta elettronica quando un processo del runbook non riesce o viene sospeso
Uno dei clienti più importanti chiede di poter inviare un messaggio di posta elettronica o un SMS quando si verificano problemi con un processo del runbook.   

Per creare una regola di avviso, è necessario creare prima di tutto una ricerca nei log per trovare i record del processo del runbook che dovranno richiamare l'avviso.  Fare clic su pulsante **Avviso** per creare e configurare la regola di avviso.

1. Dalla pagina della panoramica di Log Analytics fare clic su **Ricerca log**.
2. Creare una query di ricerca log per l'avviso digitando quanto segue nel campo query: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)`. È anche possibile raggruppare in base al valore RunbookName usando: `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`   

   Se sono stati configurati log da più account di Automazione o sottoscrizioni nell'area di lavoro, è possibile raggruppare gli avvisi per sottoscrizione o account di Automazione.  Il nome dell'account di automazione può essere derivato dal campo Risorsa nella ricerca di JobLogs.  
3. Per aprire la schermata **Aggiungi regola di avviso** fare clic su **Avviso** nella parte superiore della pagina. Per altre informazioni sulle opzioni per la configurazione dell'avviso, vedere [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Trovare tutti i processi completati con errori
Oltre agli avvisi per gli errori, è possibile determinare quando un processo del runbook presenta un errore non irreversibile. In questi casi PowerShell produce un flusso di errore, ma gli errori non irreversibili non comportano la sospensione o l'esito negativo del processo.    

1. Nell'area di lavoro di Log Analytics fare clic su **Ricerca log**.
2. Nel campo delle query digitare `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g` e quindi fare clic su **Search**.

### <a name="view-job-streams-for-a-job"></a>Visualizzare flussi del processo per un processo
Quando si esegue il debug di un processo, è consigliabile esaminarne anche i flussi.  La query seguente illustra tutti i flussi per un singolo processo con GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>Visualizzare lo stato cronologico del processo
Infine, è consigliabile visualizzare la cronologia dei processi nel tempo.  È possibile usare questa query per cercare lo stato dei processi nel tempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![Grafico dello stato cronologico del processo OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Riepilogo
Inviando i dati di stato e flusso dei processi di Automazione a Log Analytics è possibile ottenere maggiori informazioni sullo stato dei processi di Automazione:
+ Configurando avvisi che segnalino la presenza di un problema
+ Usando viste personalizzate e query di ricerca per visualizzare i risultati del runbook, lo stato dei processi del runbook e altri indicatori chiave o metriche correlati.  

Log Analytics offre maggiore visibilità operativa ai processi di Automazione e può consentire di gestire gli eventi imprevisti in modo più veloce.  

## <a name="next-steps"></a>Passaggi successivi
* Per alte informazioni su come creare query di ricerca diverso ed esaminare i log del processo di automazione con Log Analytics, vedere [Ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md)
* Per informazioni su come creare e recuperare l'output e i messaggi di errore da runbook, vedere [Output di runbook e messaggi in automazione di Azure](automation-runbook-output-and-messages.md)
* Per maggiori informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere come tenere traccia del processo di un runbook in [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md)
* Per altre informazioni su Log Analytics di OMS e sulle origini di raccolta dati, vedere la [panoramica della raccolta dati di Archiviazione di Azure in Log Analytics](../log-analytics/log-analytics-azure-storage.md)

