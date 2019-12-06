---
title: Risolvere i problemi relativi alle risorse condivise di Automazione di Azure
description: Informazioni su come risolvere i problemi relativi alle risorse condivise di Automazione di Azure
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 9313b042433489307a2bd2822a96d1e0e127362b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849293"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Risolvere i problemi relativi alle risorse condivise

Questo articolo illustra alcune procedure per la risoluzione dei problemi che si possono riscontrare quando si usano le risorse condivise di Automazione di Azure.

## <a name="modules"></a>Moduli

### <a name="module-stuck-importing"></a>Scenario: Il modulo si blocca durante l'importazione

#### <a name="issue"></a>Problema

Durante l'importazione o l'esportazione dei moduli in Automazione di Azure, un modulo rimane bloccato nello stato **Importazione**.

#### <a name="cause"></a>Causa

L'importazione dei moduli di PowerShell è un processo complesso in più passaggi. Questo processo introduce la possibilità che un modulo non venga importato correttamente. Se si verifica questo problema, il modulo che si sta importando può rimanere bloccato in uno stato temporaneo. Per altre informazioni su questo processo, vedere [Importazione di un modulo PowerShell](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema è necessario rimuovere il modulo bloccato nello stato **Importazione** tramite il cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). È quindi possibile ritentare l'importazione del modulo.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="update-azure-modules-importing"></a>Scenario: l'importazione di moduli AzureRM è bloccata dopo il tentativo di aggiornamento

#### <a name="issue"></a>Problema

Un banner con il messaggio seguente rimane nell'account dopo aver tentato di aggiornare i moduli di AzureRM:

```error
Azure modules are being updated
```

#### <a name="cause"></a>Causa

Si è verificato un problema noto relativo all'aggiornamento dei moduli AzureRM in un account di automazione che si trova in un gruppo di risorse con un nome numerico che inizia con 0.

#### <a name="resolution"></a>Risoluzione

Per aggiornare i moduli di Azure nell'account di automazione, è necessario che si trovi in un gruppo di risorse con un nome alfanumerico. I gruppi di risorse con nomi numerici che iniziano con 0 non sono in grado di aggiornare i moduli AzureRM in questo momento.

### <a name="module-fails-to-import"></a>Scenario: L'importazione del modulo non riesce o i cmdlet non possono essere eseguiti dopo l'importazione

#### <a name="issue"></a>Problema

L'importazione di un modulo ha esito negativo oppure riesce ma non viene estratto alcun cmdlet.

#### <a name="cause"></a>Causa

Di seguito sono elencati alcuni motivi comuni che possono causare un'importazione errata di un modulo in Automazione di Azure:

* La struttura non corrisponde a quella in cui Automazione dovrebbe trovarsi.
* Il modulo dipende da un altro modulo che non è stato distribuito nel proprio account di automazione.
* Le dipendenze del modulo non si trovano nella cartella.
* Il `New-AzureRmAutomationModule` cmdlet viene usato per caricare il modulo e non è stato specificato il percorso di archiviazione completo oppure il modulo non è stato caricato con un URL accessibile pubblicamente.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Assicurarsi che il modulo sia conforme al formato seguente: NomeModulo.Zip **->** NomeModulo o NumeroVersione **->** (NomeModulo.psm1, NomeModulo.psd1)
* Aprire il file con estensione psd1 e vedere se il modulo include dipendenze. In caso affermativo, caricare i moduli nell'account di automazione.
* Assicurarsi che le eventuali DLL a cui viene fatto riferimento siano presenti nella cartella del modulo.

### <a name="all-modules-suspended"></a>Scenario: Update-AzureModule. ps1 viene sospeso durante l'aggiornamento di moduli

#### <a name="issue"></a>Problema

Quando si usa il runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) per aggiornare i moduli di Azure, il processo di aggiornamento dei moduli viene sospeso.

#### <a name="cause"></a>Causa

L'impostazione predefinita che determina il numero di moduli aggiornati contemporaneamente è 10 quando si usa lo script `Update-AzureModule.ps1`. Quando si aggiornano troppi moduli nello stesso momento, il processo di aggiornamento è soggetto a errori.

#### <a name="resolution"></a>Risoluzione

Non è usuale che nello stesso account di automazione siano necessari tutti i moduli AzureRM. È quindi consigliabile importare solo i moduli AzureRM effettivamente necessari.

> [!NOTE]
> Evitare di importare il modulo **AzureRM**. L'importazione dei moduli **AzureRM** causa l'importazione di tutti i moduli **AzureRM.\*** e questa situazione non è consigliata.

Se il processo di aggiornamento viene sospeso, è necessario aggiungere il parametro `SimultaneousModuleImportJobCount` allo script `Update-AzureModules.ps1` e fornire un valore inferiore a quello predefinito, ossia 10. Se si implementa questa logica, iniziare con il valore 3 o 5. `SimultaneousModuleImportJobCount` è un parametro del runbook di sistema `Update-AutomationAzureModulesForAccount` usato per aggiornare i moduli di Azure. Questa modifica prolunga l'esecuzione del processo, ma migliora le probabilità di completamento. L'esempio seguente mostra il parametro e la posizione in cui inserirlo nel runbook:

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

### <a name="unable-create-update"></a>Scenario: non è possibile creare o aggiornare un account RunAs

#### <a name="issue"></a>Problema

Quando si prova a creare o aggiornare un account RunAs viene visualizzato un messaggio di errore simile al seguente:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Causa

Non si dispone delle autorizzazioni necessarie per creare o aggiornare l'account RunAs oppure la risorsa è bloccata a un livello di gruppo di risorse.

#### <a name="resolution"></a>Risoluzione

Per creare o aggiornare un account RunAs è necessario disporre delle autorizzazioni appropriate per le varie risorse usate dall'account RunAs. Per informazioni sulle autorizzazioni necessarie per creare o aggiornare un account RunAs, vedere [Run As account permissions](../manage-runas-account.md#permissions) (Autorizzazioni per l'account RunAs).

Se il problema è causato da un blocco, verificare che sia possibile rimuoverlo. Passare quindi alla risorsa bloccata, fare clic con il pulsante destro del mouse sul blocco e scegliere **Elimina** per rimuovere il blocco.

### <a name="iphelper"></a>Scenario: viene visualizzato l'errore "Impossibile trovare un punto di ingresso denominato ' GetPerAdapterInfo ' nella DLL ' iplpapi. dll '" durante l'esecuzione di un Runbook.

#### <a name="issue"></a>Problema

Quando si esegue un Runbook, si riceve l'eccezione seguente:

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>Causa

Questo errore è probabilmente causato da un [account RunAs](../manage-runas-account.md)configurato in modo non corretto.

#### <a name="resolution"></a>Risoluzione

Verificare che l' [account RunAs](../manage-runas-account.md) sia configurato correttamente. Una volta configurata correttamente, verificare di avere il codice corretto nel Runbook per l'autenticazione con Azure. L'esempio seguente illustra un frammento di codice per l'autenticazione in Azure in una Runbook usando un account RunAs.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiedi supporto**.
