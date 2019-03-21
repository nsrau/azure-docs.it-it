---
title: Esecuzione di runbook in Automazione di Azure
description: Descrive i dettagli dell'elaborazione di un runbook in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b6c61b4116983f36cef0632f7bbec4d36d203d0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842980"
---
# <a name="runbook-execution-in-azure-automation"></a>Esecuzione di runbook in Automazione di Azure

Quando si avvia un runbook in Automazione di Azure, viene creato un processo. Un processo è una singola istanza di esecuzione di un runbook. Per eseguire ogni processo, viene assegnato un computer di lavoro di Automazione di Azure. I computer di lavoro sono condivisi da molti account Azure, mentre i processi di account di automazione diversi sono isolati l'uno dall'altro. Non si ha alcun controllo sul computer di lavoro che gestisce la richiesta per il processo. In un singolo runbook possono venire eseguiti molti processi contemporaneamente. È possibile riusare l'ambiente di esecuzione per i processi dallo stesso account di automazione. Quanti più processi vengono eseguiti contemporaneamente, tanto più spesso questi possono essere inviati allo stesso ambiente sandbox. I processi in esecuzione nello stesso processo sandbox possono influire l'uno sull'altro. Un esempio è dato dal cmdlet `Disconnect-AzureRMAccount`. L'esecuzione di questo cmdlet ha l'effetto di disconnettere ogni processo runbook nel processo sandbox condiviso. Quando si visualizza l'elenco dei runbook nel portale di Azure, è visibile lo stato di tutti i processi avviati per ogni runbook. È possibile visualizzare l'elenco dei processi per il singolo runbook per tenere traccia dello stato di ognuno. I log dei processi vengono archiviati per un massimo di 30 giorni. Per una descrizione dei diversi stati dei processi, vedere [Stati dei processi](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

Nel diagramma seguente viene illustrato il ciclo di vita di un processo del Runbook per [Runbook grafici](automation-runbook-types.md#graphical-runbooks) e [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Stati del processo - Flusso di lavoro PowerShell](./media/automation-runbook-execution/job-statuses.png)

Nel diagramma seguente viene illustrato il ciclo di vita di un processo runbook per [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks).

![Stati del processo - Script PowerShell](./media/automation-runbook-execution/job-statuses-script.png)

I processi accedono alle risorse di Azure eseguendo una connessione alla sottoscrizione di Azure. Possono accedere solo alle risorse del data center dell'utente se tali risorse sono accessibili dal cloud pubblico.

## <a name="where-to-run-your-runbooks"></a>Posizione di esecuzione dei runbook

I runbook in Automazione di Automazione di Azure possono essere eseguiti in una sandbox di Azure o in un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md). Una sandbox è un ambiente condiviso di Azure che può essere usato da più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni di risorse della sandbox. I ruoli di lavoro ibridi per runbook possono essere usati per eseguire runbook direttamente nel computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. I runbook vengono infatti archiviati e gestiti in Automazione di Azure e quindi distribuiti a uno o più computer assegnati. La maggior parte dei runbook può essere facilmente eseguita nelle sandbox di Azure. Esistono però alcuni scenari specifici in cui è consigliabile scegliere un ruolo di lavoro ibrido per runbook invece di una sandbox di Azure per eseguire i runbook. La tabella seguente illustra alcuni scenari di esempio:

|Attività|Scelta migliore|Note|
|---|---|---|
|Integrazione con le risorse di Azure|Sandbox di Azure|Se ospitata in Azure, l'autenticazione è più semplice. Se si usa un ruolo di lavoro ibrido per runbook in una macchina virtuale di Azure, è possibile usare le [identità gestite per le risorse di Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Prestazioni ottimali per gestire le risorse di Azure|Sandbox di Azure|Lo script viene eseguito nello stesso ambiente, che a sua volta ha una latenza inferiore|
|Ridurre al minimo i costi operativi|Sandbox di Azure|Non c'è alcun overhead di calcolo e non è necessaria una macchina virtuale|
|Script a esecuzione prolungata|ruolo di lavoro ibrido per runbook|Le sandbox di Azure sono soggette a una [limitazione sulle risorse](../azure-subscription-service-limits.md#automation-limits)|
|Interagire con i servizi locali|ruolo di lavoro ibrido per runbook|Può accedere direttamente al computer host|
|Richiedere software ed eseguibili di terze parti|ruolo di lavoro ibrido per runbook|L'utente gestisce il sistema operativo e può installare software|
|Monitorare un file o una cartella con un runbook|ruolo di lavoro ibrido per runbook|Usare un'[attività watcher](automation-watchers-tutorial.md) in un ruolo di lavoro ibrido per runbook|
|Script con utilizzo intensivo di risorse|ruolo di lavoro ibrido per runbook| Le sandbox di Azure sono soggette a una [limitazione sulle risorse](../azure-subscription-service-limits.md#automation-limits)|
|Usare moduli con requisiti specifici| ruolo di lavoro ibrido per runbook|Di seguito sono riportati alcuni esempi:</br> **WinSCP** - dipendenza da winscp.exe </br> **IISAdministration** - IIS deve essere abilitato|
|Installare un modulo che richiede un programma di installazione|ruolo di lavoro ibrido per runbook|I moduli per la sandbox devono supportare la copia tramite xcopy|
|Usare runbook o moduli che richiedono una versione di .NET Framework diversa dalla 4.7.2|ruolo di lavoro ibrido per runbook|Le sandbox di automazione hanno .NET Framework 4.7.2 e non è possibile aggiornare questa versione|
|Script che richiedono l'elevazione dei privilegi|ruolo di lavoro ibrido per runbook|Le sandbox di non consentano l'elevazione dei privilegi. Per risolvere questo uso di Hybrid Runbook Workers ed è possibile disattivare controllo dell'account utente e usare `Invoke-Command` quando l'esecuzione del comando che richiede l'elevazione dei privilegi|

## <a name="runbook-behavior"></a>Comportamento dei runbook

I runbook vengono eseguiti in base alla logica definita al loro interno. Se un runbook viene interrotto, viene riavviato dall'inizio. Questo comportamento richiede che i runbook siano scritti in un modo che ne supporti il riavvio in caso di problemi temporanei.

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

La creazione di runbook deve essere soggetta a un'attenta considerazione. Come già accennato, i runbook devono essere creati in modo che siano affidabili e in grado di gestire errori temporanei a causa dei quali il runbook potrebbe essere riavviato o avere esito negativo. Se l'esecuzione di un runbook non riesce, viene ritentata. Se normalmente un runbook viene eseguito entro un limite di tempo, è necessario implementare nel runbook una logica che controlli il tempo di esecuzione, per assicurare che operazioni come l'avvio, l'arresto o l'aumento di istanze vengano eseguite solo in orari specifici.

### <a name="tracking-progress"></a>Tenere traccia dello stato di avanzamento

È opportuno creare i runbook in modo che siano per natura modulari. A questo scopo occorre strutturare la logica del runbook in modo che possa essere riutilizzato e riavviato facilmente. Tenere traccia dello stato di avanzamento in un runbook è un ottimo modo per assicurarsi che la relativa logica venga eseguita correttamente in caso di problemi. Un modo per tenere traccia dello stato del runbook consiste nell'usare un'origine esterna come un account di archiviazione, un database o file condivisi. Tenendo traccia dello stato esternamente, è possibile creare la logica del runbook in modo che controlli come prima cosa lo stato dell'ultima azione eseguita dal runbook e, in base ai risultati, ignori o continui attività specifiche nel runbook.

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

Creazione di runbook che gestiscono più sottoscrizioni del runbook quando deve usare la [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) cmdlet per garantire che il contesto di autenticazione non viene recuperato da un altro runbook che potrebbe essere in esecuzione Nell'ambiente sandbox stesso. È quindi necessario usare il `-AzureRmContext` parametro il `AzureRM` cmdlet e passare il contesto appropriato.

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

### <a name="using-executables-or-calling-processes"></a>Uso di file eseguibili o chiamata di processi

I runbook eseguiti in sandbox di Azure non supportano la chiamata di processi (come .exe o subprocess.call). Le sandbox di Azure sono infatti processi condivisi eseguiti in contenitori, che potrebbero non avere accesso a tutte le API sottostanti. Per le situazioni in cui è necessario usare software di terze parti o chiamare sottoprocessi, è consigliabile eseguire il runbook in un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md).

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

È possibile visualizzare uno stato riassuntivo di tutti i processi del runbook o esaminare i dettagli di uno specifico processo del runbook nel portale di Azure. È anche possibile configurare l'integrazione con l'area di lavoro di Log Analytics per inoltrare lo stato del processo del runbook e i flussi di processo. Per altre informazioni sull'integrazione con i log di monitoraggio di Azure, vedere [inoltrare lo stato del processo e i flussi del processo da automazione a log di monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

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

## <a name="retrieving-job-status-using-windows-powershell"></a>Recupero dello stato di un processo tramite Windows PowerShell

È possibile usare [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) per recuperare i processi creati per un runbook e i dettagli di un processo specifico. Se si avvia un runbook con Windows PowerShell tramite [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), viene restituito il processo risultante. Usare [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) per ottenere l'output di un processo.

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
$rg = "ResourceGroup01"
$AutomationAccount = "MyAutomationAccount"
$RunbookName = "Test-Runbook"
$JobResourceID = "/subscriptions/$subid/resourcegroups/$rg/providers/Microsoft.Automation/automationAccounts/$AutomationAccount/jobs"

Get-AzureRmLog -ResourceId $JobResourceID -MaxRecord 1 | Select Caller
```

## <a name="fair-share"></a>condivisione equa

Per condividere le risorse tra tutti i runbook nel cloud, Automazione di Azure scarica o arresta temporaneamente qualsiasi processo in esecuzione da più di tre ore. I processi dei [runbook basati su PowerShell](automation-runbook-types.md#powershell-runbooks) e dei [runbook di Python](automation-runbook-types.md#python-runbooks) vengono arrestati e non vengono riavviati e lo stato del processo risulta arrestato.

Per le attività a esecuzione prolungata è consigliabile usare un [ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md#job-behavior). I ruoli di lavoro ibridi per runbook non sono limitati da condivisione equa e non hanno una limitazione rispetto alla possibile durata dell'esecuzione di un runbook. Gli altri [limiti](../azure-subscription-service-limits.md#automation-limits) dei processi si applicano sia ai sandbox di Azure che ai ruoli di lavoro ibridi per runbook. Anche se i ruoli di lavoro ibridi per runbook non sono soggetti al limite di 3 ore della condivisione equa, i runbook eseguiti in tali ruoli di lavoro devono comunque essere sviluppati in modo da supportare i comportamenti di riavvio causati da problemi imprevisti dell'infrastruttura locale.

Un'altra opzione consiste nell'ottimizzare il runbook usando runbook figlio. Se il runbook esegue ciclicamente la stessa funzione su più risorse, ad esempio un'operazione di database su più database, è possibile spostare tale funzione in un [runbook figlio](automation-child-runbooks.md) e chiamarla con il cmdlet [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Ognuno di questi runbook figli verrà eseguito in parallelo in processi separati, riducendo la quantità totale di tempo del runbook padre richiesta per il completamento. È possibile usare la [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) cmdlet nel runbook per controllare lo stato del processo per ogni elemento figlio, se sono presenti operazioni che eseguono dopo il completamento del runbook figlio.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui diversi metodi che possono essere usati per avviare un runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md)

