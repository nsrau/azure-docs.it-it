---
title: Gestire pre-script e post-script nella distribuzione di Gestione aggiornamenti in Azure
description: Questo articolo descrive come configurare e gestire pre-script e post-script per le distribuzioni di aggiornamento.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 32a8214a5bd6844c9177c7af159a453bc3673efc
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450486"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Gestire pre-script e post-script

I pre-script e i post-script sono runbook da eseguire nell'account di Automazione prima (attività preliminare) e dopo (attività successiva) una distribuzione di aggiornamento. I pre-script e i post-script vengono eseguiti nel contesto di Azure e non in locale. I pre-script sono eseguiti all'inizio della distribuzione di aggiornamento. I post-script vengono eseguiti al termine della distribuzione e dopo eventuali riavvii configurati.

## <a name="pre-script-and-post-script-requirements"></a>Requisiti di pre-script e post-script

Per usare un runbook come pre-script o post-script, è necessario importarlo nell'account di Automazione e [pubblicare il runbook](../manage-runbooks.md#publish-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parametri di pre-script e post-script

Quando si configurano i pre-script e i post-script è possibile passare i parametri come quando si pianifica un runbook. I parametri vengono definiti al momento della creazione della distribuzione di aggiornamento. I pre-script e i post-script supportano i tipi seguenti:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

I parametri runbook di pre-script e post-script non supportano i tipi booleani, oggetto o matrice. Questi valori determinano l'esito negativo dei runbook. 

Se è necessario un altro tipo di oggetto, è possibile eseguirne il cast in un altro tipo con la propria logica nel runbook.

Oltre ai parametri runbook standard, viene fornito il parametro `SoftwareUpdateConfigurationRunContext` (tipo stringa JSON). Se si definisce il parametro nel runbook di pre-script o post-script, questo viene passato automaticamente dalla distribuzione di aggiornamento. Il parametro contiene informazioni sulla distribuzione di aggiornamento costituite da un subset delle informazioni restituite dall'[API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). Le sezioni seguenti definiscono le proprietà associate.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Proprietà SoftwareUpdateConfigurationRunContext

|Proprietà  |Descrizione  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nome della configurazione di aggiornamento software.        |
|SoftwareUpdateConfigurationRunId     | ID univoco per l'esecuzione.        |
|SoftwareUpdateConfigurationSettings     | Raccolta di proprietà correlate alla configurazione di aggiornamento software.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Sistemi operativi di destinazione per la distribuzione di aggiornamento.         |
|SoftwareUpdateConfigurationSettings.duration     | Durata massima dell'esecuzione della distribuzione di aggiornamento in formato `PT[n]H[n]M[n]S` ISO8601, denominata anche "finestra di manutenzione".          |
|SoftwareUpdateConfigurationSettings.Windows     | Raccolta di proprietà relative ai computer Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Elenco di Knowledge Base escluse dalla distribuzione di aggiornamento.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classificazioni degli aggiornamenti selezionati per la distribuzione di aggiornamento.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Impostazioni di riavvio per la distribuzione di aggiornamento.        |
|azureVirtualMachines     | Elenco di ResourceID per le macchine virtuali di Azure nella distribuzione di aggiornamento.        |
|nonAzureComputerNames|Elenco di FQDN dei computer non Azure nella distribuzione di aggiornamento.|

Di seguito è riportato un esempio di stringa JSON passata al parametro **SoftwareUpdateConfigurationRunContext**:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Un esempio completo con tutte le proprietà è disponibile in: [Ottenere la configurazione dell'aggiornamento software per nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> L'oggetto `SoftwareUpdateConfigurationRunContext` può contenere voci duplicate per i computer. Questo può causare l'esecuzione di pre-script e post-script più volte nello stesso computer. Per ovviare a questo comportamento, usare `Sort-Object -Unique` per selezionare solo nomi di VM univoci.

> [!NOTE]
> Attualmente solo manuali operativi di PowerShell sono supportati come script pre/post. Altri tipi di Runbook, ad esempio Python, grafico, flusso di lavoro PowerShell, flusso di lavoro PowerShell grafico non sono attualmente supportati come script pre/post.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Usare pre-script o post-script in una distribuzione

Per usare un pre-script o un post-script in una distribuzione di aggiornamento, iniziare creando una distribuzione di aggiornamento. Selezionare **Pre-script e post-script**. Verrà visualizzata la pagina **Selezionare i pre-script e i post-script**.

![Selezionare gli script](./media/update-mgmt-pre-post-scripts/select-scripts.png)

Selezionare lo script che si vuole usare. In questo esempio viene usato il runbook **UpdateManagement-TurnOnVms**. Quando si seleziona il runbook, viene visualizzata la pagina **Configura script**. Selezionare **Pre-Script**, quindi **OK**.

Ripetere la procedura per lo script **UpdateManagement-TurnOffVms**. Quando tuttavia si sceglie il **tipo di script**, selezionare **Post-script**.

La sezione **Elementi selezionati** mostra ora entrambi gli script selezionati. Uno è un pre-script e l'altro è un post-script:

![Elementi selezionati](./media/update-mgmt-pre-post-scripts/selected-items.png)

Completare la configurazione della distribuzione di aggiornamento.

Una volta terminata la distribuzione di aggiornamento, è possibile passare a **Distribuzioni di aggiornamento** per visualizzare i risultati. Come si può notare, è indicato lo stato del pre-script e del post-script:

![Risultati aggiornamento](./media/update-mgmt-pre-post-scripts/update-results.png)

Selezionando l'esecuzione della distribuzione di aggiornamento, vengono visualizzati altri dettagli su pre-script e post-script. Viene fornito un collegamento all'origine dello script al momento dell'esecuzione.

![Risultati dell'esecuzione della distribuzione](./media/update-mgmt-pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Arrestare una distribuzione

Se si vuole arrestare una distribuzione in base a un pre-script, è necessario [generare](../automation-runbook-execution.md#throw) un'eccezione. In caso contrario, la distribuzione e il post-script saranno ancora in esecuzione. Il frammento di codice seguente illustra come generare un'eccezione.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="interact-with-machines"></a>Interagire con i computer

I pre-script e i post-task vengono eseguiti come runbook nell'account di Automazione e non direttamente nei computer della distribuzione. Anche pre-task e post-task vengono eseguiti nel contesto di Azure e non hanno accesso ai computer non Azure. Le sezioni seguenti illustrano come interagire direttamente con i computer, che si tratti di macchine virtuali di Azure o computer non Azure.

### <a name="interact-with-azure-machines"></a>Interagire con i computer Azure

Pre-task e post-task vengono eseguiti come runbook e non in modo nativo nelle macchine virtuali di Azure nella distribuzione. Per interagire con le macchine virtuali di Azure sono necessari gli elementi seguenti:

* Un account RunAs
* Un runbook da eseguire

Per interagire con i computer di Azure è necessario usare il cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) per interagire con le VM di Azure. Per un esempio di come eseguire questa operazione, vedere l'esempio di runbook [Gestione aggiornamenti: eseguire uno script con il comando Run](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interact-with-non-azure-machines"></a>Interagire con computer non Azure

Pre-task e post-task vengono eseguiti nel contesto di Azure e non hanno accesso ai computer non Azure. Per interagire con i computer non Azure è necessario quanto segue:

* Un account RunAs
* Ruolo di lavoro ibrido per runbook installato nel computer
* Un runbook da eseguire in locale
* Un runbook padre

Per interagire con i computer non Azure viene eseguito un runbook padre nel contesto di Azure. Questo runbook chiama un runbook figlio con il cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). È necessario specificare il `RunOn` parametro e specificare il nome del ruolo di lavoro ibrido per runbook in cui eseguire lo script. Vedere l'esempio di runbook [Gestione aggiornamenti: eseguire lo script in locale](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Interrompere la distribuzione di patch

Se il pre-script restituisce un errore, può essere opportuno interrompere la distribuzione. A tale scopo, è necessario [generare](/powershell/module/microsoft.powershell.core/about/about_throw) un errore nello script per qualsiasi logica che costituirebbe un errore.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Esempi

Gli esempi di script di pre-script e post-script sono disponibili nella [raccolta di Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) e in [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22) oppure è possibile importarli attraverso il portale di Azure. Nell'account di Automazione selezionare **Raccolta di runbook** in **Automazione processi**. Usare **Update Management** come filtro.

![Elenco della raccolta](./media/update-mgmt-pre-post-scripts/runbook-gallery.png)

In alternativa è possibile eseguire una ricerca in base al nome dello script, come indicato nell'elenco seguente:

* Update Management - Turn On VMs
* Update Management - Turn Off VMs
* Update Management - Run Script Locally
* Update Management - Template for Pre/Post Scripts
* Update Management - Run Script with Run Command

> [!IMPORTANT]
> Dopo aver importato i runbook, è necessario pubblicarli prima di poterli usare. A questo scopo trovare il runbook nell'account di Automazione, selezionare **Modifica** e quindi **Pubblica**.

Gli esempi sono tutti basati sul modello di base definito nell'esempio seguente. Questo modello può essere usato per creare un proprio runbook da usare con pre-script e post-script. È inclusa la logica necessaria per l'autenticazione ad Azure e la gestione del parametro `SoftwareUpdateConfigurationRunContext`.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Per runbook PowerShell non grafici, `Add-AzAccount` e `Add-AzureRMAccount` sono alias di [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](../automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sulla gestione degli aggiornamenti, vedere [gestire gli aggiornamenti e le patch per le macchine virtuali](update-mgmt-manage-updates-for-vm.md).
