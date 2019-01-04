---
title: Risolvere i problemi relativi alle risorse condivise di Automazione di Azure
description: Informazioni su come risolvere i problemi relativi alle risorse condivise di Automazione di Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848460"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Risolvere i problemi relativi alle risorse condivise

Questo articolo illustra alcune procedure per la risoluzione dei problemi che si possono riscontrare quando si usano le risorse condivise di Automazione di Azure.

## <a name="modules"></a>Moduli

### <a name="module-stuck-importing"></a>Scenario: un modulo si blocca durante l'importazione

#### <a name="issue"></a>Problema

Durante l'importazione o l'esportazione dei moduli in Automazione di Azure, un modulo rimane bloccato nello stato **Importazione**.

#### <a name="cause"></a>Causa

L'importazione dei moduli di PowerShell è un processo complesso in più passaggi. Questo processo introduce la possibilità che un modulo non venga importato correttamente. Se si verifica questo problema, il modulo che si sta importando può rimanere bloccato in uno stato temporaneo. Per altre informazioni su questo processo, vedere [Importazione di un modulo PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema è necessario rimuovere il modulo bloccato nello stato **Importazione** tramite il cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). È quindi possibile ritentare l'importazione del modulo.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
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

Se il problema è dovuto a un blocco, verificare che sia possibile eliminarlo, quindi passare alla risorsa bloccata, fare clic con il pulsante destro del mouse sul blocco e scegliere **Elimina** per rimuovere il blocco.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.