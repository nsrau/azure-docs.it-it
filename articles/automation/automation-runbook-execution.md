---
title: Esecuzione di runbook in Automazione di Azure
description: Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c8968eb72b29b004d94e25433da65d3262287147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367143"
---
# <a name="runbook-execution-in-azure-automation"></a>Esecuzione di runbook in Automazione di Azure

I Runbook vengono eseguiti in base alla logica definita al loro interno. Se un runbook viene interrotto, viene riavviato dall'inizio. Questo comportamento richiede la scrittura di runbook che supportano il riavvio in caso di problemi temporanei.

L'avvio di un runbook in Automazione di Azure crea un processo. Un processo è una singola istanza di esecuzione del runbook. Ogni processo ha accesso alle risorse di Azure effettuando una connessione alla sottoscrizione di Azure.Each job has access to Azure resources by making a connection to your Azure subscription. Il processo ha accesso alle risorse nel data center solo se tali risorse sono accessibili dal cloud pubblico.

Automazione di Azure assegna a un worker l'esecuzione di ogni processo durante l'esecuzione del runbook. I computer di lavoro sono condivisi da molti account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non si ha il controllo su quali servizi di lavoro la richiesta di lavoro.

Quando si visualizza l'elenco dei runbook nel portale di Azure, viene visualizzato lo stato di ogni processo avviato per ogni runbook. Automazione di Azure archivia i log dei processi per un massimo di 30 giorni. 

Nel diagramma seguente viene illustrato il ciclo di vita di un processo di runbook per runbook di [PowerShell](automation-runbook-types.md#powershell-runbooks), [Runbook grafici](automation-runbook-types.md#graphical-runbooks)e [runbook del flusso di lavoro](automation-runbook-types.md#powershell-workflow-runbooks)di PowerShell .

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="where-to-run-your-runbooks"></a>Posizione di esecuzione dei runbook

I runbook in Automazione di Azure possono essere eseguiti in una sandbox di Azure o in un ruolo di lavoro ibrido per [runbook.](automation-hybrid-runbook-worker.md) La maggior parte dei runbook può essere eseguita facilmente in una sandbox di Azure, un ambiente condiviso che può usare più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni di risorse della sandbox.

È possibile usare un ruolo di lavoro ibrido per runbook per eseguire runbook direttamente nel computer che ospita il ruolo e in base alle risorse locali nell'ambiente. Automazione di Azure archivia e gestisce i runbook e quindi li recapita a uno o più computer assegnati.

Nella tabella seguente sono elencate alcune attività di esecuzione del runbook con l'ambiente di esecuzione consigliato elencato per ognuna di esse.

|Attività|Scelta migliore|Note|
|---|---|---|
|Integrazione con le risorse di Azure|Sandbox di Azure|Ospitato in Azure, l'autenticazione è più semplice. Se si usa un ruolo di lavoro ibrido per runbook in una macchina virtuale di Azure, è possibile usare le identità gestite per le risorse di Azure.If you are using a Hybrid Runbook Worker on an Azure VM, you can use [managed identities for Azure resources.](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Ottenere prestazioni ottimali per gestire le risorse di AzureObtain optimal performance to manage Azure resources|Sandbox di Azure|Lo script viene eseguito nello stesso ambiente, con una latenza inferiore.|
|Ridurre al minimo i costi operativi|Sandbox di Azure|Non è presente alcun sovraccarico di calcolo e non è necessario una macchina virtuale.|
|Esegui script a esecuzione prolungata|ruolo di lavoro ibrido per runbook|I Sandbox di Azure hanno [limitazioni sulle risorse.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interagire con i servizi locali|ruolo di lavoro ibrido per runbook|Può avere accesso direttamente alla macchina host.|
|Richiedere software ed eseguibili di terze parti|ruolo di lavoro ibrido per runbook|È possibile gestire il sistema operativo e installare il software.|
|Monitorare un file o una cartella con un runbook|ruolo di lavoro ibrido per runbook|Usare [un'attività Watcher](automation-watchers-tutorial.md) in un ruolo di lavoro ibrido per runbook.|
|Eseguire uno script a elevato utilizzo di risorseRun a resource-intensive script|ruolo di lavoro ibrido per runbook| I Sandbox di Azure hanno [limitazioni sulle risorse.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Utilizzare i moduli con requisiti specifici| ruolo di lavoro ibrido per runbook|Ad esempio:</br> WinSCP - dipendenza da winscp.exe </br> IISAdministration - dipendenza dall'abilitazione di IIS.|
|Installare un modulo con un programma di installazione|ruolo di lavoro ibrido per runbook|I moduli per sandbox devono supportare la copia.|
|Usare runbook o moduli che richiedono una versione di .NET Framework diversa dalla versione 4.7.2Use runbooks or modules that require .NET Framework version different from 4.7.2|ruolo di lavoro ibrido per runbook|Sandbox di automazione hanno .NET Framework 4.7.2 e non è possibile aggiornarlo.|
|Eseguire script che richiedono l'elevazione dei privilegiRun scripts that require elevation|ruolo di lavoro ibrido per runbook|Le sandbox non consentono l'elevazione. Con un ruolo di lavoro ibrido per runbook, è possibile disattivare il controllo dell'account utente e usare **Invoke-Command** quando si esegue il comando che richiede l'elevazione dei privilegi.|
|Eseguire script che richiedono l'accesso a WMI|ruolo di lavoro ibrido per runbook|I processi in esecuzione in sandbox nel cloud non hanno accesso a WMI. |

## <a name="runbook-behavior"></a>Comportamento dei runbook

### <a name="creating-resources"></a>Creazione di risorse

Se il runbook crea una risorsa, lo script deve verificare se la risorsa esiste già prima di tentare di crearla. Ecco un esempio di base.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>Supporto di script dipendenti dal tempoSupporting time-dependent scripts

I runbook devono essere affidabili e in grado di gestire errori temporanei che possono causare il riavvio o l'esito negativo. Se un runbook ha esito negativo, Automazione di Azure ritenta.

Se il runbook viene in genere eseguito entro un vincolo di tempo, fare in modo che lo script implementi la logica per controllare il tempo di esecuzione. Questo controllo garantisce l'esecuzione di operazioni quali avvio, arresto o scalabilità orizzontale solo in orari specifici.

> [!NOTE]
> L'ora locale nel processo sandbox di Azure è impostata su UTC. I calcoli per la data e l'ora nei runbook devono prendere in considerazione questo fatto.

### <a name="tracking-progress"></a>Tenere traccia dello stato di avanzamento

È buona norma creare i runbook per essere modulari in natura, strutturando la logica del runbook in modo che possa essere riutilizzata e riavviata facilmente. Il rilevamento dello stato di avanzamento in un runbook è un buon modo per garantire che la logica del runbook venga eseguita correttamente in caso di problemi. È possibile tenere traccia dello stato di avanzamento di un runbook usando un'origine esterna, ad esempio un account di archiviazione, un database o file condivisi. È possibile creare la logica nel runbook per controllare innanzitutto lo stato dell'ultima azione eseguita. Quindi, in base al risultato del controllo, la logica può ignorare o continuare attività specifiche nel runbook.

### <a name="preventing-concurrent-jobs"></a>Prevenzione di processi simultanei

Alcuni runbook si comportano in modo strano se vengono eseguiti in più processi contemporaneamente. In questo caso, è importante che un runbook implementi la logica per determinare se esiste già un processo in esecuzione. Ecco un esempio di base.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
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

Per gestire più sottoscrizioni, il runbook deve utilizzare il cmdlet [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) per garantire che il contesto di autenticazione non venga recuperato da un altro runbook in esecuzione nella stessa sandbox. Il runbook utilizza`AzContext` inoltre il parametro nei cmdlet del modulo Az e gli passa il contesto corretto.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Gestione delle eccezioni

In questa sezione vengono descritti alcuni modi per gestire le eccezioni o i problemi intermittenti nei runbook.

#### <a name="erroractionpreference"></a>ErrorActionPreference

La variabile [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina il modo in cui PowerShell risponde a un errore non di terminazione. Gli errori di terminazione terminano sempre e non sono interessati da *ErrorActionPreference*.

Quando il runbook utilizza `ErrorActionPreference`, un errore normalmente non di terminazione, ad esempio **PathNotFound** dal `Get-ChildItem` cmdlet, interrompe il completamento del runbook. L'esempio seguente illustra l'uso di `ErrorActionPreference`. Il `Write-Output` comando finale non viene mai eseguito, poiché lo script si interrompe.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Prova Catch finalmente

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) viene usato negli script di PowerShell per gestire gli errori di terminazione. Lo script può utilizzare questo meccanismo per intercettare eccezioni specifiche o eccezioni generali. L'istruzione `catch` deve essere utilizzata per tenere traccia o tentare di gestire gli errori. Nell'esempio seguente si tenta di scaricare un file che non esiste. Rileva l'eccezione `System.Net.WebException` e restituisce l'ultimo valore per qualsiasi altra eccezione.

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

#### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) può essere utilizzato per generare un errore di terminazione. Questo meccanismo può essere utile quando si definisce la logica personalizzata in un runbook. Se lo script soddisfa un criterio che deve interromperlo, può utilizzare l'istruzione `throw` per interrompere. Nell'esempio seguente viene utilizzata questa istruzione per visualizzare un parametro di funzione obbligatorio.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Uso di file eseguibili o chiamata di processi

I Runbook eseguiti nelle Sandbox di Azure non supportano la chiamata di processi, ad esempio eseguibili (file**con estensione exe)** o sottoprocessi.  Il motivo è che una sandbox di Azure è un processo condiviso eseguito in un contenitore che potrebbe non avere accesso a tutte le API sottostanti. Per gli scenari che richiedono software di terze parti o chiamate a processi secondari, è consigliabile eseguire un runbook in un ruolo di lavoro ibrido per [runbook.](automation-hybrid-runbook-worker.md)

### <a name="accessing-device-and-application-characteristics"></a>Accesso alle caratteristiche del dispositivo e dell'applicazione

I processi runbook eseguiti nelle Sandbox di Azure non hanno accesso alle caratteristiche del dispositivo o dell'applicazione. L'API più comune utilizzata per eseguire query sulle metriche delle prestazioni in Windows è WMI, con alcune delle metriche comuni che sono l'utilizzo della memoria e della CPU. Tuttavia, non importa quale API viene utilizzata, come i processi in esecuzione nel cloud non hanno accesso all'implementazione Microsoft di Web-Based Enterprise Management (WBEM). Questa piattaforma si basa sul Common Information Model (CIM), fornendo gli standard di settore per la definizione delle caratteristiche dei dispositivi e delle applicazioni.

## <a name="handling-errors"></a>Gestione degli errori

I tuoi runbook devono essere in grado di gestire gli errori. In PowerShell sono disponibili due tipi di errori, terminazione e non terminata. Gli errori di terminazione interrompono l'esecuzione del runbook quando si verificano. Il runbook si interrompe con lo stato del processo Non riuscito.

Gli errori non terminati consentono a uno script di continuare anche dopo che si sono verificati. Un esempio di errore non di terminazione è quello `Get-ChildItem` che si verifica quando un runbook utilizza il cmdlet con un percorso che non esiste. PowerShell rileva che il percorso non esiste, genera un errore e passa alla cartella successiva. L'errore in questo caso non imposta lo stato del processo del runbook su Non riuscito e il processo potrebbe anche essere completato. Per forzare l'arresto di un runbook in caso di errore non fatale, è possibile usare `-ErrorAction Stop` nel cmdlet.

## <a name="handling-jobs"></a>Gestione dei lavori

È possibile riutilizzare l'ambiente di esecuzione per i processi dallo stesso account di automazione. In un singolo runbook possono venire eseguiti molti processi contemporaneamente. Quanti più processi vengono eseguiti contemporaneamente, tanto più spesso questi possono essere inviati allo stesso ambiente sandbox.

I processi in esecuzione nello stesso processo sandbox possono influire l'uno sull'altro. Un esempio è `Disconnect-AzAccount` l'esecuzione del cmdlet. L'esecuzione di questo cmdlet disconnette ogni processo runbook nel processo sandbox condiviso.

I processi di PowerShell avviati da un runbook eseguito in una sandbox di Azure potrebbero non essere eseguiti in modalità lingua completa. Per altre informazioni sulle modalità del linguaggio PowerShell, vedere [Modalità del linguaggio PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Per altre informazioni sull'interazione con i processi in Automazione di Azure, vedere [Recupero dello stato dei processi con PowerShell.](#retrieving-job-status-using-powershell)

### <a name="job-statuses"></a>Stati dei processi

Nella tabella seguente vengono descritti gli stati possibili per un processo.

| Stato | Descrizione |
|:--- |:--- |
| Completi |Il processo è stato completato. |
| Operazione non riuscita |Impossibile compilare un runbook grafico o del flusso di lavoro di PowerShell.A graphical or PowerShell Workflow runbook failed to compile. Impossibile avviare un runbook di script di PowerShell o il processo ha avuto un'eccezione. Vedere [Tipi di runbook di Automazione di Azure.](automation-runbook-types.md)|
| Failed, waiting for resources |Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](#fair-share) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook. |
| Queued |Il processo è in attesa che le risorse in un worker di automazione diventino disponibili in modo che possa essere avviato. |
| Avvio in corso |Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando. |
| Resuming |Il sistema sta riprendendo il processo dopo che è stato sospeso. |
| In esecuzione |Il processo è in esecuzione. |
| Running, waiting for resources |Il processo è stato scaricato perché ha raggiunto il limite di condivisione equa . Riprenderà a breve dall'ultimo checkpoint. |
| Arrestato |Il processo è stato arrestato dall'utente prima del completamento. |
| Stopping |Il sistema sta arrestando il processo. |
| Suspended |Si applica solo [ai runbook grafici e al flusso di lavoro di PowerShell.Applies to graphical and PowerShell Workflow runbooks](automation-runbook-types.md) only. Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Se un runbook non dispone di un checkpoint, inizia dall'inizio. Se ha un checkpoint, può essere nuovamente avviato e riprendere dall'ultimo checkpoint. Il sistema sospende il runbook solo quando si verifica un'eccezione. Per impostazione `ErrorActionPreference` predefinita, la variabile è impostata su Continua, a indicare che il processo continua a essere eseguito in caso di errore. Se la variabile di preferenza è impostata su Stop, il processo viene sospeso in caso di errore.  |
| Suspending |Si applica solo [ai runbook grafici e al flusso di lavoro di PowerShell.Applies to graphical and PowerShell Workflow runbooks](automation-runbook-types.md) only. Il sistema sta provando a sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, viene completato prima di poter essere sospeso. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Visualizzazione dello stato del processo dal portale di Azure

È possibile visualizzare uno stato riassuntivo di tutti i processi del runbook o esaminare i dettagli di uno specifico processo del runbook nel portale di Azure. È anche possibile configurare l'integrazione con l'area di lavoro Log Analytics per inoltrare lo stato del processo del runbook e i flussi di processo. Per altre informazioni sull'integrazione con i log di Monitoraggio di Azure, vedere [Inoltrare lo stato dei processi e i flussi di lavoro dall'automazione ai log](automation-manage-send-joblogs-log-analytics.md)di Monitoraggio di Azure.For more information about integrating with Azure Monitor logs, see Forward job status and job streams from Automation to Azure Monitor logs.

A destra dell'account di automazione selezionato, è possibile visualizzare un riepilogo di tutti i processi del runbook nel riquadro **Statistiche processo.**

![Riquadro Statistiche del processo](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Questo riquadro visualizza un conteggio e una rappresentazione grafica dello stato del processo per ogni processo eseguito.

Facendo clic sul riquadro viene visualizzata la pagina Processi, che include un elenco riepilogativo di tutti i processi eseguiti. Questa pagina mostra lo stato, il nome del runbook, l'ora di inizio e l'ora di completamento di ogni processo.

![Pagina Processi dell'account di Automazione](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

È possibile filtrare l'elenco dei processi selezionando **Filtra processi**. Filtrare in base a un runbook specifico, allo stato del processo o a una scelta dall'elenco a discesa e specificare l'intervallo di tempo per la ricerca.

![Filtrare lo stato del processo](./media/automation-runbook-execution/automation-account-jobs-filter.png)

In alternativa, è possibile visualizzare i dettagli di riepilogo dei processi per un runbook specifico selezionando il runbook dalla pagina Runbooks dell'account di automazione e quindi selezionando il riquadro **Processi.** Questa azione presenta la pagina Processi. Da qui, è possibile fare clic sul record del processo per visualizzarne i dettagli e l'output.

![Pagina Processi dell'account di Automazione](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Visualizzazione del riepilogo del lavoro

Il riepilogo dei processi descritto in precedenza consente di esaminare un elenco di tutti i processi creati per un runbook specifico e il relativo stato più recente. Per visualizzare informazioni dettagliate e output per un processo, fare clic sul relativo nome nell'elenco. La visualizzazione dettagliata del processo include i valori per i parametri del runbook forniti a tale processo.

Per visualizzare i processi per un runbook, seguire questa procedura.

1. Nel portale di Azure, selezionare **Automazione** e selezionare il nome di un account di automazione.
2. Dall'hub, selezionare **Runbook** in **Process Automation**.
3. Nella pagina Runbooks selezionare un runbook dall'elenco.
3. Nella pagina del runbook selezionato fare clic sul riquadro **Processi**.
4. Fare clic su uno dei processi nell'elenco e visualizzarne i dettagli e l'output nella pagina dei dettagli del processo del runbook.

### <a name="retrieving-job-status-using-powershell"></a>Recupero dello stato del processo tramite PowerShellRetrieving job status using PowerShell

Utilizzare `Get-AzAutomationJob` il cmdlet per recuperare i processi creati per un runbook e i dettagli di un determinato processo. Se si avvia un runbook `Start-AzAutomationRunbook`con PowerShell usando PowerShell, viene restituito il processo risultante. Utilizzare [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) per recuperare l'output del processo.

Nell'esempio seguente viene ottenuto l'ultimo processo per un runbook di esempio e ne vengono visualizzati lo stato, i valori forniti per i parametri del runbook e l'output del processo.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Nell'esempio seguente viene recuperato l'output per un processo specifico e viene restituito ogni record. Se si verifica un'eccezione per uno dei record, lo script scrive l'eccezione anziché il valore. Questo comportamento è utile, poiché le eccezioni possono fornire informazioni aggiuntive che potrebbero non essere registrate normalmente durante l'output.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
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

## <a name="getting-details-from-the-activity-log"></a>Recupero dei dettagli dal log attività

È possibile recuperare i dettagli del runbook, ad esempio la persona o l'account che ha avviato il runbook, dal registro attività per l'account di automazione. L'esempio di PowerShell seguente fornisce l'ultimo utente che esegue il runbook specificato.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Condivisione delle risorse tra i runbook

Per condividere le risorse tra tutti i runbook nel cloud, Automazione di Azure scarica temporaneamente o arresta qualsiasi processo eseguito per più di tre ore. I processi per i runbook di PowerShell e i runbook Python vengono arrestati e non riavviati e lo stato del processo diventa Arrestato.Jobs for [PowerShell runbooks](automation-runbook-types.md#powershell-runbooks) and [Python runbooks](automation-runbook-types.md#python-runbooks) are stopped and not restarted, and the job status becomes Stopped.

Per le attività a esecuzione prolungata, è consigliabile usare un ruolo di lavoro ibrido per runbook. I ruoli di lavoro ibridi per runbook non sono limitati da condivisione equa e non hanno una limitazione rispetto alla possibile durata dell'esecuzione di un runbook. Gli altri [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) dei processi si applicano sia ai sandbox di Azure che ai ruoli di lavoro ibridi per runbook. Anche se i ruoli di lavoro ibridi per runbook non sono limitati dal limite di condivisione equa di 3 ore, è consigliabile sviluppare runbook per l'esecuzione sui ruoli di lavoro che supportano il riavvio da problemi imprevisti dell'infrastruttura locale.

Un'altra opzione consiste nell'ottimizzare un runbook utilizzando i runbook figlio. Ad esempio, il runbook potrebbe scorrere la stessa funzione su diverse risorse, ad esempio un'operazione di database in diversi database. È possibile spostare questa funzione in un [runbook](automation-child-runbooks.md) `Start-AzAutomationRunbook`figlio e fare in modo che il runbook la chiami utilizzando . I runbook figlio vengono eseguiti in parallelo in processi separati.

L'utilizzo di runbook figlio riduce la quantità totale di tempo per il completamento del runbook padre. Il runbook può `Get-AzAutomationJob` utilizzare il cmdlet per controllare lo stato del processo per un runbook figlio se dispone ancora di operazioni da eseguire dopo il completamento del figlio.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui metodi che possono essere usati per avviare un runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure.To](automation-starting-a-runbook.md)learn more about the methods that can be used to start a runbook in Azure Automation, see Starting a runbook in Azure Automation.
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere Documenti di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
