---
title: Configurare pre-script e post-script nella distribuzione di Gestione aggiornamenti in Azure (anteprima)
description: Questo articolo descrive come configurare e gestire pre-script e post-script per le distribuzioni di aggiornamento
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edbb953111fb4589539369bd9b2519b48b9b70eb
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121312"
---
# <a name="manage-pre-and-post-scripts-preview"></a>Gestire i pre-script e i post-script (anteprima)

I pre-script e i post-script consentono di eseguire runbook di PowerShell nell'account di automazione prima (attività preliminare) e dopo (attività successiva) una distribuzione di aggiornamento. I pre-script e i post-script vengono eseguiti nel contesto di Azure e non in locale. I pre-script sono eseguiti all'inizio della distribuzione di aggiornamento. Registrare gli script eseguiti al termine della distribuzione e dopo eventuali riavvii che sono configurati.

## <a name="runbook-requirements"></a>Requisiti dei runbook

Per poter usare un runbook come pre-script o post-script, il runbook deve essere importato nell'account di automazione e pubblicato. Per altre informazioni su questo processo, vedere [Pubblicazione di un runbook](automation-creating-importing-runbook.md#publishing-a-runbook).

## <a name="using-a-prepost-script"></a>Uso di un pre/post-script

Per usare un pre-script e un post-script in una distribuzione di aggiornamento, iniziare creando una distribuzione di aggiornamento. Selezionare **Pre-script e post-script (anteprima)**. Si aprirà la pagina **Selezionare i pre-script e i post-script**.  

![Selezionare gli script](./media/pre-post-scripts/select-scripts.png)

Selezionare lo script da usare, in questo esempio, è stato usato il runbook **UpdateManagement-TurnOnVms**. Quando si seleziona il runbook si apre la pagina **Configura script**. Specificare i valori per i parametri e scegliere **Pre-script**. Fare clic su **OK** al termine dell'operazione.

![Configura script](./media/pre-post-scripts/configure-script.png)

Ripetere la procedura per lo script **UpdateManagement-TurnOffVms**. Al momento di selezionare il **Tipo di script**, scegliere però **Post-script**.

La sezione **Elementi selezionati** mostra ora entrambi gli script selezionati, uno è un pre-script, l'altro è un post-script.

![Elementi selezionati](./media/pre-post-scripts/selected-items.png)

Completare la configurazione della distribuzione di aggiornamento.

Una volta terminata la distribuzione di aggiornamento, è possibile passare a **Distribuzioni di aggiornamento** per visualizzare i risultati. Come si può notare, è indicato lo stato del pre-script e del post-script.

![Risultati aggiornamento](./media/pre-post-scripts/update-results.png)

Facendo clic sull'esecuzione di distribuzione aggiornamenti vengono forniti dettagli aggiuntivi per gli script pre e post. Viene fornito un collegamento all'origine dello script al momento dell'esecuzione.

![Risultati dell'esecuzione della distribuzione](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Passaggio dei parametri

Quando si configurano i pre-script e i post-script è possibile passare i parametri come se si stesse pianificando un runbook. I parametri vengono definiti al momento della creazione della distribuzione di aggiornamento. Oltre ai parametri standard del runbook viene fornito un parametro aggiuntivo, **SoftwareUpdateConfigurationRunContext**. Questo parametro è una stringa JSON e, se viene definito nello script pre o post, viene automaticamente passato alla distribuzione di aggiornamento. Il parametro contiene informazioni sulla distribuzione di aggiornamento che rappresenta un subset delle informazioni restituite dall'API [SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). La tabella seguente illustra le proprietà fornite nella variabile:

### <a name="softwareupdateconfigurationruncontext-properties"></a>Proprietà SoftwareUpdateConfigurationRunContext

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|SoftwareUpdateConfigurationName     | Nome della configurazione di aggiornamento software        |
|SoftwareUpdateConfigurationRunId     | ID univoco per l'esecuzione.        |
|SoftwareUpdateConfigurationSettings     | Raccolta di proprietà correlate alla configurazione di aggiornamento software         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Sistemi operativi di destinazione per la distribuzione di aggiornamento         |
|SoftwareUpdateConfigurationSettings.duration     | Durata massima dell'esecuzione di distribuzione aggiornamenti in formato `PT[n]H[n]M[n]S` ISO8601, denominata anche "finestra di manutenzione"          |
|SoftwareUpdateConfigurationSettings.Windows     | Raccolta di proprietà relative ai computer Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Elenco di Knowledge Base escluse dalla distribuzione di aggiornamento        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classificazioni degli aggiornamenti selezionati per la distribuzione di aggiornamento        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Impostazioni di riavvio per la distribuzione di aggiornamento        |
|azureVirtualMachines     | Elenco di ResourceID per le macchine virtuali di Azure nella distribuzione di aggiornamento        |
|nonAzureComputerNames|Elenco di FQDN dei computer non Azure nella distribuzione di aggiornamento|

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

Un esempio completo con tutte le proprietà è disponibile in: [Software Update Configurations - Get By Name](/rest/api/automation/softwareupdateconfigurations/getbyname#examples) (Configurazioni degli aggiornamenti software - Recupero per nome)

## <a name="samples"></a>Esempi

Esempi di pre-script e post-script sono disponibili nella [raccolta Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) o possono essere importati tramite il portale di Azure. Per importarli tramite il portale, nell'account di automazione selezionare **Raccolta di runbook** in **Automazione processi**. Usare **Update Management** come filtro.

![Elenco della raccolta](./media/pre-post-scripts/runbook-gallery.png)

In alternativa, è possibile eseguire una ricerca in base al nome dello script, come indicato nell'elenco seguente:

* Update Management - Turn On VMs
* Update Management - Turn Off VMs
* Update Management - Run Script Locally
* Update Management - Template for Pre/Post Scripts
* Update Management - Run Script with Run Command

> [!IMPORTANT]
> Dopo aver importato i runbook, è necessario **pubblicarli** prima di poterli usare. A questo scopo trovare il runbook nell'account di automazione, selezionare **Modifica** e fare clic su **Pubblica**.

Gli esempi sono tutti basati sul modello di base definito nell'esempio seguente. Questo modello può essere usato per creare un proprio runbook da usare con pre-script e post-script. Sono inclusi la logica necessaria per l'autenticazione con Azure e la gestione del parametro `SoftwareUpdateConfigurationRunContext`.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
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
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines 
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

## <a name="interacting-with-non-azure-machines"></a>Interazione con computer non Azure

Le attività preliminari e successive vengono eseguite nel contesto di Azure e non hanno accesso ai computer non Azure. Per interagire con i computer non Azure è necessario quanto segue:

* Un account RunAs
* Ruolo di lavoro ibrido per runbook installato nel computer
* Un runbook da eseguire in locale
* Runbook padre

Per interagire con i computer non Azure, un runbook padre viene eseguito nel contesto di Azure. Questo runbook chiama un runbook figlio con il cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). È necessario specificare il `-RunOn` parametro e specificare il nome del ruolo di lavoro ibrido per runbook in cui eseguire lo script.

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="known-issues"></a>Problemi noti

* Quando si usano pre-script o post-script, non è possibile passare oggetti o matrici ai parametri. Il runbook avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

Continuare con l'esercitazione sulla gestione degli aggiornamenti per le macchine virtuali Windows.

> [!div class="nextstepaction"]
> [Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure](automation-tutorial-update-management.md)
