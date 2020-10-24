---
title: Gestire runbook in Automazione di Azure
description: Questo articolo descrive come gestire runbook in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 10/23/2020
ms.topic: conceptual
ms.openlocfilehash: 30979f49a48954280942d786af7e7ff592089062
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521068"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gestire runbook in Automazione di Azure

Per aggiungere un runbook ad Automazione di Azure, è possibile creare un nuovo runbook oppure importarne uno esistente da un file o dalla [raccolta di runbook](automation-runbook-gallery.md). Questo articolo contiene informazioni per la gestione di un runbook importato da un file. È possibile ottenere tutti i dettagli sull'accesso ai runbook e ai moduli della community in [Raccolte di runbook e moduli per l'Automazione di Azure](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Creare un runbook

Creare un nuovo runbook in Automazione di Azure usando il portale di Azure o Windows PowerShell. Dopo che il runbook è stato creato, è possibile modificarlo usando le informazioni negli articoli seguenti:

* [Modificare runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md)
* [Informazioni sui concetti chiave del flusso di lavoro di Windows PowerShell per i runbook di Automazione](automation-powershell-workflow.md)
* [Gestire pacchetti Python 2 in Automazione di Azure](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Creare un runbook nel portale di Azure

1. Nel portale di Azure aprire l'account di automazione.
2. Nell'hub selezionare **Runbook** in **Automazione processi** per aprire l'elenco di runbook.
3. Fare clic su **Crea un runbook**.
4. Immettere un nome per il runbook e selezionarne il [tipo](automation-runbook-types.md). Il nome del runbook deve iniziare con una lettera e può includere lettere, numeri, caratteri di sottolineatura e trattini.
5. Fare clic su **Crea** per creare il runbook e aprire l'editor.

### <a name="create-a-runbook-with-powershell"></a>Creare un runbook con PowerShell

Usare il cmdlet [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) per creare un runbook vuoto. Usare il parametro `Type` per specificare uno dei tipi di runbook definiti per `New-AzAutomationRunbook`.

L'esempio seguente illustra come creare un nuovo runbook vuoto.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importare un runbook

È possibile importare uno script PowerShell o un flusso di lavoro PowerShell ( **.ps1**), un runbook grafico ( **.graphrunbook**) o uno script Python 2 ( **.py**) per creare il proprio runbook. È necessario specificare il [tipo di runbook](automation-runbook-types.md) che viene creato durante l'importazione tenendo conto delle considerazioni seguenti.

* È possibile importare un file **.ps1** che non contiene un flusso di lavoro in un [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks) oppure in un [runbook del flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se il file viene importato in un runbook del flusso di lavoro PowerShell, questo viene convertito in un flusso di lavoro. In questo caso, i commenti vengono inclusi nel runbook per descrivere le modifiche apportate.

* È possibile importare solo un file **.ps1** che contiene un flusso di lavoro di PowerShell Workflow in un [runbook del flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se il file contiene più flussi di lavoro di PowerShell, l'importazione avrà esito negativo. È necessario salvare ogni flusso di lavoro nel relativo file e importare ogni file separatamente.

* Non importare un file **.ps1** che contiene un flusso di lavoro di PowerShell in un [runbook di PowerShell](automation-runbook-types.md#powershell-runbooks), perché il motore di script di PowerShell non è in grado di riconoscerlo.

* Importare solo un file **.graphrunbook** in un nuovo [runbook grafico](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Importare un runbook dal portale di Azure

È possibile usare la procedura seguente per importare un file di script in Automazione di Azure.

> [!NOTE]
> È possibile importare solo un file con estensione **.ps1** in un runbook del flusso di lavoro PowerShell tramite il portale.

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.
3. Fare clic su **Importa un runbook**.
4. Fare clic su **File di runbook** e selezionare il file da importare.
5. Se il campo **Nome** è abilitato, è possibile modificare il nome del runbook. Il nome deve iniziare con una lettera e può includere lettere, numeri, caratteri di sottolineatura e trattini.
6. Il [tipo di runbook](automation-runbook-types.md) viene selezionato automaticamente, ma è possibile modificarlo dopo aver valutato le restrizioni applicabili.
7. Fare clic su **Crea**. Il nuovo runbook verrà visualizzato nell'elenco dei runbook dell'account di automazione.
8. È necessario [pubblicare il runbook](#publish-a-runbook) prima di poterlo eseguire.

> [!NOTE]
> Dopo aver importato un runbook grafico, è possibile convertirlo in un runbook di altro tipo. Non è tuttavia possibile convertire un runbook grafico in un runbook testuale.

### <a name="import-a-runbook-with-windows-powershell"></a>Importare un runbook con Windows PowerShell

Usare il cmdlet [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) per importare un file di script come un runbook in formato bozza. Se il runbook esiste già, l'importazione ha esito negativo a meno che non si usi il parametro `Force` con il cmdlet.

L'esempio seguente illustra come importare un file di script in un runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>Gestire risorse

Se il runbook crea una [risorsa](automation-runbook-execution.md#resources), lo script deve verificare se la risorsa esiste già prima di tentare di crearla. Di seguito viene indicato un esempio di base.

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

## <a name="retrieve-details-from-activity-log"></a>Recuperare i dettagli dal log attività

È possibile recuperare i dettagli di un runbook, ad esempio la persona o l'account che ha avviato un runbook, dal [log attività](automation-runbook-execution.md#activity-logging) per l'account di automazione. L'esempio di PowerShell seguente consente di ottenere l'ultimo utente che ha eseguito il runbook specificato.

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

È consigliabile creare i runbook in modo che siano modulari per natura, con la logica che può essere usata nuovamente e riavviata facilmente. Il monitoraggio dello stato di avanzamento in un runbook garantisce che la logica di runbook venga eseguita correttamente in caso di problemi.

È possibile tenere traccia dello stato di avanzamento di un runbook usando un'origine esterna, ad esempio un account di archiviazione, un database o file condivisi. Creare la logica nel runbook per verificare innanzitutto lo stato dell'ultima azione eseguita. In base ai risultati del controllo, la logica può quindi ignorare o continuare attività specifiche nel runbook.

## <a name="prevent-concurrent-jobs"></a>Evitare i processi simultanei

Alcuni runbook si comportano in modo strano se vengono eseguiti in più processi contemporaneamente. In questo caso, è importante che un runbook implementi la logica per determinare se esiste già un processo in esecuzione. Di seguito viene indicato un esempio di base.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

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

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Gestire errori temporanei in uno script dipendente dal tempo

I runbook devono essere affidabili e in grado di gestire [errori](automation-runbook-execution.md#errors), ad esempio gli errori temporanei che possono causare il riavvio o un esito negativo. Se un runbook ha esito negativo, Automazione di Azure lo ripete.

Se il runbook viene eseguito in genere all'interno di un vincolo temporale, fare in modo che lo script implementi la logica per controllare il tempo di esecuzione. Questo controllo garantisce l'esecuzione di operazioni come l'avvio, l'arresto o la scalabilità orizzontale solo in momenti specifici.

> [!NOTE]
> L'ora locale nel processo sandbox di Azure è impostata sull'ora UTC. I calcoli relativi a data e ora nei runbook devono tenere in considerazione questo aspetto.

## <a name="work-with-multiple-subscriptions"></a>Usare più sottoscrizioni

Il runbook deve essere in grado di funzionare con le [sottoscrizioni](automation-runbook-execution.md#subscriptions). Per gestire più sottoscrizioni, ad esempio, il runbook usa il cmdlet [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave). Tale cmdlet garantisce che il contesto di autenticazione non venga recuperato da un altro runbook in esecuzione nello stesso sandbox. Runbook usa anche il `Get-AzContext` cmdlet per recuperare il contesto della sessione corrente e assegnarlo alla variabile `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
$AzureContext = Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint `
-Subscription $Conn.SubscriptionId

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
-ResourceGroupName $ResourceGroupName `
-AutomationAccountName $AutomationAccountName `
-Name $ChildRunbookName `
-DefaultProfile $AzureContext
```

## <a name="work-with-a-custom-script"></a>Usare uno script personalizzato

> [!NOTE]
> Non è in genere possibile eseguire script personalizzati e runbook nell'host con un agente di Log Analytics installato.

Per usare uno script personalizzato:

1. Creare un account di automazione e ottenere un [ruolo collaboratore](automation-role-based-access-control.md).
2. [Collegare l'account all'area di lavoro di Azure](../security-center/security-center-enable-data-collection.md).
3. Abilitare il [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md), [Gestione aggiornamenti](update-management/update-mgmt-overview.md) o un'altra funzionalità di Automazione. 
4. Se si dispone di un computer Linux, è necessario disporre di autorizzazioni con privilegi elevati. Eseguire l'accesso per [disattivare i controlli di firma](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testare un runbook

Quando si testa un Runbook, viene eseguita la [Versione bozza](#publish-a-runbook) e le azioni eseguite vengono completate. Non viene creata alcuna cronologia dei processi, ma nel pannello di output del test vengono visualizzati i flussi di [output](automation-runbook-output-and-messages.md#use-the-output-stream) e di [avviso ed errore](automation-runbook-output-and-messages.md#monitor-message-streams). I messaggi per il [flusso dettagliato](automation-runbook-output-and-messages.md#monitor-message-streams) vengono visualizzati solo se nel pannello di output la variabile [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) è impostata su `Continue`.

Anche se si esegue la versione bozza, il runbook viene comunque eseguito normalmente ed effettua qualsiasi azione sulle risorse nell'ambiente. Per questo motivo, è necessario testare i runbook solo con risorse non di produzione.

La procedura per testare ogni [tipo di runbook](automation-runbook-types.md) è la stessa. Non esiste alcuna differenza nei test tra l'editor di testo e l'editor grafico nel portale di Azure.

1. Aprire la versione bozza del runbook nell'[editor di testo](automation-edit-textual-runbook.md) o o nell'[editor grafico](automation-graphical-authoring-intro.md).
1. Fare clic su **Test** per aprire la pagina di test.
1. Se il runbook dispone di parametri, questi ultimi verranno elencati nel riquadro sinistro, dove è possibile specificare valori da usare per il test.
1. Se si desidera eseguire il test su un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md), per l'opzione **Impostazioni di esecuzione** specificare il valore **Ruolo di lavoro ibrido** e selezionare il nome del gruppo di destinazione.  In caso contrario, mantenere l'impostazione predefinita **Azure** per eseguire il test nel cloud.
1. Fare clic su **Avvia** per avviare il test.
1. È possibile usare i pulsanti sotto il riquadro di output per arrestare o sospendere un [flusso di lavoro di PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) o un runbook [grafico](automation-runbook-types.md#graphical-runbooks) durante il test. Quando si sospende il runbook, esso completa l'attività corrente prima di essere sospeso. Una volta che il runbook viene sospeso, è possibile arrestarlo o riavviarlo.
1. Esaminare l'output dal runbook nel pannello di output.

## <a name="publish-a-runbook"></a>Pubblicare un runbook

Quando si crea o si importa un nuovo runbook, è necessario pubblicarlo prima di poterlo eseguire. Ogni runbook in Automazione di Azure include una versione bozza e una versione pubblicata. È possibile eseguire solo la versione pubblicata, mentre è possibile modificare solo la versione bozza. La versione pubblicata non è interessata dalle modifiche apportate alla versione bozza. Quando la versione bozza deve essere resa disponibile, è possibile pubblicarla, sovrascrivendo la versione pubblicata corrente con la versione bozza.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Pubblicare un runbook nel portale di Azure

1. Aprire il runbook nel portale di Azure.
2. Fare clic su **Modifica**.
3. Fare clic su **Pubblica** e quindi su **Sì** in risposta al messaggio di verifica.

### <a name="publish-a-runbook-using-powershell"></a>Pubblicare un runbook con PowerShell

Usare il cmdlet [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) per pubblicare il runbook. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Pianificare un runbook nel portale di Azure

Dopo che il runbook è stato pubblicato, è possibile pianificarlo per l'operazione:

1. Aprire il runbook nel portale di Azure.
2. Selezionare **Pianificazioni** in **Risorse**.
3. Selezionare **Aggiungi pianificazione**.
4. Nel riquadro Pianifica runbook selezionare **Collegare una pianificazione al runbook**.
5. Scegliere **Crea una nuova pianificazione** nel riquadro Pianificazione.
6. Immettere un nome, una descrizione e altri parametri nel riquadro Nuova pianificazione.
7. Dopo aver creato la pianificazione, evidenziarla e fare clic su **OK**. A questo punto la pianificazione dovrebbe essere collegata a runbook.
8. Nella cassetta postale cercare un messaggio di notifica relativo allo stato del runbook.

## <a name="obtain-job-statuses"></a>Ottenere gli stati del processo

### <a name="view-statuses-in-the-azure-portal"></a>Visualizzare gli stati nel portale di Azure

I dettagli della gestione dei processi in Automazione di Azure sono presenti in [Processi](automation-runbook-execution.md#jobs). Quando si è pronti per visualizzare i processi di runbook, accedere all'account di automazione nel portale di Azure. A destra è possibile visualizzare un riepilogo di tutti i processi di runbook in **Statistiche del processo**.

![Riquadro Statistiche del processo](./media/manage-runbooks/automation-account-job-status-summary.png)

Il riepilogo visualizza un conteggio e una rappresentazione grafica dello stato per ogni processo eseguito.

Se si fa clic sul riquadro, viene visualizzata la pagina Processi, che include un elenco riepilogativo di tutti i processi eseguiti. In questa pagina vengono visualizzati lo stato, il nome del runbook e l'ora di inizio e di completamento di ogni processo.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Screenshot della pagina processi.":::

È possibile filtrare l'elenco dei processi selezionando **Filtra processi**. Applicare il filtro in base a un runbook specifico, allo stato del processo oppure a una scelta nell'elenco a discesa e specificare l'intervallo di tempo per la ricerca.

![Filtrare i processi in base allo stato](./media/manage-runbooks/automation-account-jobs-filter.png)

In alternativa, è possibile visualizzare i dettagli di riepilogo dei processi per un runbook specifico selezionandolo nella pagina Runbook nel proprio account di Automazione e quindi selezionare il riquadro **Processi**. Viene visualizzata la pagina Processi. In tale pagina è possibile fare clic su un record del processo per visualizzarne i dettagli e l'output.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Screenshot della pagina processi.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Recuperare gli stati del processo tramite PowerShell

Usare [Get-AzureAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) per recuperare i processi creati per un runbook e i dettagli di un processo specifico. Se si avvia un runbook usando `Start-AzAutomationRunbook`, viene restituito il processo risultante. Usare [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) per recuperare l'output del processo.

Nell'esempio seguente viene ottenuto l'ultimo processo per un runbook di esempio e ne vengono visualizzati lo stato, i valori specificati per i parametri del runbook e l'output del processo.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

L'esempio seguente recupera l'output di un processo specifico e restituisce ogni record. Se è presente un'[eccezione](automation-runbook-execution.md#exceptions) per uno dei record, lo script scrive l'eccezione anziché il valore. Questo comportamento è utile perché le eccezioni possono fornire informazioni aggiuntive che possono non essere registrate normalmente durante l'output.

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

* Per informazioni dettagliate sulla gestione del runbook, vedere [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md).
* Per preparare un runbook di PowerShell, vedere [Modificare runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md).
* Per risolvere i problemi relativi all'esecuzione di runbook, vedere [Risolvere i problemi relativi ai runbook](troubleshoot/runbooks.md).
