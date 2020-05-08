---
title: Gestire runbook in Automazione di Azure
description: Questo articolo descrive come gestire runbook in Automazione di Azure. Vengono illustrate le operazioni di base e sono state aggiunte alcune procedure consigliate.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 687579825c652888112ff8ddff7401b3305e3a8e
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871190"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gestire runbook in Automazione di Azure

È possibile aggiungere una Runbook ad automazione di Azure creandone una nuova o importandone una esistente da un file o dalla [raccolta Runbook](automation-runbook-gallery.md). Questo articolo fornisce informazioni per la gestione di un Runbook importato da un file. È possibile trovare tutti i dettagli relativi all'accesso a manuali operativi e moduli della community in [Runbook e alle raccolte di moduli per automazione di Azure](automation-runbook-gallery.md).

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Creare un runbook

Creare un nuovo Runbook in automazione di Azure usando il portale di Azure o Windows PowerShell. Una volta creato il Runbook, è possibile modificarlo usando le informazioni in:

* [Modificare Runbook testuali in automazione di Azure](automation-edit-textual-runbook.md) 
* [Informazioni sui concetti chiave dei flussi di lavoro di Windows PowerShell per manuali operativi di automazione](automation-powershell-workflow.md)
* [Creazione grafica in automazione di Azure](automation-graphical-authoring-intro.md)
* [Gestire pacchetti Python 2 in Automazione di Azure](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Creare un runbook nel portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Dall'hub selezionare **manuali operativi** in **automazione processi** per aprire l'elenco di manuali operativi.
3. Fare clic su **Create a Runbook**.
4. Immettere un nome per il Runbook e selezionare il relativo [tipo](automation-runbook-types.md). Il nome del Runbook deve iniziare con una lettera e può contenere lettere, numeri, caratteri di sottolineatura e trattini.
5. Fare clic su **Crea** per creare il runbook e aprire l'editor.

### <a name="create-a-runbook-with-powershell"></a>Creare un runbook con PowerShell

Usare il cmdlet [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) per creare un Runbook vuoto. Usare il `Type` parametro per specificare uno dei tipi Runbook definiti per `New-AzAutomationRunbook`.

Nell'esempio seguente viene illustrato come creare un nuovo Runbook vuoto.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importare un runbook

È possibile importare uno script di PowerShell o del flusso di lavoro PowerShell (con estensione**ps1**), un Runbook grafico (**graphrunbook**) o uno script Python 2 (**. py**) per creare il proprio Runbook.  È necessario specificare il [tipo di runbook](automation-runbook-types.md) che viene creato durante l'importazione tenendo conto delle considerazioni seguenti.

* È possibile importare un file con **estensione ps1** che non contiene un flusso di lavoro in un [Runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) o in un [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se viene importato in un Runbook del flusso di lavoro PowerShell, questo viene convertito in un flusso di lavoro. In questo caso, i commenti vengono inclusi in Runbook per descrivere le modifiche apportate.

* È possibile importare solo un file con **estensione ps1** contenente un flusso di lavoro PowerShell in un [Runbook del flusso di lavoro PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se il file contiene più flussi di lavoro di PowerShell, l'importazione avrà esito negativo. È necessario salvare ogni flusso di lavoro nel relativo file e importare ogni file separatamente.

* Non importare un file con **estensione ps1** contenente un flusso di lavoro PowerShell in un [Runbook di PowerShell](automation-runbook-types.md#powershell-runbooks), perché il motore di script di PowerShell non è in grado di riconoscerlo.

* Importare un file con **estensione graphrunbook** solo in un nuovo [Runbook grafico](automation-runbook-types.md#graphical-runbooks). 

### <a name="import-a-runbook-from-the-azure-portal"></a>Importare un Runbook dalla portale di Azure

È possibile usare la procedura seguente per importare un file di script in Automazione di Azure.

> [!NOTE]
> È possibile importare solo un file con **estensione ps1** in un Runbook del flusso di lavoro PowerShell usando il portale.

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.
3. Fare clic su **Import a Runbook**.
4. Fare clic su **file Runbook** e selezionare il file da importare.
5. Se il campo **nome** è abilitato, è possibile modificare il nome del Runbook. Il nome deve iniziare con una lettera e può contenere lettere, numeri, caratteri di sottolineatura e trattini.
6. Il [tipo di runbook](automation-runbook-types.md) viene selezionato automaticamente, ma è possibile modificarlo dopo aver valutato le restrizioni applicabili.
7. Scegliere **Crea**. Il nuovo runbook verrà visualizzato nell'elenco dei runbook dell'account di automazione.
8. È necessario [pubblicare il runbook](#publish-a-runbook) prima di poterlo eseguire.

> [!NOTE]
> Dopo aver importato un Runbook grafico, è possibile convertirlo in un altro tipo. Tuttavia, non è possibile convertire un Runbook grafico in un Runbook testuale.

### <a name="import-a-runbook-with-windows-powershell"></a>Importare un runbook con Windows PowerShell

Usare il cmdlet [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) per importare un file di script come bozza di Runbook. Se il Runbook esiste già, l'importazione ha esito negativo a meno `Force` che non si usi il parametro con il cmdlet.

Nell'esempio seguente viene illustrato come importare un file di script in un Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Gestisci risorse

Se il Runbook crea una [risorsa](automation-runbook-execution.md#resources), lo script deve verificare se la risorsa esiste già prima di tentare di crearla. Di seguito è riportato un esempio di base.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>Recupera i dettagli dal log attività

È possibile recuperare i dettagli di Runbook, ad esempio la persona o l'account che ha avviato un Runbook, dal [log attività](automation-runbook-execution.md#activity-logging) per l'account di automazione. L'esempio di PowerShell seguente fornisce l'ultimo utente per eseguire il Runbook specificato.

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

## <a name="track-progress"></a>Monitorare l'avanzamento

È consigliabile creare la manuali operativi per la natura modulare, con la logica che può essere riutilizzata e riavviata facilmente. Tenere traccia dello stato di avanzamento in un Runbook garantisce che la logica Runbook venga eseguita correttamente in caso di problemi. 

È possibile tenere traccia dello stato di avanzamento di un Runbook usando un'origine esterna, ad esempio un account di archiviazione, un database o file condivisi. Creare la logica nel Runbook per verificare innanzitutto lo stato dell'ultima azione eseguita. Quindi, in base ai risultati del controllo, la logica può ignorare o continuare attività specifiche in Runbook.

## <a name="prevent-concurrent-jobs"></a>Evitare i processi simultanei

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

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Gestione degli errori temporanei in uno script dipendente dal tempo

La manuali operativi deve essere affidabile e in grado di gestire gli [errori](automation-runbook-execution.md#errors), inclusi errori temporanei che possono causare il riavvio o l'esito negativo. Se un Runbook ha esito negativo, automazione di Azure la ripete.

Se il Runbook viene eseguito in genere all'interno di un vincolo temporale, fare in modo che lo script implementi la logica per controllare il tempo di esecuzione. Questo controllo garantisce l'esecuzione di operazioni come l'avvio, l'arresto o la scalabilità orizzontale solo in momenti specifici.

> [!NOTE]
> L'ora locale nel processo Azure sandbox è impostata su UTC. I calcoli relativi a data e ora nella manuali operativi devono prendere in considerazione questo fatto.

## <a name="work-with-multiple-subscriptions"></a>Usare più sottoscrizioni

Il Runbook deve essere in grado di usare le [sottoscrizioni](automation-runbook-execution.md#subscriptions). Per gestire più sottoscrizioni, ad esempio, Runbook usa il cmdlet [Disable-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) . Questo cmdlet garantisce che il contesto di autenticazione non venga recuperato da un altro Runbook in esecuzione nella stessa sandbox. Runbook usa anche il`AzContext` parametro sui cmdlet AZ Module e lo passa al contesto appropriato.

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

## <a name="work-with-a-custom-script"></a>Usare uno script personalizzato

> [!NOTE]
> Non è in genere possibile eseguire script personalizzati e manuali operativi nell'host con un agente Log Analytics installato. 

Per usare uno script personalizzato:

1. Creare un account di automazione e ottenere un [ruolo di collaboratore](automation-role-based-access-control.md).
2. [Collegare l'account all'area di lavoro di Azure](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection.md).
3. Abilitare il ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md), [Gestione aggiornamenti](automation-update-management.md)o un'altra funzionalità di automazione. 
4. Se si dispone di un computer Linux, è necessario disporre di autorizzazioni elevate. Accedere per [disabilitare i controlli della firma](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testare un runbook

Quando si testa un Runbook, viene eseguita la [Versione bozza](#publish-a-runbook) e le azioni eseguite vengono completate. Non viene creata alcuna cronologia processo, ma i flussi di [output](automation-runbook-output-and-messages.md#output-stream) e di [avviso e di errore](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati nel riquadro di output del test. I messaggi nel [flusso dettagliato](automation-runbook-output-and-messages.md#message-streams) vengono visualizzati nel riquadro di output solo se la variabile [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) è impostata su `Continue`.

Anche se si esegue la versione bozza, il runbook viene comunque eseguito normalmente ed effettua qualsiasi azione sulle risorse nell'ambiente. Per questo motivo, è necessario testare i runbook solo con risorse non di produzione.

La procedura per testare ogni [tipo di Runbook](automation-runbook-types.md) è la stessa. Non esiste alcuna differenza nei test tra l'editor di testo e l'editor grafico nel portale di Azure.

1. Aprire la versione bozza del Runbook nell' [editor di testo](automation-edit-textual-runbook.md) o nell' [Editor grafico](automation-graphical-authoring-intro.md).
1. Fare clic su **test** per aprire la pagina di test.
1. Se il Runbook dispone di parametri, questi vengono elencati nel riquadro a sinistra, in cui è possibile specificare i valori da usare per il test.
1. Se si vuole eseguire il test in un ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md), modificare le **impostazioni di esecuzione** nel ruolo di **lavoro ibrido** e selezionare il nome del gruppo di destinazione.  In caso contrario, mantenere l'impostazione predefinita **Azure** per eseguire il test nel cloud.
1. Fare clic su **Avvia** per avviare il test.
1. È possibile usare i pulsanti nel riquadro output per arrestare o sospendere un [flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o un Runbook [grafico](automation-runbook-types.md#graphical-runbooks) durante il test. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
1. Esaminare l'output del Runbook nel riquadro di output.

## <a name="publish-a-runbook"></a>Pubblicare un runbook

Quando si crea o si importa un nuovo runbook, è necessario pubblicarlo prima di poterlo eseguire. Ogni Runbook in automazione di Azure include una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Quando la versione bozza deve essere resa disponibile, è possibile pubblicarla, sovrascrivendo la versione pubblicata corrente con la versione bozza.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Pubblicare un Runbook nel portale di Azure

1. Aprire il runbook nel portale di Azure.
2. Fare clic su **Edit**.
3. Fare clic su **pubblica** e quindi su **Sì** in risposta al messaggio di verifica.

### <a name="publish-a-runbook-using-powershell"></a>Pubblicare un runbook con PowerShell

Usare il cmdlet [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) per pubblicare il Runbook. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Pianificare un Runbook nel portale di Azure

Quando il Runbook è stato pubblicato, è possibile pianificarlo per l'operazione:

1. Aprire il runbook nel portale di Azure.
2. Selezionare **pianificazioni** in **risorse**.
3. Selezionare **Aggiungi pianificazione**.
4. Nel riquadro Pianifica Runbook selezionare **collega una pianificazione al Runbook**.
5. Scegliere **Crea una nuova pianificazione** nel riquadro pianificazione.
6. Immettere un nome, una descrizione e altri parametri nel riquadro nuova pianificazione. 
7. Una volta creata la pianificazione, evidenziarla e fare clic su **OK**. A questo punto dovrebbe essere collegato a Runbook.
8. Cercare un messaggio di posta elettronica nella cassetta postale per notificare lo stato di Runbook.

## <a name="obtain-job-statuses"></a>Ottenere gli Stati del processo

### <a name="view-statuses-in-the-azure-portal"></a>Visualizzare gli Stati nel portale di Azure

I dettagli relativi alla gestione dei processi in automazione di Azure sono disponibili in [processi](automation-runbook-execution.md#jobs). Quando si è pronti per visualizzare i processi di Runbook, usare portale di Azure e accedere all'account di automazione. A destra è possibile visualizzare un riepilogo di tutti i processi di Runbook in **statistiche processi**. 

![Riquadro Statistiche del processo](./media/manage-runbooks/automation-account-job-status-summary.png)

Il riepilogo Visualizza un conteggio e una rappresentazione grafica dello stato del processo per ogni processo eseguito.

Facendo clic sul riquadro viene visualizzata la pagina Processi, che include un elenco riepilogativo di tutti i processi eseguiti. In questa pagina vengono visualizzati lo stato, il nome Runbook, l'ora di inizio e l'ora di completamento di ogni processo.

![Pagina Processi dell'account di Automazione](./media/manage-runbooks/automation-account-jobs-status-blade.png)

È possibile filtrare l'elenco dei processi selezionando **Filtra processi**. Filtrare in base a un Runbook specifico, allo stato del processo o a una scelta dall'elenco a discesa e specificare l'intervallo di tempo per la ricerca.

![Filtrare lo stato del processo](./media/manage-runbooks/automation-account-jobs-filter.png)

In alternativa, è possibile visualizzare i dettagli di riepilogo dei processi per uno specifico Runbook selezionando tale Runbook dalla pagina manuali operativi nell'account di automazione e quindi selezionando **processi**. Questa azione Visualizza la pagina processi. Da qui è possibile fare clic su un record del processo per visualizzarne i dettagli e l'output.

![Pagina Processi dell'account di Automazione](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>Recuperare gli Stati del processo tramite PowerShell

Usare il cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) per recuperare i processi creati per un Runbook e i dettagli di un determinato processo. Se si avvia un Runbook usando `Start-AzAutomationRunbook`, viene restituito il processo risultante. Usare [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) per recuperare l'output del processo.

Nell'esempio seguente viene ottenuto l'ultimo processo per un Runbook di esempio e ne vengono visualizzati lo stato, i valori specificati per i parametri Runbook e l'output del processo.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

Nell'esempio seguente viene recuperato l'output per un processo specifico e viene restituito ogni record. Se è presente un' [eccezione](automation-runbook-execution.md#exceptions) per uno dei record, lo script scrive l'eccezione anziché il valore. Questo comportamento è utile poiché le eccezioni possono fornire informazioni aggiuntive che potrebbero non essere registrate normalmente durante l'output.

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

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sull'elaborazione di Runbook, vedere [esecuzione di Runbook in automazione di Azure](automation-runbook-execution.md).
* Per altre informazioni sulla modifica di PowerShell e manuali operativi del flusso di lavoro PowerShell con un editor di testo, vedere [modificare manuali operativi testuali in automazione di Azure](automation-edit-textual-runbook.md).
* Per altre informazioni sulla creazione di Runbook grafici, vedere [creazione grafica in automazione di Azure](automation-graphical-authoring-intro.md).
