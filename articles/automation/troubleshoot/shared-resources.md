---
title: Risolvere i problemi relativi alle risorse condivise di Automazione di Azure
description: Questo articolo mostra come risolvere i problemi relativi alle risorse condivise di Automazione di Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5b87a98ed38e3af315789adffc11824f2522b802
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680876"
---
# <a name="troubleshoot-shared-resource-issues"></a>Risolvere i problemi relativi alle risorse condivise

Questo articolo illustra i problemi che possono verificarsi quando si usano [risorse condivise](../automation-intro.md#shared-resources) in Automazione di Azure.

## <a name="modules"></a>Moduli

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: un modulo si blocca durante l'importazione

#### <a name="issue"></a>Problema

Durante l'importazione o l'aggiornamento dei moduli in Automazione di Azure, un modulo rimane bloccato nello stato *Importazione*.

#### <a name="cause"></a>Causa

Poiché l'importazione dei moduli di PowerShell è un processo complesso e in più passaggi, un modulo potrebbe non essere importato correttamente e può rimanere bloccato in uno stato temporaneo. Per altre informazioni su questo processo, vedere [Importazione di un modulo di PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema è necessario rimuovere il modulo bloccato tramite il cmdlet [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0). È quindi possibile ritentare l'importazione del modulo.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: I moduli AzureRM rimangono bloccati durante l'importazione dopo un tentativo di aggiornamento

#### <a name="issue"></a>Problema

Un banner con il messaggio seguente rimane nell'account dopo aver tentato di aggiornare i moduli di AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Si è verificato un problema noto relativo all'aggiornamento dei moduli AzureRM in un account di Automazione. In particolare, il problema si verifica se i moduli si trovano in un gruppo di risorse con un nome numerico che inizia con 0.

#### <a name="resolution"></a>Risoluzione

Per aggiornare i moduli di AzureRM nell'account di Automazione, l'account deve trovarsi in un gruppo di risorse con un nome alfanumerico. I gruppi di risorse con nomi numerici che iniziano con 0 non sono in grado di aggiornare i moduli di AzureRM in questo momento.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: l'importazione del modulo non riesce o non è possibile eseguire i cmdlet dopo l'importazione

#### <a name="issue"></a>Problema

L'importazione di un modulo ha esito negativo oppure riesce ma non viene estratto alcun cmdlet.

#### <a name="cause"></a>Causa

Di seguito sono elencati alcuni motivi comuni che possono causare un'importazione errata di un modulo in Automazione di Azure:

* La struttura non corrisponde a quella necessaria per Automazione.
* Il modulo dipende da un altro modulo che non è stato distribuito nel proprio account di automazione.
* Le dipendenze del modulo non si trovano nella cartella.
* Il cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) viene usato per caricare il modulo e non è stato specificato il percorso di archiviazione completo oppure il modulo non è stato caricato con un URL accessibile pubblicamente.

#### <a name="resolution"></a>Risoluzione

Usare una di queste soluzioni per risolvere il problema:

* Assicurarsi che il modulo rispetti il formato: ModuleName.sip -> Nome modulo o numero versione -> (ModuleName.psm1, ModuleName.psd1).
* Aprire il file con estensione **.psd1** e vedere se il modulo include dipendenze. In caso affermativo, caricare i moduli nell'account di automazione.
* Assicurarsi che le eventuali file **.dll** a cui viene fatto riferimento siano presenti nella cartella del modulo.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: L'esecuzione di Update-AzureModule.ps1 viene sospesa durante l'aggiornamento dei moduli

#### <a name="issue"></a>Problema

Quando si usa il runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) per aggiornare i moduli di Azure, il processo di aggiornamento dei moduli viene sospeso.

#### <a name="cause"></a>Causa

Per questo runbook, l'impostazione predefinita per determinare il numero di moduli aggiornati contemporaneamente è 10. Quando si aggiornano troppi moduli nello stesso momento, il processo di aggiornamento è soggetto a errori.

#### <a name="resolution"></a>Risoluzione

Non è usuale che nello stesso account di automazione siano necessari tutti i moduli AzureRM o Az. È necessario importare solo i moduli specifici necessari.

> [!NOTE]
> Evitare di importare l'intero modulo `Az.Automation` o `AzureRM.Automation`, che importa tutti i moduli contenuti.

Se il processo di aggiornamento viene sospeso, è necessario aggiungere il parametro `SimultaneousModuleImportJobCount` allo script **Update-AzureModules.ps1** e fornire un valore inferiore a quello predefinito, ossia 10. Se si implementa questa logica, provare a iniziare con un valore pari a 3 o 5. `SimultaneousModuleImportJobCount` è un parametro del runbook di sistema **Update-AutomationAzureModulesForAccount** usato per aggiornare i moduli di Azure. Se si effettua questa regolazione, il processo di aggiornamento viene eseguito più a lungo, ma ha una maggiore probabilità di completamento. L'esempio seguente mostra il parametro e la posizione in cui inserirlo nel runbook:

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

Quando si prova a creare o aggiornare un account RunAs viene visualizzato un errore simile al seguente:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Non si dispone delle autorizzazioni necessarie per creare o aggiornare l'account RunAs oppure la risorsa è bloccata a un livello di gruppo di risorse.

#### <a name="resolution"></a>Risoluzione

Per creare o aggiornare un account RunAs è necessario disporre delle [autorizzazioni](../manage-runas-account.md#permissions) appropriate per le varie risorse usate dall'account RunAs. 

Se il problema è dovuto a un blocco, verificare che sia possibile rimuoverlo. Passare quindi alla risorsa bloccata nel portale di Azure, fare clic con il pulsante destro del mouse sul blocco e selezionare **Elimina**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: Viene visualizzato l'errore "Impossibile trovare un punto di ingresso con nome 'GetPerAdapterInfo' nella DLL 'iplpapi.dll'" durante l'esecuzione di un runbook

#### <a name="issue"></a>Problema

Quando si esegue un runbook, si riceve l'eccezione seguente:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Questo errore è probabilmente causato da una configurazione non corretta dell'[account RunAs](../manage-runas-account.md).

#### <a name="resolution"></a>Risoluzione

Verificare che l'account RunAs sia configurato correttamente. Quindi verificare di disporre del codice appropriato nel runbook per l'autenticazione con Azure. L'esempio seguente illustra un frammento di codice per l'autenticazione in Azure in un runbook usando un account RunAs.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Passaggi successivi

Se questo articolo non risolve il problema, usare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottenere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.

