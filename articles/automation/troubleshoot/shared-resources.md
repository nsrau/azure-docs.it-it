---
title: Risolvere i problemi relativi alle risorse condivise in automazione di Azure
description: Informazioni su come risolvere i problemi relativi alle risorse condivise di automazione di Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733580"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Risolvere i problemi relativi alle risorse condivise in automazione di Azure

Questo articolo illustra le soluzioni per i problemi che possono verificarsi quando si usano [risorse condivise](../automation-intro.md#shared-resources) in automazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="modules"></a>Moduli

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>Scenario: un modulo è bloccato durante l'importazione

#### <a name="issue"></a>Problema

Un modulo è bloccato nello stato di importazione quando si importano o si aggiornano i moduli di automazione di Azure.

#### <a name="cause"></a>Causa

Poiché l'importazione di moduli di PowerShell è un processo in più passaggi complessi, un modulo potrebbe non essere importato correttamente e può essere bloccato in uno stato temporaneo. Per altre informazioni sul processo di importazione, vedere [importazione di un modulo di PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Soluzione

Per risolvere questo problema, è necessario rimuovere il modulo bloccato nello stato di importazione usando il cmdlet [Remove-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) . È quindi possibile ritentare l'importazione del modulo.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>Scenario: i moduli AzureRM sono bloccati durante l'importazione dopo un tentativo di aggiornamento

#### <a name="issue"></a>Problema

Un banner con il messaggio seguente rimane nell'account dopo aver tentato di aggiornare i moduli di AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Si è verificato un problema noto relativo all'aggiornamento dei moduli AzureRM in un account di automazione che si trova in un gruppo di risorse con un nome numerico che inizia con 0.

#### <a name="resolution"></a>Soluzione

Per aggiornare i moduli di AzureRM nell'account di automazione, l'account deve trovarsi in un gruppo di risorse con un nome alfanumerico. I gruppi di risorse con nomi numerici che iniziano con 0 non sono in grado di aggiornare i moduli AzureRM in questo momento.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>Scenario: L'importazione del modulo non riesce o i cmdlet non possono essere eseguiti dopo l'importazione

#### <a name="issue"></a>Problema

Un modulo non viene importato o viene importato correttamente, ma non viene estratto alcun cmdlet.

#### <a name="cause"></a>Causa

Di seguito sono elencati alcuni motivi comuni che possono causare un'importazione errata di un modulo in Automazione di Azure:

* La struttura non corrisponde alla struttura necessaria per l'automazione.
* Il modulo dipende da un altro modulo che non è stato distribuito nel proprio account di automazione.
* Le dipendenze del modulo non si trovano nella cartella.
* Il cmdlet [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) viene usato per caricare il modulo e non è stato specificato il percorso di archiviazione completo o il modulo non è stato caricato usando un URL accessibile pubblicamente.

#### <a name="resolution"></a>Soluzione

Usare una di queste soluzioni per risolvere il problema.

* Verificare che il modulo segua il formato: ModuleName. zip-> moduleName o numero di versione-> (ModuleName. psm1, ModuleName. psd1).
* Aprire il file con **estensione psd1** e verificare se il modulo presenta dipendenze. In caso affermativo, caricare i moduli nell'account di automazione.
* Verificare che nella cartella del modulo siano presenti file **dll** a cui si fa riferimento.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>Scenario: Update-AzureModule. ps1 viene sospeso durante l'aggiornamento di moduli

#### <a name="issue"></a>Problema

Quando si usa il Runbook [Update-AzureModule. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) per aggiornare i moduli di Azure, il processo di aggiornamento del modulo viene sospeso.

#### <a name="cause"></a>Causa

L'impostazione predefinita per determinare il numero di moduli aggiornati simultaneamente è 10 quando si usa **Update-AzureModule. ps1**. Quando si aggiornano troppi moduli nello stesso momento, il processo di aggiornamento è soggetto a errori.

#### <a name="resolution"></a>Soluzione

Non è comune che tutti i moduli AzureRM o AZ siano necessari nello stesso account di automazione. È consigliabile importare solo i moduli specifici necessari.

> [!NOTE]
> Evitare di importare l' `Az.Automation` intero `AzureRM.Automation` modulo o, che importa tutti i moduli contenuti.

Se il processo di aggiornamento viene sospeso, aggiungere `SimultaneousModuleImportJobCount` il parametro allo script **Update-AzureModules. ps1** e specificare un valore inferiore a quello predefinito di 10. Se si implementa questa logica, è consigliabile iniziare con un valore pari a 3 o 5. `SimultaneousModuleImportJobCount`è un parametro del Runbook di sistema **Update-AutomationAzureModulesForAccount** usato per aggiornare i moduli di Azure. Se si effettua questa regolazione, il processo di aggiornamento viene eseguito più a lungo, ma ha una maggiore probabilità di completamento. L'esempio seguente mostra il parametro e la posizione in cui inserirlo nel runbook:

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

#### <a name="resolution"></a>Soluzione

Per creare o aggiornare un account RunAs, è necessario disporre [delle autorizzazioni](../manage-runas-account.md#permissions) appropriate per le varie risorse utilizzate dall'account RunAs. 

Se il problema è dovuto a un blocco, verificare che sia possibile rimuovere il blocco. Passare quindi alla risorsa bloccata in portale di Azure, fare clic con il pulsante destro del mouse sul blocco e scegliere **Elimina**.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>Scenario: viene visualizzato l'errore "Impossibile trovare un punto di ingresso denominato ' GetPerAdapterInfo ' nella DLL ' iplpapi. dll '" durante l'esecuzione di un Runbook

#### <a name="issue"></a>Problema

Quando si esegue un Runbook, si riceve l'eccezione seguente:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Questo errore è probabilmente causato da un [account RunAs](../manage-runas-account.md)configurato in modo non corretto.

#### <a name="resolution"></a>Soluzione

Verificare che l'account RunAs sia configurato correttamente. Quindi verificare di disporre del codice appropriato nella Runbook per l'autenticazione con Azure. L'esempio seguente illustra un frammento di codice per l'autenticazione in Azure in una Runbook usando un account RunAs.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato in precedenza o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi a [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.
