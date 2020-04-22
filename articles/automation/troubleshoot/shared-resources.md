---
title: Risolvere i problemi relativi alle risorse condivise in Automazione di AzureTroubleshoot shared resources in Azure Automation
description: Informazioni su come risolvere i problemi relativi alle risorse condivise di Automazione di Azure.Learn how to troubleshoot and resolve issues with Azure Automation shared resources.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733580"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Risolvere i problemi relativi alle risorse condivise in Automazione di AzureTroubleshoot shared resources in Azure Automation

Questo articolo illustra le soluzioni per i problemi che possono essere eseguiti quando si usano risorse condivise in Automazione di Azure.This article discusses solutions for issues that you might run across when using [shared resources](../automation-intro.md#shared-resources) in Azure Automation.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="modules"></a>Moduli

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: un modulo è bloccato durante l'importazione

#### <a name="issue"></a>Problema

Un modulo è bloccato nello stato Importazione durante l'importazione o l'aggiornamento dei moduli di Automazione di Azure.A module is stuck in the Importing state when you are importing or updating your Azure Automation modules.

#### <a name="cause"></a>Causa

Poiché l'importazione di moduli di PowerShell è un processo multifase complesso, un modulo potrebbe non essere importato correttamente e può essere bloccato in uno stato temporaneo. Per altre informazioni sul processo di importazione, vedere [Importazione di un modulo di PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema, è necessario rimuovere il modulo che è bloccato nello stato di importazione utilizzando il cmdlet [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) . È quindi possibile ritentare l'importazione del modulo.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: i moduli di AzureRM vengono bloccati durante l'importazione dopo un tentativo di aggiornamentoScenario: AzureRM modules are stuck during import after an update attempt

#### <a name="issue"></a>Problema

Un banner con il messaggio seguente rimane nell'account dopo aver tentato di aggiornare i moduli di AzureRM:A banner with the following message stays in your account after trying to update your AzureRM modules:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Si è verificato un problema noto con l'aggiornamento dei moduli di AzureRM in un account di automazione che si trova in un gruppo di risorse con un nome numerico che inizia con 0.There is a known issue with updating the AzureRM modules in an Automation account that's in a resource group with a numeric name starting with 0.

#### <a name="resolution"></a>Risoluzione

Per aggiornare i moduli di AzureRM nell'account di automazione, l'account deve trovarsi in un gruppo di risorse con un nome alfanumerico. I gruppi di risorse con nomi numerici che iniziano con 0 non sono in grado di aggiornare i moduli di AzureRM in questo momento.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: L'importazione del modulo non riesce o i cmdlet non possono essere eseguiti dopo l'importazione

#### <a name="issue"></a>Problema

Un modulo non viene importato o viene importato correttamente, ma non viene estratto alcun cmdlet.

#### <a name="cause"></a>Causa

Di seguito sono elencati alcuni motivi comuni che possono causare un'importazione errata di un modulo in Automazione di Azure:

* La struttura non corrisponde alla struttura necessaria per l'automazione.
* Il modulo dipende da un altro modulo che non è stato distribuito nel proprio account di automazione.
* Le dipendenze del modulo non si trovano nella cartella.
* Il cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) viene utilizzato per caricare il modulo e non è stato assegnato il percorso di archiviazione completo o non è stato caricato il modulo utilizzando un URL accessibile pubblicamente.

#### <a name="resolution"></a>Risoluzione

Utilizzare una di queste soluzioni per risolvere il problema.

* Assicurarsi che il modulo segua il formato: NomeModulo.zip -> NomeModulo o Numero versione -> (ModuleName.psm1, ModuleName.psd1).
* Aprire il file **con estensione psd1** e verificare se il modulo ha dipendenze. In caso affermativo, caricare i moduli nell'account di automazione.
* Assicurarsi che tutti i file **DLL** a cui si fa riferimento siano presenti nella cartella del modulo.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: update-AzureModule.ps1 viene sospeso durante l'aggiornamento dei moduliScenario: Update-AzureModule.ps1 suspends when updating modules

#### <a name="issue"></a>Problema

Quando si usa il runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) per aggiornare i moduli di Azure, il processo di aggiornamento dei moduli viene sospeso.

#### <a name="cause"></a>Causa

L'impostazione predefinita per determinare il numero di moduli aggiornati contemporaneamente è 10 quando si usa **Update-AzureModule.ps1.** Quando si aggiornano troppi moduli nello stesso momento, il processo di aggiornamento è soggetto a errori.

#### <a name="resolution"></a>Risoluzione

Non è comune che tutti i moduli di AzureRM o Az siano necessari nello stesso account di automazione. Si consiglia di importare solo i moduli specifici necessari.

> [!NOTE]
> Evitare di `Az.Automation` `AzureRM.Automation` importare l'intero modulo o l'intero modulo, che importa tutti i moduli contenuti.

Se il processo di aggiornamento `SimultaneousModuleImportJobCount` viene sospeso, aggiungere il parametro allo script Update-AzureModules.ps1 e specificare un valore inferiore a quello predefinito di 10.If the update process suspends, add the parameter to the **Update-AzureModules.ps1** script and provide a lower value than the default of 10. Se si implementa questa logica, è consigliabile iniziare con un valore pari a 3 o 5.If you implement this logic, it's recommended to start with a value of 3 or 5. `SimultaneousModuleImportJobCount`è un parametro del runbook di sistema **Update-AutomationAzureModulesForAccount** usato per aggiornare i moduli di Azure. Se si apporta questa regolazione, il processo di aggiornamento viene eseguito più a lungo, ma ha maggiori probabilità di essere completato. L'esempio seguente mostra il parametro e la posizione in cui inserirlo nel runbook:

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>Account RunAs

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>Scenario: non è possibile creare o aggiornare un account RunAs

#### <a name="issue"></a>Problema

Quando si prova a creare o aggiornare un account RunAs viene visualizzato un messaggio di errore simile al seguente:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Non si dispone delle autorizzazioni necessarie per creare o aggiornare l'account RunAs oppure la risorsa è bloccata a un livello di gruppo di risorse.

#### <a name="resolution"></a>Risoluzione

Per creare o aggiornare un account RunAs, è necessario disporre delle [autorizzazioni](../manage-runas-account.md#permissions) appropriate per le varie risorse utilizzate dall'account RunAs. 

Se il problema è dovuto a un blocco, verificare che il blocco possa essere rimosso. Passare quindi alla risorsa bloccata nel portale di Azure, fare clic con il pulsante destro del mouse sul blocco e **scegliere Elimina**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: viene visualizzato l'errore "Impossibile trovare un punto di ingresso denominato 'GetPerAdapterInfo' nella DLL 'iplpapi.dll'" durante l'esecuzione di un runbook

#### <a name="issue"></a>Problema

Quando si esegue un runbook si riceve la seguente eccezione:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Questo errore è probabilmente causato da un [account RunAs](../manage-runas-account.md)configurato in modo non corretto.

#### <a name="resolution"></a>Risoluzione

Assicurarsi che l'account RunAs sia configurato correttamente. Verificare quindi di disporre del codice corretto nel runbook per eseguire l'autenticazione con Azure.Then verify that you have the proper code in your runbook to authenticate with Azure. L'esempio seguente mostra un frammento di codice per l'autenticazione in Azure in un runbook usando un account RunAs.The following example shows a snippet of code to authenticate to Azure in a runbook using a RunAs Account.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Passaggi successivi

Se non vedi il problema sopra o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
