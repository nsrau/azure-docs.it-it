---
title: Configurare pre e post-script per la distribuzione di Gestione aggiornamenti in Azure
description: Questo articolo descrive come configurare e gestire gli script preliminari e i post-script per le distribuzioni degli aggiornamenti.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a5d68b64fc24577621c82be62f833c356e8fb9c2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850245"
---
# <a name="manage-pre-and-post-scripts"></a>Gestire pre e post-script

Gli script di pre e post-script consentono di eseguire PowerShell manuali operativi nell'account di automazione di Azure prima (pre-attività) e dopo (post-attività) una distribuzione degli aggiornamenti. Pre e post-script vengono eseguiti nel contesto di Azure, non in locale. Gli script preliminari vengono eseguiti all'inizio della distribuzione degli aggiornamenti. I post-script vengono eseguiti al termine della distribuzione e dopo i riavvii configurati.

## <a name="runbook-requirements"></a>Requisiti dei runbook

Perché un Runbook venga usato come pre o post-script, il Runbook deve essere importato nell'account di automazione e pubblicato. Per ulteriori informazioni su questo processo, vedere la pagina relativa alla [pubblicazione di un Runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Utilizzo di uno script di pre-script o di post-script

Per usare uno script di pre-script o un post-script in una distribuzione di aggiornamenti, iniziare creando una distribuzione degli aggiornamenti. Selezionare **pre-script + post-script**. Verrà visualizzata la pagina **Selezionare i pre-script e i post-script**.

![Selezionare gli script](./media/pre-post-scripts/select-scripts.png)

Selezionare lo script che si desidera utilizzare. In questo esempio viene usato **UpdateManagement-TurnOnVms** Runbook. Quando si seleziona Runbook, viene visualizzata la pagina **Configura script** . Selezionare **pre-script**, quindi fare clic su **OK**.

Ripetere la procedura per lo script **UpdateManagement-TurnOffVms**. Tuttavia, quando si sceglie il **tipo di script**, selezionare **post-script**.

Nella sezione **elementi selezionati** sono ora visualizzati entrambi gli script selezionati. Uno è uno script di pre-script e l'altro è uno script di post-script:

![Elementi selezionati](./media/pre-post-scripts/selected-items.png)

Completare la configurazione della distribuzione degli aggiornamenti.

Al termine della distribuzione degli aggiornamenti, è possibile passare a **distribuzioni di aggiornamento** per visualizzare i risultati. Come si può notare, lo stato viene fornito per il pre-script e il post-script:

![Risultati aggiornamento](./media/pre-post-scripts/update-results.png)

Selezionando l'esecuzione della distribuzione degli aggiornamenti, vengono visualizzati dettagli aggiuntivi sugli script pre e post-script. Viene fornito un collegamento all'origine dello script al momento dell'esecuzione.

![Risultati dell'esecuzione della distribuzione](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Passaggio dei parametri

Quando si configurano pre e post-script, è possibile passare i parametri proprio come la pianificazione di un Runbook. I parametri vengono definiti al momento della creazione della distribuzione di aggiornamento. Pre e post-script supportano i tipi seguenti:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Se è necessario un altro tipo di oggetto, è possibile eseguirne il cast in un altro tipo con la propria logica nel runbook.

Oltre ai parametri Runbook standard, viene fornito un altro parametro: **SoftwareUpdateConfigurationRunContext**

Questo parametro è una stringa JSON e, se si definisce il parametro nello script pre o post-script, viene passato automaticamente dalla distribuzione degli aggiornamenti. Il parametro contiene informazioni sulla distribuzione degli aggiornamenti, ovvero un subset di informazioni restituite dall' [API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). 

Nella tabella seguente vengono illustrate le proprietà fornite nella variabile.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Proprietà SoftwareUpdateConfigurationRunContext

|Proprietà  |Description  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nome della configurazione di aggiornamento software.        |
|SoftwareUpdateConfigurationRunId     | ID univoco per l'esecuzione.        |
|SoftwareUpdateConfigurationSettings     | Raccolta di proprietà correlate alla configurazione dell'aggiornamento software.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Sistemi operativi destinati alla distribuzione degli aggiornamenti.         |
|SoftwareUpdateConfigurationSettings.duration     | La durata massima della distribuzione degli aggiornamenti viene eseguita `PT[n]H[n]M[n]S` come per ogni ISO8601; detto anche *finestra di manutenzione*.          |
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
> L'oggetto `SoftwareUpdateConfigurationRunContext` può contenere voci duplicate per i computer. Questo può causare l'esecuzione più volte di pre e post-script nello stesso computer. Per ovviare a questo comportamento, usare `Sort-Object -Unique` per selezionare solo nomi di VM univoci nello script.


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

## <a name="samples"></a>Esempi

Gli esempi di pre e post-script sono disponibili nella [raccolta di script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) e nel [PowerShell Gallery](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)oppure è possibile importarli tramite la portale di Azure. A tale scopo, nell'account di automazione, in **automazione processi**selezionare **raccolta manuali operativi**. Usare **Update Management** come filtro.

![Elenco della raccolta](./media/pre-post-scripts/runbook-gallery.png)

In alternativa, è possibile cercarli in base al nome dello script, come illustrato nell'elenco seguente:

* Update Management - Turn On VMs
* Update Management - Turn Off VMs
* Update Management - Run Script Locally
* Update Management - Template for Pre/Post Scripts
* Update Management - Run Script with Run Command

> [!IMPORTANT]
> Dopo aver importato il manuali operativi, è necessario pubblicarlo prima di poterlo usare. A tale scopo, trovare il Runbook nell'account di automazione, selezionare **modifica**e quindi selezionare **pubblica**.

Gli esempi sono tutti basati sul modello di base definito nell'esempio seguente. Questo modello può essere usato per creare Runbook personalizzati da usare con pre e post-script. Viene inclusa la logica necessaria per l'autenticazione con Azure e la gestione del parametro `SoftwareUpdateConfigurationRunContext`.

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

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

## <a name="interacting-with-machines"></a>Interazione con i computer

Pre e post-attività vengono eseguite come Runbook nell'account di automazione e non direttamente nei computer nella distribuzione. Pre e post-Tasks vengono eseguiti anche nel contesto di Azure e non hanno accesso a computer non Azure. Le sezioni seguenti illustrano come è possibile interagire direttamente con i computer, che si tratti di macchine virtuali di Azure o computer non Azure.

### <a name="interacting-with-azure-machines"></a>Interazione con macchine virtuali di Azure

Le attività preliminari e successive vengono eseguite come manuali operativi e non vengono eseguite in modo nativo nelle macchine virtuali di Azure nella distribuzione. Per interagire con le macchine virtuali di Azure, è necessario disporre degli elementi seguenti:

* Un account RunAs
* Un Runbook che si vuole eseguire

Per interagire con i computer di Azure, è necessario usare il cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) per interagire con le VM di Azure. Per un esempio di come eseguire questa operazione, vedere l'esempio Runbook [Gestione aggiornamenti – Esegui script con il comando Esegui](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interazione con computer non Azure

Pre e post-attività vengono eseguite nel contesto di Azure e non hanno accesso a computer non Azure. Per interagire con i computer non Azure, è necessario disporre degli elementi seguenti:

* Un account RunAs
* Ruolo di lavoro ibrido per runbook installato nel computer
* Un runbook da eseguire in locale
* Un Runbook padre

Per interagire con computer non Azure, un Runbook padre viene eseguito nel contesto di Azure. Questo runbook chiama un runbook figlio con il cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). È necessario specificare il `-RunOn` parametro e specificare il nome del ruolo di lavoro ibrido per runbook in cui eseguire lo script. Per altre informazioni, vedere l'esempio Runbook [Gestione aggiornamenti – eseguire lo script localmente](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Interrompi distribuzione patch

Se lo script di pre-installazione restituisce un errore, è possibile che si desideri interrompere la distribuzione. A tale scopo, è necessario [generare](/powershell/module/microsoft.powershell.core/about/about_throw) un errore nello script per qualsiasi logica che costituirebbe un errore.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Problemi noti

* Non è possibile passare un valore booleano, un oggetto o una matrice ai parametri quando si usano pre e post-script. In caso contrario, il Runbook ha esito negativo. Per un elenco completo dei tipi supportati, vedere [passaggio di parametri](#passing-parameters).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire gli aggiornamenti per le macchine virtuali Windows, vedere l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure](automation-tutorial-update-management.md)

