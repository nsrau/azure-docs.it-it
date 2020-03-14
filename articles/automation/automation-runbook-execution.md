---
title: Esecuzione di runbook in Automazione di Azure
description: Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: c8968eb72b29b004d94e25433da65d3262287147
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367143"
---
# <a name="runbook-execution-in-azure-automation"></a>Esecuzione di runbook in Automazione di Azure

Manuali operativi vengono eseguiti in base alla logica definita al suo interno. Se un runbook viene interrotto, viene riavviato dall'inizio. Questo comportamento richiede la scrittura di manuali operativi che supportano il riavvio se si verificano problemi temporanei.

L'avvio di un Runbook in automazione di Azure crea un processo. Un processo è una singola istanza di esecuzione del Runbook. Ogni processo ha accesso alle risorse di Azure effettuando una connessione alla sottoscrizione di Azure. Il processo può accedere solo alle risorse nel Data Center se tali risorse sono accessibili dal cloud pubblico.

Automazione di Azure assegna a un thread di lavoro l'esecuzione di ogni processo durante l'esecuzione del Runbook. I computer di lavoro sono condivisi da molti account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non si ha il controllo sui servizi di lavoro richiesti dalla richiesta del processo.

Quando si visualizza l'elenco di manuali operativi nella portale di Azure, viene visualizzato lo stato di ogni processo avviato per ogni Runbook. Automazione di Azure archivia i log dei processi per un massimo di 30 giorni. 

Il diagramma seguente mostra il ciclo di vita di un processo Runbook per [PowerShell manuali operativi](automation-runbook-types.md#powershell-runbooks), [manuali operativi grafici](automation-runbook-types.md#graphical-runbooks)e [manuali operativi del flusso](automation-runbook-types.md#powershell-workflow-runbooks)di lavoro PowerShell.

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="where-to-run-your-runbooks"></a>Posizione di esecuzione dei runbook

Manuali operativi in automazione di Azure può essere eseguito in una sandbox di Azure o in un ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md). La maggior parte di manuali operativi può essere eseguita facilmente in una sandbox di Azure, un ambiente condiviso che può essere usato da più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni di risorse della sandbox.

È possibile usare un ruolo di lavoro ibrido per Runbook per eseguire manuali operativi direttamente sul computer che ospita il ruolo e sulle risorse locali nell'ambiente. Automazione di Azure archivia e gestisce manuali operativi e li recapita a uno o più computer assegnati.

Nella tabella seguente sono elencate alcune attività di esecuzione di runbook con l'ambiente di esecuzione consigliato elencato per ciascuna di esse.

|Attività|Scelta migliore|Note|
|---|---|---|
|Integrazione con le risorse di Azure|Sandbox di Azure|Ospitata in Azure, l'autenticazione è più semplice. Se si usa un ruolo di lavoro ibrido per Runbook in una macchina virtuale di Azure, è possibile usare [le identità gestite per le risorse di Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources).|
|Ottenere prestazioni ottimali per la gestione delle risorse di Azure|Sandbox di Azure|Lo script viene eseguito nello stesso ambiente, con minore latenza.|
|Ridurre al minimo i costi operativi|Sandbox di Azure|Non è previsto alcun overhead di calcolo e non è necessaria alcuna macchina virtuale.|
|Esegui script con esecuzione prolungata|ruolo di lavoro ibrido per runbook|I sandbox di Azure presentano [limitazioni sulle risorse](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Interagire con i servizi locali|ruolo di lavoro ibrido per runbook|Può accedere direttamente al computer host.|
|Richiedi software e file eseguibili di terze parti|ruolo di lavoro ibrido per runbook|È possibile gestire il sistema operativo e installare il software.|
|Monitorare un file o una cartella con un runbook|ruolo di lavoro ibrido per runbook|Usare un' [attività Watcher](automation-watchers-tutorial.md) in un ruolo di lavoro ibrido per Runbook.|
|Eseguire uno script con utilizzo intensivo delle risorse|ruolo di lavoro ibrido per runbook| I sandbox di Azure presentano [limitazioni sulle risorse](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).|
|Usare moduli con requisiti specifici| ruolo di lavoro ibrido per runbook|Ad esempio:</br> WinSCP-dipendenza da WinSCP. exe </br> IISAdministration: dipendenza dall'abilitazione di IIS.|
|Installare un modulo con un programma di installazione|ruolo di lavoro ibrido per runbook|I moduli per sandbox devono supportare la copia.|
|Usare manuali operativi o moduli che richiedono la versione di .NET Framework diversa da 4.7.2|ruolo di lavoro ibrido per runbook|Le sandbox di automazione hanno .NET Framework 4.7.2 e non esiste alcun modo per aggiornarlo.|
|Eseguire script che richiedono l'elevazione dei privilegi|ruolo di lavoro ibrido per runbook|Le sandbox non consentono l'elevazione dei privilegi. Con un ruolo di lavoro ibrido per Runbook è possibile disattivare il controllo dell'account utente e usare **Invoke-Command** quando si esegue il comando che richiede l'elevazione dei privilegi.|
|Eseguire script che richiedono l'accesso a WMI|ruolo di lavoro ibrido per runbook|I processi in esecuzione in sandbox nel cloud non possono accedere a WMI. |

## <a name="runbook-behavior"></a>Comportamento dei runbook

### <a name="creating-resources"></a>Creazione di risorse

Se il Runbook crea una risorsa, lo script deve verificare se la risorsa esiste già prima di tentare di crearla. Di seguito è riportato un esempio di base.

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

### <a name="supporting-time-dependent-scripts"></a>Supporto di script dipendenti dal tempo

La manuali operativi deve essere affidabile e in grado di gestire gli errori temporanei che possono causare il riavvio o l'esito negativo. Se un Runbook ha esito negativo, automazione di Azure la ripete.

Se il Runbook viene eseguito in genere all'interno di un vincolo temporale, fare in modo che lo script implementi la logica per controllare il tempo di esecuzione. Questo controllo garantisce l'esecuzione di operazioni come l'avvio, l'arresto o la scalabilità orizzontale solo in momenti specifici.

> [!NOTE]
> L'ora locale nel processo Azure sandbox è impostata su UTC. I calcoli relativi a data e ora nella manuali operativi devono prendere in considerazione questo fatto.

### <a name="tracking-progress"></a>Tenere traccia dello stato di avanzamento

È consigliabile creare la manuali operativi per la natura modulare, strutturando la logica Runbook in modo che possa essere riutilizzata e riavviata facilmente. Tenere traccia dello stato di avanzamento in un Runbook è un modo efficace per garantire che la logica Runbook venga eseguita correttamente in caso di problemi. È possibile tenere traccia dello stato di avanzamento di un Runbook usando un'origine esterna, ad esempio un account di archiviazione, un database o file condivisi. È possibile creare la logica nel Runbook per verificare innanzitutto lo stato dell'ultima azione eseguita. Quindi, in base al risultato del controllo, la logica può ignorare o continuare attività specifiche in Runbook.

### <a name="preventing-concurrent-jobs"></a>Impedisci processi simultanei

Alcuni manuali operativi si comportano in modo strano se vengono eseguiti contemporaneamente a più processi. In questo caso, è importante che un Runbook implementi la logica per determinare se esiste già un processo in esecuzione. Di seguito è riportato un esempio di base.

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

Per gestire più sottoscrizioni, Runbook deve usare il cmdlet [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) per assicurarsi che il contesto di autenticazione non venga recuperato da un altro Runbook in esecuzione nella stessa sandbox. Runbook usa anche il parametro`AzContext` nei cmdlet AZ Module e lo passa al contesto appropriato.

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

Questa sezione descrive alcuni modi per gestire le eccezioni o i problemi intermittenti nei manuali operativi.

#### <a name="erroractionpreference"></a>ErrorActionPreference

La variabile [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina il modo in cui PowerShell risponde a un errore non fatale. Gli errori di terminazione terminano sempre e non sono interessati da *ErrorActionPreference*.

Quando Runbook utilizza `ErrorActionPreference`, un errore normalmente non fatale, ad esempio **PathNotFound** dal cmdlet `Get-ChildItem`, impedisce il completamento del Runbook. Nell'esempio seguente viene illustrato l'utilizzo di `ErrorActionPreference`. Il comando `Write-Output` finale non viene mai eseguito, perché lo script si arresta.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try catch finally

[Try catch finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) viene usato negli script di PowerShell per gestire gli errori di terminazione. Lo script può utilizzare questo meccanismo per intercettare eccezioni specifiche o eccezioni generali. L'istruzione `catch` deve essere utilizzata per tenere traccia o tentare di gestire gli errori. Nell'esempio seguente viene tentato di scaricare un file che non esiste. Rileva l'eccezione `System.Net.WebException` e restituisce l'ultimo valore per qualsiasi altra eccezione.

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

#### <a name="throw"></a>Genera

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) può essere usato per generare un errore di terminazione. Questo meccanismo può essere utile quando si definisce la logica personalizzata in un Runbook. Se lo script soddisfa un criterio che dovrebbe arrestarlo, può utilizzare l'istruzione `throw` per arrestare. Nell'esempio seguente viene utilizzata l'istruzione per visualizzare un parametro di funzione obbligatorio.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Uso di file eseguibili o chiamata di processi

Manuali operativi eseguiti in sandbox di Azure non supportano processi di chiamata, ad esempio file eseguibili (file con**estensione exe** ) o sottoprocessi.  Il motivo è che Azure sandbox è un processo condiviso eseguito in un contenitore che potrebbe non avere accesso a tutte le API sottostanti. Per gli scenari che richiedono software di terze parti o chiamate a sottoprocessi, è consigliabile eseguire un Runbook in un ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md).

### <a name="accessing-device-and-application-characteristics"></a>Accesso alle caratteristiche del dispositivo e dell'applicazione

I processi Runbook eseguiti in sandbox di Azure non hanno accesso alle caratteristiche del dispositivo o dell'applicazione. L'API più comune utilizzata per eseguire query sulle metriche delle prestazioni in Windows è WMI e alcune delle metriche comuni sono l'utilizzo della memoria e della CPU. Tuttavia, non importa quale API viene usata, perché i processi in esecuzione nel cloud non hanno accesso all'implementazione Microsoft di Web-Based Enterprise Management (WBEM). Questa piattaforma è basata sulla Common Information Model (CIM), che fornisce gli standard di settore per la definizione delle caratteristiche del dispositivo e dell'applicazione.

## <a name="handling-errors"></a>Gestione degli errori

Il manuali operativi deve essere in grado di gestire gli errori. PowerShell presenta due tipi di errori, che terminano e non terminano. Gli errori di chiusura interrompono l'esecuzione del runbook quando si verificano. Il Runbook si interrompe con lo stato del processo non riuscito.

Gli errori non fatali consentono a uno script di continuare anche dopo essersi verificati. Un esempio di errore non fatale è quello che si verifica quando un Runbook usa il cmdlet `Get-ChildItem` con un percorso che non esiste. PowerShell rileva che il percorso non esiste, genera un errore e passa alla cartella successiva. L'errore in questo caso non imposta lo stato del processo Runbook su Failed e il processo potrebbe anche essere completato. Per forzare l'arresto di un runbook in caso di errore non fatale, è possibile usare `-ErrorAction Stop` nel cmdlet.

## <a name="handling-jobs"></a>Gestione dei processi

È possibile riutilizzare l'ambiente di esecuzione per i processi dallo stesso account di automazione. In un singolo runbook possono venire eseguiti molti processi contemporaneamente. Quanti più processi vengono eseguiti contemporaneamente, tanto più spesso questi possono essere inviati allo stesso ambiente sandbox.

I processi in esecuzione nello stesso processo sandbox possono interessare l'uno con l'altro. Un esempio è l'esecuzione del cmdlet `Disconnect-AzAccount`. L'esecuzione di questo cmdlet disconnette tutti i processi del Runbook nel processo sandbox condiviso.

I processi di PowerShell avviati da un Runbook in esecuzione in una sandbox di Azure potrebbero non essere eseguiti in modalità linguaggio completo. Per altre informazioni sulle modalità del linguaggio PowerShell, vedere [modalità del linguaggio PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Per altri dettagli sull'interazione con i processi in automazione di Azure, vedere [recupero dello stato dei processi con PowerShell](#retrieving-job-status-using-powershell).

### <a name="job-statuses"></a>Stati dei processi

Nella tabella seguente vengono descritti gli stati possibili per un processo.

| Stato | Descrizione |
|:--- |:--- |
| Operazione completata |Il processo è stato completato. |
| Operazione non riuscita |Impossibile compilare un Runbook grafico o un flusso di lavoro PowerShell. Non è stato possibile avviare uno script di PowerShell Runbook oppure il processo ha restituito un'eccezione. Vedere [tipi di Runbook di automazione di Azure](automation-runbook-types.md).|
| Failed, waiting for resources |Il processo non è riuscito perché ha raggiunto il limite di [condivisione equa](#fair-share) tre volte iniziando ogni volta dallo stesso checkpoint o dall'inizio del runbook. |
| Accodato |Il processo è in attesa che le risorse di un ruolo di lavoro di automazione diventino disponibili per poter essere avviate. |
| Avvio in corso |Il processo è stato assegnato a un computer di lavoro e il sistema lo sta avviando. |
| Resuming |Il sistema sta riprendendo il processo dopo che è stato sospeso. |
| In esecuzione |Il processo è in esecuzione. |
| Running, waiting for resources |Il processo è stato scaricato perché ha raggiunto il limite di condivisione equa. Riprenderà a breve dall'ultimo checkpoint. |
| Arrestato |Il processo è stato arrestato dall'utente prima del completamento. |
| Stopping |Il sistema sta arrestando il processo. |
| Suspended |Si applica solo a [manuali operativi grafico e del flusso di lavoro PowerShell](automation-runbook-types.md) . Il processo è stato sospeso dall'utente, dal sistema o da un comando del runbook. Se un Runbook non ha un checkpoint, inizia dall'inizio. Se ha un checkpoint, può essere nuovamente avviato e riprendere dall'ultimo checkpoint. Il sistema sospende il Runbook solo quando si verifica un'eccezione. Per impostazione predefinita, la variabile `ErrorActionPreference` è impostata su continue (continua), a indicare che il processo viene mantenuto in un errore. Se la variabile di preferenza è impostata su Stop, il processo viene sospeso in caso di errore.  |
| Suspending |Si applica solo a [manuali operativi grafico e del flusso di lavoro PowerShell](automation-runbook-types.md) . Il sistema sta provando a sospendere il processo su richiesta dell'utente. Il runbook deve raggiungere il checkpoint successivo prima di poter essere sospeso. Se ha già superato l'ultimo checkpoint, viene completato prima di poter essere sospeso. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Visualizzazione dello stato del processo dal portale di Azure

È possibile visualizzare uno stato riassuntivo di tutti i processi del runbook o esaminare i dettagli di uno specifico processo del runbook nel portale di Azure. È anche possibile configurare l'integrazione con l'area di lavoro Log Analytics per inoltrare lo stato del processo del runbook e i flussi di processo. Per altre informazioni sull'integrazione con i log di monitoraggio di Azure, vedere [trasmettere lo stato del processo e i flussi del processo dall'automazione ai log di monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

A destra dell'account di automazione selezionato è possibile visualizzare un riepilogo di tutti i processi del Runbook nel riquadro **statistiche processi** .

![Riquadro Statistiche del processo](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Questo riquadro Visualizza un conteggio e una rappresentazione grafica dello stato del processo per ogni processo eseguito.

Facendo clic sul riquadro viene visualizzata la pagina processi, che include un elenco riepilogativo di tutti i processi eseguiti. In questa pagina vengono visualizzati lo stato, il nome Runbook, l'ora di inizio e l'ora di completamento di ogni processo.

![Pagina Processi dell'account di Automazione](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

È possibile filtrare l'elenco dei processi selezionando **Filtra processi**. Filtrare in base a un Runbook specifico, allo stato del processo o a una scelta dall'elenco a discesa e specificare l'intervallo di tempo per la ricerca.

![Filtrare lo stato del processo](./media/automation-runbook-execution/automation-account-jobs-filter.png)

In alternativa, è possibile visualizzare i dettagli di riepilogo dei processi per uno specifico Runbook selezionando tale Runbook dalla pagina manuali operativi nell'account di automazione e quindi selezionando il riquadro **processi** . Questa azione Visualizza la pagina processi. Da qui è possibile fare clic sul record del processo per visualizzarne i dettagli e l'output.

![Pagina Processi dell'account di Automazione](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>Visualizzazione del riepilogo dei processi

Il riepilogo del processo descritto in precedenza consente di esaminare un elenco di tutti i processi creati per un determinato Runbook e il relativo stato più recente. Per visualizzare informazioni dettagliate e l'output di un processo, fare clic sul relativo nome nell'elenco. La visualizzazione dettagliata del processo include i valori per i parametri Runbook che sono stati forniti al processo.

Per visualizzare i processi per un runbook, seguire questa procedura.

1. Nel portale di Azure, selezionare **Automazione** e selezionare il nome di un account di automazione.
2. Dall'hub selezionare **manuali operativi** in **automazione processi**.
3. Nella pagina manuali operativi selezionare un Runbook dall'elenco.
3. Nella pagina del runbook selezionato fare clic sul riquadro **Processi**.
4. Fare clic su uno dei processi nell'elenco e visualizzarne i dettagli e l'output nella pagina dei dettagli del processo Runbook.

### <a name="retrieving-job-status-using-powershell"></a>Recupero dello stato del processo tramite PowerShell

Usare il cmdlet `Get-AzAutomationJob` per recuperare i processi creati per un Runbook e i dettagli di un determinato processo. Se si avvia un runbook con PowerShell usando `Start-AzAutomationRunbook`, viene restituito il processo risultante. Usare [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) per recuperare l'output del processo.

Nell'esempio seguente viene ottenuto l'ultimo processo per un Runbook di esempio e ne vengono visualizzati lo stato, i valori specificati per i parametri Runbook e l'output del processo.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Nell'esempio seguente viene recuperato l'output per un processo specifico e viene restituito ogni record. Se è presente un'eccezione per uno dei record, lo script scrive l'eccezione anziché il valore. Questo comportamento è utile, poiché le eccezioni possono fornire informazioni aggiuntive che potrebbero non essere registrate normalmente durante l'output.

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

È possibile recuperare i dettagli di Runbook, ad esempio la persona o l'account che ha avviato il Runbook, dal log attività per l'account di automazione. L'esempio di PowerShell seguente fornisce l'ultimo utente per eseguire il Runbook specificato.

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

## <a name="fair-share"></a>Condivisione di risorse tra manuali operativi

Per condividere le risorse tra tutti manuali operativi nel cloud, automazione di Azure Scarica temporaneamente o arresta qualsiasi processo eseguito per più di tre ore. I processi per [manuali operativi di PowerShell](automation-runbook-types.md#powershell-runbooks) e [manuali operativi Python](automation-runbook-types.md#python-runbooks) vengono arrestati e non riavviati e lo stato del processo viene arrestato.

Per le attività a esecuzione prolungata, è consigliabile usare un ruolo di lavoro ibrido per Runbook. I ruoli di lavoro ibridi per runbook non sono limitati da condivisione equa e non hanno una limitazione rispetto alla possibile durata dell'esecuzione di un runbook. Gli altri [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) dei processi si applicano sia ai sandbox di Azure che ai ruoli di lavoro ibridi per runbook. Sebbene i ruoli di lavoro ibridi per Runbook non siano limitati dal limite di tre ore, è necessario sviluppare manuali operativi per l'esecuzione nei ruoli di lavoro che supportano i riavvii da problemi di infrastruttura locale imprevisti.

Un'altra opzione consiste nell'ottimizzare un Runbook usando manuali operativi figlio. Ad esempio, il Runbook potrebbe eseguire il ciclo della stessa funzione su diverse risorse, ad esempio un'operazione di database su diversi database. È possibile spostare questa funzione in un [Runbook figlio](automation-child-runbooks.md) e fare in modo che il Runbook lo chiami usando `Start-AzAutomationRunbook`. I manuali operativi figlio vengono eseguiti in parallelo in processi distinti.

L'uso di manuali operativi figlio riduce la quantità totale di tempo necessario per il completamento del Runbook padre. Il Runbook può usare il cmdlet `Get-AzAutomationJob` per verificare lo stato del processo per un Runbook figlio se sono ancora presenti operazioni da eseguire dopo il completamento del figlio.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui metodi che possono essere usati per avviare un Runbook in automazione di Azure, vedere [avvio di un Runbook in automazione di Azure](automation-starting-a-runbook.md).
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
