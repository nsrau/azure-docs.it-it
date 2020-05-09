---
title: Gestire pre-script e post-script nella distribuzione di Gestione aggiornamenti in Azure
description: Questo articolo descrive come configurare e gestire gli script preliminari e i post-script per le distribuzioni degli aggiornamenti.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: f55ebb3270fdd97a1fdbbf5a56f9703c08933f9f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855332"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Gestire pre-script e post-script

Gli script di pre e post-script sono manuali operativi per l'esecuzione nell'account di automazione di Azure prima (pre-attività) e dopo (post-attività) una distribuzione degli aggiornamenti. Pre-script e post-script vengono eseguiti nel contesto di Azure, non in locale. Gli script preliminari vengono eseguiti all'inizio della distribuzione degli aggiornamenti. I post-script vengono eseguiti al termine della distribuzione e dopo i riavvii configurati.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="pre-script-and-post-script-requirements"></a>Requisiti di pre-script e post-script

Per usare un Runbook come pre-script o post-script, è necessario importarlo nell'account di automazione e [pubblicare il Runbook](manage-runbooks.md#publish-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parametri di pre-script e post-script

Quando si configurano gli script e i post-script, è possibile passare i parametri proprio come la pianificazione di un Runbook. I parametri vengono definiti al momento della creazione della distribuzione di aggiornamento. Gli script di pre e post-script supportano i tipi seguenti:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

I parametri Runbook di pre-script e post-script non supportano i tipi booleani, di oggetto o di matrice. Questi valori determinano l'esito negativo del manuali operativi. 

Se è necessario un altro tipo di oggetto, è possibile eseguirne il cast in un altro tipo con la propria logica nel runbook.

Oltre ai parametri Runbook standard, viene fornito il `SoftwareUpdateConfigurationRunContext` parametro (Type JSON String). Se si definisce il parametro nel runbook di pre-script o post-script, questo viene passato automaticamente dalla distribuzione degli aggiornamenti. Il parametro contiene informazioni sulla distribuzione degli aggiornamenti, ovvero un subset di informazioni restituite dall' [API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). Le sezioni seguenti definiscono le proprietà associate.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Proprietà SoftwareUpdateConfigurationRunContext

|Proprietà  |Descrizione  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nome della configurazione di aggiornamento software.        |
|SoftwareUpdateConfigurationRunId     | ID univoco per l'esecuzione.        |
|SoftwareUpdateConfigurationSettings     | Raccolta di proprietà correlate alla configurazione dell'aggiornamento software.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Sistemi operativi destinati alla distribuzione degli aggiornamenti.         |
|SoftwareUpdateConfigurationSettings.duration     | Durata massima della distribuzione degli aggiornamenti eseguita come `PT[n]H[n]M[n]S` per ogni ISO8601; detto anche finestra di manutenzione.          |
|SoftwareUpdateConfigurationSettings.Windows     | Raccolta di proprietà correlate ai computer Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Elenco di KB esclusi dalla distribuzione degli aggiornamenti.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classificazioni degli aggiornamenti selezionate per la distribuzione degli aggiornamenti.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Impostazioni di riavvio per la distribuzione degli aggiornamenti.        |
|azureVirtualMachines     | Elenco di resourceIds per le macchine virtuali di Azure nella distribuzione degli aggiornamenti.        |
|nonAzureComputerNames|Elenco di FQDN dei computer non Azure nella distribuzione degli aggiornamenti.|

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

Un esempio completo di tutte le proprietà è disponibile in: [ottenere la configurazione degli aggiornamenti software in base al nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> L' `SoftwareUpdateConfigurationRunContext` oggetto può contenere voci duplicate per i computer. Questo può causare l'esecuzione più volte di script di pre-script e post-script nello stesso computer. Per ovviare a questo comportamento, `Sort-Object -Unique` usare per selezionare solo nomi di VM univoci.

## <a name="using-a-pre-script-or-post-script-in-a-deployment"></a>Utilizzo di uno script di pre-script o post-script in una distribuzione

Per usare uno script di pre-script o un post-script in una distribuzione di aggiornamenti, iniziare creando una distribuzione degli aggiornamenti. Selezionare **pre-script + post-script**. Verrà visualizzata la pagina **Selezionare i pre-script e i post-script**.

![Selezionare gli script](./media/pre-post-scripts/select-scripts.png)

Selezionare lo script che si desidera utilizzare. In questo esempio viene usato **UpdateManagement-TurnOnVms** Runbook. Quando si seleziona Runbook, viene visualizzata la pagina **Configura script** . Selezionare **pre-script**, quindi fare clic su **OK**.

Ripetere la procedura per lo script **UpdateManagement-TurnOffVms**. Tuttavia, quando si sceglie il **tipo di script**, selezionare **post-script**.

Nella sezione **elementi selezionati** sono ora visualizzati entrambi gli script selezionati. Uno è uno script di pre-script e l'altro è uno script di post-script:

![Elementi selezionati](./media/pre-post-scripts/selected-items.png)

Completare la configurazione della distribuzione degli aggiornamenti.

Al termine della distribuzione degli aggiornamenti, è possibile passare a **distribuzioni di aggiornamento** per visualizzare i risultati. Come si può notare, lo stato viene fornito per il pre-script e il post-script:

![Risultati aggiornamento](./media/pre-post-scripts/update-results.png)

Selezionando l'esecuzione della distribuzione degli aggiornamenti, vengono visualizzati altri dettagli sugli script e i post-script. Viene fornito un collegamento all'origine dello script al momento dell'esecuzione.

![Risultati dell'esecuzione della distribuzione](./media/pre-post-scripts/deployment-run.png)

es nello script.

## <a name="stopping-a-deployment"></a>Arresto di una distribuzione

Se si desidera arrestare una distribuzione basata su uno script preliminare, è necessario [generare](automation-runbook-execution.md#throw) un'eccezione. In caso contrario, la distribuzione e il post-script saranno ancora in esecuzione. Nel frammento di codice seguente viene illustrato come generare un'eccezione.

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



## <a name="interacting-with-machines"></a>Interazione con i computer

Gli script pre-e post-attività vengono eseguiti come manuali operativi nell'account di automazione e non direttamente nei computer della distribuzione. Le attività preliminari e successive alle attività vengono eseguite anche nel contesto di Azure e non hanno accesso a computer non Azure. Le sezioni seguenti illustrano come è possibile interagire direttamente con i computer, che si tratti di macchine virtuali di Azure o computer non Azure.

### <a name="interact-with-azure-machines"></a>Interagire con i computer Azure

Le attività preliminari e successive alle attività vengono eseguite come manuali operativi e non vengono eseguite in modo nativo nelle macchine virtuali di Azure nella distribuzione. Per interagire con le macchine virtuali di Azure, è necessario disporre degli elementi seguenti:

* Un account RunAs
* Un Runbook che si vuole eseguire

Per interagire con i computer di Azure, è necessario usare il cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) per interagire con le VM di Azure. Per un esempio di come eseguire questa operazione, vedere l'esempio Runbook [Gestione aggiornamenti – Esegui script con il comando Esegui](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interact-with-non-azure-machines"></a>Interagire con computer non Azure

Pre-attività e post-attività vengono eseguite nel contesto di Azure e non hanno accesso a computer non Azure. Per interagire con i computer non Azure, è necessario disporre degli elementi seguenti:

* Un account RunAs
* Ruolo di lavoro ibrido per runbook installato nel computer
* Un runbook da eseguire in locale
* Un Runbook padre

Per interagire con computer non Azure, un Runbook padre viene eseguito nel contesto di Azure. Questo runbook chiama un Runbook figlio con il cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) . È necessario specificare il `RunOn` parametro e specificare il nome del ruolo di lavoro ibrido per runbook in cui eseguire lo script. Vedere l'esempio di Runbook [Gestione aggiornamenti – eseguire lo script localmente](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="aborting-patch-deployment"></a>Interruzione della distribuzione della patch

Se lo script di pre-installazione restituisce un errore, è possibile che si desideri interrompere la distribuzione. A tale scopo, è necessario [generare](/powershell/module/microsoft.powershell.core/about/about_throw) un errore nello script per qualsiasi logica che costituirebbe un errore.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Esempi

Gli esempi di script di pre-script e post-script sono disponibili nella [raccolta di script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) e nel [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)oppure è possibile importarli tramite il portale di Azure. A tale scopo, nell'account di automazione, in **automazione processi**selezionare **raccolta manuali operativi**. Usare **Update Management** come filtro.

![Elenco della raccolta](./media/pre-post-scripts/runbook-gallery.png)

In alternativa, è possibile cercarli in base al nome dello script, come illustrato nell'elenco seguente:

* Update Management - Turn On VMs
* Update Management - Turn Off VMs
* Update Management - Run Script Locally
* Update Management - Template for Pre/Post Scripts
* Update Management - Run Script with Run Command

> [!IMPORTANT]
> Dopo aver importato il manuali operativi, è necessario pubblicarlo prima di poterlo usare. A tale scopo, trovare il Runbook nell'account di automazione, selezionare **modifica**e quindi selezionare **pubblica**.

Gli esempi sono tutti basati sul modello di base definito nell'esempio seguente. Questo modello può essere usato per creare Runbook personalizzati da usare con gli script di pre-scrittura e post-script. Viene inclusa la logica necessaria per l'autenticazione con Azure e `SoftwareUpdateConfigurationRunContext` la gestione del parametro.

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
> Per manuali operativi di PowerShell non grafici, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire gli aggiornamenti per le macchine virtuali Windows, vedere l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure](automation-tutorial-update-management.md)