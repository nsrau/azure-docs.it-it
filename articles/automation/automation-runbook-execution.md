---
title: Esecuzione di runbook in Automazione di Azure
description: Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 01a321503a2c55bfc28720675932e6813cdab320
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850602"
---
# <a name="runbook-execution-in-azure-automation"></a>Esecuzione di runbook in Automazione di Azure

Quando si avvia un runbook in Automazione di Azure, viene creato un processo. Un processo è una singola istanza di esecuzione di un runbook. Per eseguire ogni processo, viene assegnato un computer di lavoro di Automazione di Azure. I computer di lavoro sono condivisi da molti account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non si ha alcun controllo sul computer di lavoro che gestisce la richiesta per il processo. In un singolo runbook possono venire eseguiti molti processi contemporaneamente. È possibile riusare l'ambiente di esecuzione per i processi dallo stesso account di automazione. Quanti più processi vengono eseguiti contemporaneamente, tanto più spesso questi possono essere inviati allo stesso ambiente sandbox. I processi in esecuzione nello stesso processo sandbox possono influire l'uno sull'altro. Un esempio è dato dal cmdlet `Disconnect-AzureRMAccount`. L'esecuzione di questo cmdlet ha l'effetto di disconnettere ogni processo runbook nel processo sandbox condiviso. Quando si visualizza l'elenco dei runbook nel portale di Azure, è visibile lo stato di tutti i processi avviati per ogni runbook. È possibile visualizzare l'elenco dei processi per il singolo runbook per tenere traccia dello stato di ognuno. I log dei processi vengono archiviati per un massimo di 30 giorni. Per una descrizione dei diversi stati dei processi, vedere [Stati dei processi](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Il diagramma seguente mostra il ciclo di vita di un processo Runbook per [PowerShell manuali operativi](automation-runbook-types.md#powershell-runbooks), [manuali operativi grafici](automation-runbook-types.md#graphical-runbooks) e [manuali operativi del flusso](automation-runbook-types.md#powershell-workflow-runbooks)di lavoro PowerShell.

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

I processi accedono alle risorse di Azure eseguendo una connessione alla sottoscrizione di Azure. Possono accedere solo alle risorse del data center dell'utente se tali risorse sono accessibili dal cloud pubblico.

## <a name="where-to-run-your-runbooks"></a>Posizione di esecuzione dei runbook

I runbook in Automazione di Automazione di Azure possono essere eseguiti in una sandbox di Azure o in un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md). Una sandbox è un ambiente condiviso di Azure che può essere usato da più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni di risorse della sandbox. I ruoli di lavoro ibridi per Runbook possono eseguire manuali operativi direttamente sul computer che ospita il ruolo e sulle risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer assegnati. La maggior parte di manuali operativi può essere eseguita facilmente nelle sandbox di Azure. Esistono però alcuni scenari specifici in cui è consigliabile scegliere un ruolo di lavoro ibrido per runbook invece di una sandbox di Azure per eseguire i runbook. La tabella seguente illustra alcuni scenari di esempio:

|Attività|Scelta migliore|Note|
|---|---|---|
|Integrazione con le risorse di Azure|Sandbox di Azure|Se ospitata in Azure, l'autenticazione è più semplice. Se si usa un ruolo di lavoro ibrido per runbook in una macchina virtuale di Azure, è possibile usare le [identità gestite per le risorse di Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Prestazioni ottimali per gestire le risorse di Azure|Sandbox di Azure|Lo script viene eseguito nello stesso ambiente, che a sua volta presenta una minore latenza|
|Ridurre al minimo i costi operativi|Sandbox di Azure|Non c'è alcun overhead di calcolo e non è necessaria una macchina virtuale|
|Script a esecuzione prolungata|ruolo di lavoro ibrido per runbook|Le sandbox di Azure sono soggette a una [limitazione sulle risorse](../azure-subscription-service-limits.md#automation-limits)|
|Interagire con i servizi locali|ruolo di lavoro ibrido per runbook|Può accedere direttamente al computer host|
|Richiedere software ed eseguibili di terze parti|ruolo di lavoro ibrido per runbook|L'utente gestisce il sistema operativo e può installare software|
|Monitorare un file o una cartella con un runbook|ruolo di lavoro ibrido per runbook|Usare un'[attività watcher](automation-watchers-tutorial.md) in un ruolo di lavoro ibrido per runbook|
|Script con utilizzo intensivo di risorse|ruolo di lavoro ibrido per runbook| Le sandbox di Azure sono soggette a una [limitazione sulle risorse](../azure-subscription-service-limits.md#automation-limits)|
|Usare moduli con requisiti specifici| ruolo di lavoro ibrido per runbook|Di seguito sono riportati alcuni esempi:</br> **WinSCP** - dipendenza da winscp.exe </br> **IISAdministration** - IIS deve essere abilitato|
|Installare un modulo che richiede un programma di installazione|ruolo di lavoro ibrido per runbook|I moduli per sandbox devono essere copiabili|
|Usare runbook o moduli che richiedono una versione di .NET Framework diversa dalla 4.7.2|ruolo di lavoro ibrido per runbook|Le sandbox di automazione hanno .NET Framework 4.7.2 e non è possibile aggiornare questa versione|
|Script che richiedono l'elevazione dei privilegi|ruolo di lavoro ibrido per runbook|Le sandbox non consentono l'elevazione dei privilegi. Per risolvere questo problema, usare un ruolo di lavoro ibrido per Runbook ed è possibile `Invoke-Command` disattivare UAC e usare quando si esegue il comando che richiede l'elevazione dei privilegi|
|Script che richiedono l'accesso a WMI|ruolo di lavoro ibrido per runbook|I processi in esecuzione in sandbox nel cloud non [possono accedere a WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Comportamento dei runbook

I runbook vengono eseguiti in base alla logica definita al loro interno. Se un runbook viene interrotto, viene riavviato dall'inizio. Questo comportamento richiede che i runbook siano scritti in un modo che ne supporti il riavvio in caso di problemi temporanei.

I processi di PowerShell avviati da un Runbook eseguito in una sandbox di Azure potrebbero non essere eseguiti in modalità linguaggio completo. Per altre informazioni sulle modalità del linguaggio PowerShell, vedere [modalità del linguaggio PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Per altri dettagli su come interagire con i processi in automazione di Azure, vedere [recupero dello stato dei processi con PowerShell](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>Creazione di risorse

Se lo script crea risorse, verificare se la risorsa esiste già prima di provare a ricrearla. Di seguito è illustrato un esempio di base:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Script dipendenti dal tempo

La creazione di runbook deve essere soggetta a un'attenta considerazione. Come già accennato, i runbook devono essere creati in modo che siano affidabili e in grado di gestire errori temporanei a causa dei quali il runbook potrebbe essere riavviato o avere esito negativo. Se un Runbook ha esito negativo, viene eseguito un nuovo tentativo. Se un Runbook viene normalmente eseguito all'interno di un vincolo di tempo, la logica per controllare il tempo di esecuzione deve essere implementata in Runbook per garantire che le operazioni come l'avvio, l'arresto o la scalabilità orizzontale vengano eseguite solo in momenti specifici.

> [!NOTE]
> L'ora locale nel processo sandbox di Azure è impostata sull'ora UTC. I calcoli relativi a data e ora nel manuali operativi devono essere presi in considerazione.

### <a name="tracking-progress"></a>Tenere traccia dello stato di avanzamento

È opportuno creare i runbook in modo che siano per natura modulari. A questo scopo occorre strutturare la logica del runbook in modo che possa essere riutilizzato e riavviato facilmente. Tenere traccia dello stato di avanzamento in un Runbook è un modo efficace per garantire che la logica in un Runbook venga eseguita correttamente in caso di problemi. Un modo per tenere traccia dello stato del runbook consiste nell'usare un'origine esterna come un account di archiviazione, un database o file condivisi. Tenendo traccia dello stato esterno, è possibile creare la logica nel Runbook per verificare innanzitutto lo stato dell'ultima azione eseguita dal Runbook. Quindi, in base ai risultati, ignorare o continuare attività specifiche in Runbook.

### <a name="prevent-concurrent-jobs"></a>Evitare i processi simultanei

Se eseguiti in più processi contemporaneamente, alcuni runbook potrebbero mostrare un comportamento anomalo. In questo caso è importante implementare una logica che controlli se un runbook ha già un processo in esecuzione. Di seguito è illustrato un esempio di base in proposito:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Uso di più sottoscrizioni

Quando si creano manuali operativi che gestiscono più sottoscrizioni, per Runbook è necessario usare il cmdlet [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) per assicurarsi che il contesto di autenticazione non venga recuperato da un altro Runbook che potrebbe essere in esecuzione nella stessa sandbox. È quindi necessario usare il `-AzureRmContext` parametro `AzureRM` sui cmdlet e passargli il contesto appropriato.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Gestione delle eccezioni

Quando si creano script, è importante essere in grado di gestire le eccezioni e potenziali errori intermittenti. Di seguito sono riportati alcuni modi diversi per gestire le eccezioni o i problemi intermittenti con il manuali operativi:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

La variabile di preferenza [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina il modo in cui PowerShell risponde a un errore non fatale. Gli errori di terminazione non sono `$ErrorActionPreference`interessati da e vengono sempre terminati. Se si `$ErrorActionPreference`utilizza, un errore normalmente non irreversibile come `PathNotFound` dal `Get-ChildItem` cmdlet arresterà il completamento del Runbook. Nell'esempio seguente viene illustrato `$ErrorActionPreference`l'utilizzo di. La riga `Write-Output` finale non verrà mai eseguita perché lo script verrà interrotto.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try catch finally

[Try catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) viene usato negli script di PowerShell per semplificare la gestione degli errori di terminazione. Con try catch è possibile rilevare eccezioni specifiche o eccezioni generali. L'istruzione catch deve essere utilizzata per tenere traccia degli errori o per tentare di gestire l'errore. Nell'esempio seguente viene tentato di scaricare un file che non esiste. Rileva l' `System.Net.WebException` eccezione, se si è verificata un'altra eccezione, viene restituito l'ultimo valore.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Generare

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) può essere usato per generare un errore di terminazione. Questa operazione può essere utile quando si definisce la logica personalizzata in un Runbook. Se viene soddisfatto un determinato criterio che dovrebbe arrestare lo script, è possibile usare `throw` per arrestare lo script. Nell'esempio seguente viene illustrato il computer a cui è richiesto `throw`un parametro di funzione tramite.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Uso di file eseguibili o chiamata di processi

Manuali operativi eseguiti in sandbox di Azure non supportano i processi di chiamata (ad esempio, un file con estensione exe o subprocess. Call). Questo perché sandbox di Azure sono processi condivisi eseguiti in contenitori, che potrebbero non avere accesso a tutte le API sottostanti. Per le situazioni in cui è necessario usare software di terze parti o chiamare sottoprocessi, è consigliabile eseguire il runbook in un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Caratteristiche del dispositivo e dell'applicazione

I processi Runbook eseguiti in sandbox di Azure non hanno accesso alle caratteristiche del dispositivo o dell'applicazione. L'API più comune utilizzata per eseguire query sulle metriche delle prestazioni in Windows è WMI. Alcune di queste metriche comuni sono l'utilizzo della memoria e della CPU. Tuttavia, non è rilevante quale API viene usata. I processi in esecuzione nel cloud non hanno accesso all'implementazione Microsoft di Web based Enterprise Management (WBEM), che si basa sulla Common Information Model (CIM), che sono gli standard di settore per la definizione delle caratteristiche del dispositivo e dell'applicazione.

## <a name="job-statuses"></a>Stati dei processi

La tabella seguente descrive i diversi stati possibili per un processo. PowerShell ha due tipi di errori, fatali e non fatali. Gli errori fatali impostano lo stato del runbook su **Non riuscito**, se si verificano. Gli errori non fatali consentono allo script di continuare anche dopo che si verificano. Un esempio di errore non fatale è l'uso del cmdlet `Get-ChildItem` con un percorso che non esiste. PowerShell rileva che il percorso non esiste, genera un errore e passa alla cartella successiva. Questo errore non imposta su **Failed** lo stato del runbook, che può essere contrassegnato come **Completato**. Per forzare l'arresto di un runbook in caso di errore non fatale, è possibile usare `-ErrorAction Stop` nel cmdlet.

| Stato | DESCRIZIONE |
|:--- |:--- |
| Completi |Il processo è stato completato. |
| Failed |Per [Runbook grafico e runbook flusso di lavoro PowerShell](automation-runbook-types.md), la compilazione di runbook non è riuscita. Per [Runbook di Script di PowerShell](automation-runbook-types.md), non è stato possibile avviare il runbook o il processo conteneva un'eccezione. |
| Failed, waiting for resources |Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](#fair-share) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook. |
| Queued |Il processo è in attesa che diventino disponibili risorse in un computer di lavoro di Automazione per poter essere avviato. |
| Avvio in corso |Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando. |
| Resuming |Il sistema sta riprendendo il processo dopo che è stato sospeso. |
| In esecuzione |Il processo è in esecuzione. |
| Running, waiting for resources |Il processo è stato scaricato perché ha raggiunto il limite di [condivisione equa](#fair-share) . Riprende a breve dall'ultimo checkpoint. |
| Arrestato |Il processo è stato arrestato dall'utente prima del completamento. |
| Stopping |Il sistema sta arrestando il processo. |
| Suspended |Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Se un runbook non ha un checkpoint, viene avviato dall'inizio del runbook. Se ha un checkpoint, può essere nuovamente avviato e riprendere dall'ultimo checkpoint. Il runbook viene sospeso dal sistema solo quando si verifica un'eccezione. Per impostazione predefinita, il valore di ErrorActionPreference è impostato su **Continua**, a indicare che il processo continua a essere eseguito in caso di errore. Se questa variabile di preferenza è impostata su **Stop**, il processo viene sospeso in caso di errore. Si applica solo a [Runbook grafico e al flusso di lavoro PowerShell](automation-runbook-types.md) . |
| Suspending |Il sistema sta provando a sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, il processo viene completato prima di poter essere sospeso. Si applica solo a [Runbook grafico e al flusso di lavoro PowerShell](automation-runbook-types.md) . |

## <a name="viewing-job-status-from-the-azure-portal"></a>Visualizzazione dello stato del processo dal portale di Azure

È possibile visualizzare uno stato riassuntivo di tutti i processi del runbook o esaminare i dettagli di uno specifico processo del runbook nel portale di Azure. È anche possibile configurare l'integrazione con l'area di lavoro Log Analytics per inoltrare lo stato del processo del runbook e i flussi di processo. Per altre informazioni sull'integrazione con i log di monitoraggio di Azure, vedere [trasmettere lo stato del processo e i flussi del processo dall'automazione ai log di monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Riepilogo dei processi del Runbook di Automazione

A destra dell'account di Automazione selezionato è disponibile un riepilogo di tutti i processi del runbook nel riquadro **Statistiche processi**.

![Riquadro Statistiche del processo](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Questo riquadro visualizza un conteggio e una rappresentazione grafica dello stato dei processi per tutti i processi eseguiti.

Facendo clic sul riquadro viene visualizzata la pagina **Processi**, che include un elenco riepilogativo di tutti i processi eseguiti. Questa pagina mostra lo stato, gli orari di inizio e gli orari di completamento.

![Pagina Processi dell'account di Automazione](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

È possibile filtrare l'elenco dei processi selezionando **Filtra processi** e filtrare in base a uno specifico runbook, allo stato del processo o, nell'elenco a discesa, all'intervallo di tempo entro cui eseguire la ricerca.

![Filtrare i processi in base allo stato](./media/automation-runbook-execution/automation-account-jobs-filter.png)

In alternativa, è possibile visualizzare i dettagli di riepilogo dei processi per un runbook specifico selezionando tale runbook dal pannello **Runbook** nel proprio account di Automazione e, successivamente, selezionare il riquadro **Processi**. Questa azione visualizza la pagina **Processi**, dove è possibile fare clic sul record del processo per visualizzarne i dettagli e l'output.

![Pagina Processi dell'account di Automazione](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Riepilogo dei processi

È possibile visualizzare un elenco di tutti i processi creati per un determinato runbook e il relativo stato più recente. È possibile filtrare l'elenco in base allo stato del processo e all'intervallo di date dell'ultima modifica del processo. Fare clic sul nome di un processo per visualizzarne le informazioni dettagliate e l'output. La visualizzazione dettagliata del processo include i valori per i parametri del runbook che sono stati forniti al processo.

Per visualizzare i processi per un runbook, seguire questa procedura.

1. Nel portale di Azure, selezionare **Automazione** e selezionare il nome di un account di automazione.
2. Dall'hub selezionare **Runbook** e quindi nella pagina **Runbook** selezionare un runbook dall'elenco.
3. Nella pagina del runbook selezionato fare clic sul riquadro **Processi**.
4. Fare clic su uno dei processi nell'elenco per aprire la pagina dei dettagli del processo del runbook e visualizzarne i dettagli e l'output.

## <a name="retrieving-job-status-using-powershell"></a>Recupero dello stato del processo tramite PowerShell

È possibile usare [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) per recuperare i processi creati per un runbook e i dettagli di un processo specifico. Se si avvia un runbook con PowerShell usando [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), viene restituito il processo risultante. Usare [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) per ottenere l'output di un processo.

I comandi di esempio seguenti recuperano l'ultimo processo per un runbook di esempio e ne visualizzano lo stato, i valori specificati per i parametri del runbook e l'output del processo.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

L'esempio seguente recupera l'output di un processo specifico e restituisce ogni record. Se si è verificata un'eccezione per uno dei record, invece del valore viene scritta l'eccezione. Questo comportamento è utile perché le eccezioni possono fornire informazioni aggiuntive che potrebbero non essere registrate normalmente durante l'output.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Ottenere dettagli dal log attività

Altri dettagli, ad esempio la persona o l'account che ha avviato il runbook, possono essere recuperati dal log attività relativo all'account di Automazione. L'esempio di PowerShell seguente consente di ottenere i dati relativi all'ultimo utente che ha eseguito il runbook in questione:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>condivisione equa

Per condividere le risorse tra tutti manuali operativi nel cloud, automazione di Azure Scarica temporaneamente o arresta qualsiasi processo eseguito per più di tre ore. I processi dei [runbook basati su PowerShell](automation-runbook-types.md#powershell-runbooks) e dei [runbook di Python](automation-runbook-types.md#python-runbooks) vengono arrestati e non vengono riavviati e lo stato del processo risulta arrestato.

Per le attività a esecuzione prolungata è consigliabile usare un [ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md#job-behavior). I ruoli di lavoro ibridi per runbook non sono limitati da condivisione equa e non hanno una limitazione rispetto alla possibile durata dell'esecuzione di un runbook. Gli altri [limiti](../azure-subscription-service-limits.md#automation-limits) dei processi si applicano sia ai sandbox di Azure che ai ruoli di lavoro ibridi per runbook. Sebbene i ruoli di lavoro ibridi per Runbook non siano limitati dal limite di 3 ore di condivisione equa, è necessario sviluppare manuali operativi per supportare i comportamenti di riavvio da problemi di infrastruttura locale imprevisti.

Un'altra opzione consiste nell'ottimizzare il runbook usando runbook figlio. Se il runbook esegue ciclicamente la stessa funzione su più risorse, ad esempio un'operazione di database su più database, è possibile spostare tale funzione in un [runbook figlio](automation-child-runbooks.md) e chiamarla con il cmdlet [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Ognuno di questi runbook figlio viene eseguito in parallelo in processi separati. Questo comportamento riduce la quantità totale di tempo per il completamento del runbook padre. È possibile usare il cmdlet [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) in Runbook per verificare lo stato del processo per ogni figlio se sono presenti operazioni che vengono eseguite dopo il completamento del Runbook figlio.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui diversi metodi che possono essere usati per avviare un runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la [documentazione di PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview).
