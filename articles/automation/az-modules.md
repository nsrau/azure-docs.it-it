---
title: Uso di moduli Az in Automazione di Azure
description: Questo articolo fornisce informazioni sull'uso dei moduli Az in Automazione di Azure
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c722dc6af2b98adb60045d530bb38de7762027d5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477901"
---
# <a name="az-module-support-in-azure-automation"></a>Supporto per i moduli Az in Automazione di Azure

Automazione di Azure supporta la possibilità di usare il [modulo Az di Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nei runbook. Il modulo Az non viene importato automaticamente in qualsiasi account di automazione nuovo o esistente. Questo articolo illustra come usare i moduli Az con Automazione di Azure.

## <a name="considerations"></a>Considerazioni

Esistono molti aspetti da prendere in considerazione quando si usa il modulo Az in Automazione di Azure. I runbook e i moduli possono essere usati da soluzioni di livello superiore nell'account di automazione. La modifica dei runbook o l'aggiornamento dei moduli può causare potenzialmente problemi con i runbook. È consigliabile testare attentamente tutti i runbook e le soluzioni in un account di automazione distinto prima di importare i nuovi moduli `Az`. Qualsiasi modifica ai moduli può influire negativamente sulle soluzioni di livello superiore, ad esempio Gestione aggiornamenti e Avvio/Arresto di macchine virtuali durante gli orari di minore attività. È consigliabile non modificare moduli e runbook in account di automazione che contengono soluzioni. Questo comportamento non è specifico per i moduli Az. Questo comportamento deve essere preso in considerazione quando si introducono modifiche all'account di automazione.

L'importazione di un modulo `Az` nell'account di automazione non comporta automaticamente l'importazione del modulo nella sessione di PowerShell usata dai runbook. I moduli vengono importati nella sessione di PowerShell nelle situazioni seguenti:

* Quando un cmdlet da un modulo viene richiamato da un runbook
* Quando un runbook lo importa in modo esplicito con il cmdlet `Import-Module`
* Quando un altro modulo dipendente dal modulo viene importato in una sessione di PowerShell

> [!IMPORTANT]
> È importante assicurarsi che i runbook in un account di automazione importino solo moduli `Az` o `AzureRM` nelle sessioni di PowerShell usate dai runbook e non entrambi. Se `Az` viene importato prima di `AzureRM` in un runbook, il runbook verrà completato, ma sarà visualizzato un [errore che fa riferimento al metodo get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) nei flussi del processo e i cmdlet potrebbero non essere eseguiti correttamente. Se si importa `AzureRM` e quindi `Az`, il runbook verrà comunque completato, ma sarà visualizzato un errore nei flussi del processo che informa che `Az` e `AzureRM` non possono essere entrambi importati nella stessa sessione o usati nello stesso runbook.

## <a name="migrating-to-az-modules"></a>Migrazione a moduli Az

È consigliabile testare la migrazione per l'uso di moduli Az anziché di moduli AzureRM in un account di automazione di test. Dopo aver creato l'account di automazione, è possibile usare la procedura seguente per assicurarsi che la migrazione venga eseguita senza problemi:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Arrestare e annullare la pianificazione di tutti i runbook che usano moduli AzureRM

Per assicurarsi di non eseguire runbook esistenti che usano cmdlet `AzureRM`, è necessario arrestare e annullare la pianificazione di tutti i runbook che usano moduli `AzureRM`. È possibile visualizzare le pianificazioni esistenti e quelle da rimuovere eseguendo l'esempio seguente:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

È importante esaminare ogni pianificazione separatamente per assicurarsi di poterla ripianificare in futuro per i runbook se necessario.

### <a name="import-the-az-modules"></a>Importare moduli Az

Importare solo i moduli Az richiesti per i runbook. Non importare il modulo `Az` di rollup, poiché include tutti i moduli `Az.*` da importare. Questa indicazione è valida per tutti i moduli.

Il modulo [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) è una dipendenza per gli altri moduli `Az.*`. Per questo motivo, questo modulo deve essere importato nell'account di automazione prima di importare qualsiasi altro modulo.

Dall'account di automazione, selezionare **Moduli** in **Risorse condivise**. Fare clic su **Esplora raccolta** per aprire la pagina **Esplora raccolta**.  Nella barra di ricerca immettere il nome del modulo (ad esempio `Az.Accounts`). Nella pagina Modulo PowerShell fare clic su **Importa** per importare il modulo nell'account di automazione.

![Importare moduli dall'account di automazione](media/az-modules/import-module.png)

Questo processo di importazione può essere eseguito da [PowerShell Gallery](https://www.powershellgallery.com) tramite una ricerca del modulo. Dopo aver trovato il modulo, selezionarlo e nella scheda **Automazione di Azure** fare clic su **Deploy to Azure Automation** (Distribuisci in Automazione di Azure).

![Importare moduli direttamente dalla raccolta](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testare i runbook

Dopo che i moduli `Az` sono stati importati nell'account di automazione, è possibile iniziare a modificare i runbook per usare il modulo Az. La maggior parte dei cmdlet ha lo stesso nome, ad eccezione di `AzureRM` che è stato modificato in `Az`. Per un elenco di moduli che non seguono questo processo, vedere l'[elenco delle eccezioni](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Un modo per testare i runbook prima di modificarli per l'uso dei nuovi cmdlet consiste nell'usare `Enable-AzureRMAlias -Scope Process` all'inizio di un runbook. In questo modo il runbook può essere eseguito senza modifiche.

## <a name="after-migration-details"></a>Dettagli post-migrazione

Al termine della migrazione, non avviare più i runbook usando i moduli `AzureRM` nell'account. È inoltre consigliabile non importare o aggiornare i moduli `AzureRM` in questo account. A partire da questo momento, considerare l'account migrato a `Az`e usarlo solo con i moduli `Az`. Quando viene creato un nuovo account di automazione, i moduli `AzureRM` esistenti continueranno a essere installati e i runbook di esercitazione verranno comunque creati con i cmdlet `AzureRM`. Questi runbook non devono essere eseguiti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di moduli Az, vedere la [guida introduttiva ai moduli Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
