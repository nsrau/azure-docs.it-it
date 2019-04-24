---
title: Configurare gli script di pre e post sulla distribuzione di gestione degli aggiornamenti in Azure
description: Questo articolo descrive come configurare e gestire pre-script e post-script per le distribuzioni di aggiornamento
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84df04a6d3fbd634524d3819657860c6a3448d65
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499807"
---
# <a name="manage-pre-and-post-scripts"></a>Gestire gli script di pre e post

I pre-script e i post-script consentono di eseguire runbook di PowerShell nell'account di automazione prima (attività preliminare) e dopo (attività successiva) una distribuzione di aggiornamento. I pre-script e i post-script vengono eseguiti nel contesto di Azure e non in locale. Pre-script eseguiti all'inizio della distribuzione degli aggiornamenti. Registrare gli script eseguiti al termine della distribuzione e dopo eventuali riavvii che sono configurati.

## <a name="runbook-requirements"></a>Requisiti dei runbook

Per poter usare un runbook come pre-script o post-script, il runbook deve essere importato nell'account di automazione e pubblicato. Per altre informazioni su questo processo, vedere [Pubblicazione di un runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Uso di un pre/post-script

Per usare un pre-script e un post-script in una distribuzione di aggiornamento, iniziare creando una distribuzione di aggiornamento. Selezionare **gli script di pre e Post script**. Verrà visualizzata la pagina **Selezionare i pre-script e i post-script**.  

![Selezionare gli script](./media/pre-post-scripts/select-scripts.png)

Selezionare lo script da usare, in questo esempio, è stato usato il runbook **UpdateManagement-TurnOnVms**. Quando si seleziona il runbook si apre la pagina **Configura script**. Specificare i valori per i parametri e scegliere **Pre-script**. Fare clic su **OK** al termine dell'operazione.

![Configura script](./media/pre-post-scripts/configure-script.png)

Ripetere la procedura per lo script **UpdateManagement-TurnOffVms**. Al momento di selezionare il **Tipo di script**, scegliere però **Post-script**.

La sezione **Elementi selezionati** mostra ora entrambi gli script selezionati, uno è un pre-script, l'altro è un post-script.

![Elementi selezionati](./media/pre-post-scripts/selected-items.png)

Completare la configurazione della distribuzione di aggiornamento.

Una volta terminata la distribuzione di aggiornamento, è possibile passare a **Distribuzioni di aggiornamento** per visualizzare i risultati. Come si può notare, è indicato lo stato del pre-script e del post-script.

![Risultati aggiornamento](./media/pre-post-scripts/update-results.png)

Facendo clic sull'esecuzione della distribuzione di aggiornamento vengono visualizzati altri dettagli relativi ai pre-script e ai post-script. Viene fornito un collegamento all'origine dello script al momento dell'esecuzione.

![Risultati dell'esecuzione della distribuzione](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Passaggio dei parametri

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

Se è necessario un altro tipo di oggetto, è possibile eseguirne il cast in un altro tipo con la propria logica nel runbook.

Oltre ai parametri standard dei runbook viene passato un parametro aggiuntivo. **SoftwareUpdateConfigurationRunContext**. Questo parametro è una stringa JSON e, se viene definito nello script pre o post, viene automaticamente passato alla distribuzione di aggiornamento. Il parametro contiene informazioni sulla distribuzione di aggiornamento costituite da un subset delle informazioni restituite dall'API [SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). La tabella seguente illustra le proprietà specificate nella variabile:

## <a name="stopping-a-deployment"></a>L'arresto di una distribuzione

Se si vuole arrestare una distribuzione basata su uno script di Pre devi [throw](automation-runbook-execution.md#throw) un'eccezione. Se non si generano un'eccezione, verrà eseguito comunque la distribuzione e lo script di Post. Il [runbook di esempio](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) nella raccolta viene illustrato come è possibile eseguire questa operazione. Di seguito è riportato un frammento di codice da tale runbook.

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

> [!NOTE]
> Il `SoftwareUpdateConfigurationRunContext` oggetto può contenere le voci duplicate per le macchine. Ciò può provocare Pre e post-script da eseguire più volte nello stesso computer. Per risolvere questo problema, usare `Sort-Object -Unique` selezionare solo i nomi di macchina virtuale univoci nello script.

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

Gli esempi sono tutti basati sul modello di base definito nell'esempio seguente. Questo modello può essere usato per creare un proprio runbook da usare con pre-script e post-script. Sono incluse la logica necessaria per l'autenticazione ad Azure e la gestione del parametro `SoftwareUpdateConfigurationRunContext`.

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

## <a name="interacting-with-machines"></a>L'interazione con i computer

Le attività di pre e post-esecuzione come un runbook nell'Account di automazione e non direttamente nei computer nella distribuzione. Le attività di pre e post, inoltre, eseguito nel contesto di Azure e non hanno accesso al computer Non Azure. Le sezioni seguenti illustrano come sia possibile interagire con i computer direttamente se sono una VM di Azure o un computer Non Azure:

### <a name="interacting-with-azure-machines"></a>L'interazione con le macchine di Azure

Le attività di pre e post sono eseguiti come runbook e non vengono eseguiti in modo nativo in macchine virtuali di Azure nella distribuzione. Per interagire con le macchine virtuali di Azure, sono necessari gli elementi seguenti:

* Un account RunAs
* Un runbook da eseguire

Per interagire con le macchine di Azure, è consigliabile usare la [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) cmdlet per interagire con le macchine virtuali di Azure. Per un esempio di come eseguire questa operazione, vedere l'esempio di runbook [gestione degli aggiornamenti - Esegui Script con esecuzione del comando](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interazione con computer non Azure

Le attività preliminari e successive vengono eseguite nel contesto di Azure e non hanno accesso ai computer non Azure. Per interagire con i computer non Azure è necessario quanto segue:

* Un account RunAs
* Ruolo di lavoro ibrido per runbook installato nel computer
* Un runbook da eseguire in locale
* Runbook padre

Per interagire con i computer non Azure, un runbook padre viene eseguito nel contesto di Azure. Questo runbook chiama un runbook figlio con il cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). È necessario specificare il `-RunOn` parametro e specificare il nome del ruolo di lavoro ibrido per runbook in cui eseguire lo script. Per un esempio di come eseguire questa operazione, vedere l'esempio di runbook [gestione degli aggiornamenti - esecuzione locale dello Script](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Interrompere la distribuzione delle patch

Se lo script precedente restituisce un errore, è possibile interrompere la distribuzione. A tale scopo, è necessario [throw](/powershell/module/microsoft.powershell.core/about/about_throw) un errore nello script per qualsiasi logica che costituisce un errore.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```
## <a name="known-issues"></a>Problemi noti

* Quando si usano pre-script o post-script, non è possibile passare oggetti o matrici ai parametri. Il runbook avrà esito negativo.

## <a name="next-steps"></a>Passaggi successivi

Continuare con l'esercitazione sulla gestione degli aggiornamenti per le macchine virtuali Windows.

> [!div class="nextstepaction"]
> [Gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure](automation-tutorial-update-management.md)

