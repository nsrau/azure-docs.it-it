---
title: Gestire pre-script e post-script nella distribuzione di gestione degli aggiornamenti in AzureManage pre-scripts and post-scripts in your Update Management deployment in Azure
description: In questo articolo viene descritto come configurare e gestire pre-script e post-script per le distribuzioni degli aggiornamenti.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: 00cde5255f9c9a2baa7c7042ae2a8f73448da0ae
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679981"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Gestire pre-script e post-script

I pre-script e i post-script vengono eseguiti nell'account di Automazione di Azure prima (pre-attività) e dopo (post-attività) una distribuzione di aggiornamento. I pre-script e i postscript vengono eseguiti nel contesto di Azure, non in locale. I pre-script vengono eseguiti all'inizio della distribuzione dell'aggiornamento. I post-script vengono eseguiti alla fine della distribuzione e dopo eventuali riavvii configurati.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="pre-script-and-post-script-requirements"></a>Requisiti di pre-script e post-script

Affinché un runbook venga usato come pre-script o postscript, è necessario importarlo nell'account di automazione e [pubblicare il runbook](manage-runbooks.md#publishing-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parametri di pre-script e post-script

Quando si configurano pre-script e post-script, è possibile passare parametri come la pianificazione di un runbook. I parametri vengono definiti al momento della creazione della distribuzione di aggiornamento. I pre-script e i post-script supportano i seguenti tipi:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

I parametri runbook pre-script e post-script non supportano i tipi booleani, oggetto o matrice. Questi valori causano l'esito negativo dei runbook. 

Se è necessario un altro tipo di oggetto, è possibile eseguirne il cast in un altro tipo con la propria logica nel runbook.

Oltre ai parametri del runbook standard, viene fornito il `SoftwareUpdateConfigurationRunContext` parametro (tipo di stringa JSON). Se si definisce il parametro nel runbook pre-script o post-script, questo viene passato automaticamente dalla distribuzione dell'aggiornamento. Il parametro contiene informazioni sulla distribuzione dell'aggiornamento, ovvero un sottoinsieme di informazioni restituite [dall'API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). Le sezioni seguenti definiscono le proprietà associate.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Proprietà SoftwareUpdateConfigurationRunContext

|Proprietà  |Descrizione  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nome della configurazione dell'aggiornamento software.        |
|SoftwareUpdateConfigurationRunId     | ID univoco per l'esecuzione.        |
|SoftwareUpdateConfigurationSettings     | Raccolta di proprietà relative alla configurazione dell'aggiornamento software.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Sistemi operativi di destinazione per la distribuzione dell'aggiornamento.         |
|SoftwareUpdateConfigurationSettings.duration     | La durata massima della distribuzione `PT[n]H[n]M[n]S` dell'aggiornamento viene eseguita in base a ISO8601. chiamata anche finestra di manutenzione.          |
|SoftwareUpdateConfigurationSettings.Windows     | Raccolta di proprietà relative ai computer Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Elenco di KB esclusi dalla distribuzione dell'aggiornamento.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Aggiornare le classificazioni selezionate per la distribuzione degli aggiornamenti.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Aggiornare le impostazioni per la distribuzione degli aggiornamenti.        |
|azureVirtualMachines     | Un elenco di resourceId per le macchine virtuali di Azure nella distribuzione dell'aggiornamento.        |
|nonAzureComputerNames|Elenco degli FQDN dei computer non Azure nella distribuzione dell'aggiornamento.|

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

Un esempio completo con tutte le proprietà è disponibile in: [Get software update configuration by name](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> L'oggetto `SoftwareUpdateConfigurationRunContext` può contenere voci duplicate per le macchine. Ciò può causare l'esecuzione di pre-script e post-script più volte sullo stesso computer. Per risolvere questo problema, utilizzare `Sort-Object -Unique` per selezionare solo nomi di macchina virtuale univoci.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Utilizzo di un pre-script o post-script in una distribuzioneUsing a pre-script or post-script in a deployment

Per utilizzare un pre-script o post-script in una distribuzione di aggiornamento, iniziare creando una distribuzione di aggiornamento. Selezionare **Pre-script - Post-script**. Verrà visualizzata la pagina **Selezionare i pre-script e i post-script**.

![Selezionare gli script](./media/pre-post-scripts/select-scripts.png)

Selezionare lo script che si desidera utilizzare. In questo esempio viene usato il runbook **UpdateManagement-TurnOnVms.In** this example, we use the UpdateManagement-TurnOnVms runbook. Quando si seleziona il runbook, viene visualizzata la pagina **Configura script.** Selezionare **Pre-Script**, quindi **scegliere OK**.

Ripetere la procedura per lo script **UpdateManagement-TurnOffVms**. Ma quando si sceglie il **tipo di script**, selezionare **Post-Script**.

La sezione **Elementi selezionati** ora mostra entrambi gli script selezionati. Uno è un pre-script e l'altro è un post-script:

![Elementi selezionati](./media/pre-post-scripts/selected-items.png)

Completare la configurazione della distribuzione degli aggiornamenti.

Al termine della distribuzione degli aggiornamenti, è possibile passare a **Distribuzioni** di aggiornamento per visualizzare i risultati. Come si può vedere, lo stato viene fornito per il pre-script e post-script:

![Risultati aggiornamento](./media/pre-post-scripts/update-results.png)

Selezionando l'esecuzione della distribuzione degli aggiornamenti, vengono visualizzati ulteriori dettagli relativi a pre-script e post-script. Viene fornito un collegamento all'origine dello script al momento dell'esecuzione.

![Risultati dell'esecuzione della distribuzione](./media/pre-post-scripts/deployment-run.png)

nel copione.

## <a name="stopping-a-deployment"></a>Arresto di una distribuzione

Se si desidera arrestare una distribuzione basata su un pre-script, è necessario [generare](automation-runbook-execution.md#throw) un'eccezione. In caso contrario, la distribuzione e il post-script verranno comunque eseguiti. Nel frammento di codice seguente viene illustrato come generare un'eccezione.

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



## <a name="interacting-with-machines"></a>Interazione con le macchine

I pre-script e le post-attività vengono eseguiti come runbook nell'account di automazione e non direttamente nei computer della distribuzione. Le attività preliminari e le post-attività vengono eseguite anche nel contesto di Azure e non hanno accesso a computer non Azure.Pre-tasks and post-tasks also run in the Azure context and don't have access to non-Azure machines. Le sezioni seguenti illustrano come interagire direttamente con i computer, siano essi macchine virtuali di Azure o computer non Azure.The following sections show how you can interact with the machines directly, whether they'm Azure VMs or non-Azure machines.

### <a name="interact-with-azure-machines"></a>Interagire con le macchine di AzureInteract with Azure machines

Le pre-attività e le post-attività vengono eseguite come runbook e non vengono eseguite in modo nativo nelle macchine virtuali di Azure nella distribuzione. Per interagire con le macchine virtuali di Azure, è necessario disporre degli elementi seguenti:To interact with your Azure VMs, you must have the following items:

* Un account RunAs
* Un runbook che si desidera eseguire

Per interagire con i computer di Azure, è necessario usare il cmdlet Invoke-AzVMRunCommand per interagire con le macchine virtuali di Azure.To interact with Azure machines, you should use the [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) cmdlet to interact with your Azure VMs. Per un esempio di come eseguire questa operazione, vedere l'esempio di runbook [Update Management – run script con il comando Esegui](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interact-with-non-azure-machines"></a>Interagire con macchine non AzureInteract with non-Azure machines

Le attività preliminari e le post-attività vengono eseguite nel contesto di Azure e non hanno accesso a computer non Azure.Pre-tasks and post-tasks run in the Azure context and don't have access to non-Azure machines. Per interagire con i computer non Azure, è necessario disporre degli elementi seguenti:To interact with the non-Azure machines, you must have the following items:

* Un account RunAs
* Ruolo di lavoro ibrido per runbook installato nel computer
* Un runbook da eseguire in locale
* Un runbook padre

Per interagire con computer non Azure, un runbook padre viene eseguito nel contesto di Azure.To interact with non-Azure machines, a parent runbook is run in the Azure context. Questo runbook chiama un runbook figlio con il cmdlet [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) È necessario specificare il `RunOn` parametro e specificare il nome del ruolo di lavoro ibrido per runbook in cui eseguire lo script. Vedere l'esempio di gestione degli aggiornamenti del [runbook: eseguire lo script in locale](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="aborting-patch-deployment"></a>Interruzione della distribuzione delle patch

Se il pre-script restituisce un errore, è possibile interrompere la distribuzione. A tale scopo, è necessario [generare](/powershell/module/microsoft.powershell.core/about/about_throw) un errore nello script per qualsiasi logica che costituirebbe un errore.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Esempi

Esempi per pre-script e post-script sono disponibili nella [raccolta Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) e PowerShell Gallery oppure è possibile importarli tramite il portale di Azure.Samples for pre-scripts and post-scripts can be found in the Script Center Gallery and the PowerShell Gallery , oppure è possibile importarli tramite il portale di Azure.Samples for pre-scripts and post-scripts can be found in the Script Center Gallery and the [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), or you can import them through the Azure portal. A tale scopo, nell'account di automazione, in **Automazione processo**selezionare **Runbooks Gallery**. Usare **Update Management** come filtro.

![Elenco della raccolta](./media/pre-post-scripts/runbook-gallery.png)

In alternativa, è possibile cercarli in base al nome dello script, come illustrato nell'elenco seguente:

* Update Management - Turn On VMs
* Update Management - Turn Off VMs
* Update Management - Run Script Locally
* Update Management - Template for Pre/Post Scripts
* Update Management - Run Script with Run Command

> [!IMPORTANT]
> Dopo aver importato i runbook, è necessario pubblicarli prima di poterli utilizzare. A tale scopo, individuare il runbook nell'account di automazione, selezionare **Modifica**e quindi **Pubblica**.

Gli esempi sono tutti basati sul modello di base definito nell'esempio seguente. Questo modello può essere utilizzato per creare il proprio runbook da utilizzare con pre-script e post-script. È inclusa la logica necessaria per `SoftwareUpdateConfigurationRunContext` l'autenticazione con Azure e la gestione del parametro.

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
> Per i runbook di `Add-AzAccount` PowerShell non grafici e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile utilizzare questi cmdlet [oppure aggiornare i moduli](automation-update-azure-modules.md) nell'account di automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli anche se è stato appena creato un nuovo account di automazione.

## <a name="next-steps"></a>Passaggi successivi

Passare all'esercitazione seguente per informazioni su come gestire gli aggiornamenti per le macchine virtuali Windows:Go on the following tutorial to learn how to manage updates for your Windows virtual machines:

> [!div class="nextstepaction"]
> [Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure](automation-tutorial-update-management.md)