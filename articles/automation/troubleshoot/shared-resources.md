---
title: Risolvere i problemi relativi alle risorse condivise di Automazione di Azure
description: Informazioni su come risolvere i problemi relativi alle risorse condivise di Automazione di Azure
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263416"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Risolvere i problemi relativi alle risorse condivise

Questo articolo illustra alcune procedure per la risoluzione dei problemi che si possono riscontrare quando si usano le risorse condivise di Automazione di Azure.

## <a name="modules"></a>Moduli

### <a name="module-stuck-importing"></a>Scenario: Il modulo si blocca durante l'importazione

#### <a name="issue"></a>Problema

Durante l'importazione o l'esportazione dei moduli in Automazione di Azure, un modulo rimane bloccato nello stato **Importazione**.

#### <a name="error"></a>Tipi di errore

L'importazione dei moduli di PowerShell è un processo complesso in più passaggi. Questo processo introduce la possibilità che un modulo non venga importato correttamente. Se ciò accade, il modulo che si sta importando può rimanere bloccato in uno stato temporaneo. Per altre informazioni su questo processo, vedere [Importazione di un modulo PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Risoluzione

Per risolvere questo problema è necessario rimuovere il modulo bloccato nello stato **Importazione** tramite il cmdlet [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule). È quindi possibile ritentare l'importazione del modulo.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.