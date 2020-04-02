---
title: Supporto per i moduli Az in Automazione di Azure
description: Questo articolo fornisce informazioni sull'uso dei moduli Az in Automazione di Azure.This article provides information about using Az modules in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: 776834937d81a3ba84e3c1db56496a7d951d7982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548350"
---
# <a name="az-module-support-in-azure-automation"></a>Supporto per i moduli Az in Automazione di Azure

Automazione di Azure supporta l'uso del [modulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nei runbook. Il modulo Az non viene importato automaticamente in alcun account Di automazione nuovo o esistente. 

## <a name="considerations"></a>Considerazioni

Esistono molti aspetti da prendere in considerazione quando si usa il modulo Az in Automazione di Azure. Runbook e moduli possono essere usati da soluzioni di livello superiore nel tuo account di automazione. La modifica dei runbook o l'aggiornamento dei moduli può causare potenzialmente problemi con i runbook. È consigliabile testare attentamente tutti i runbook e `Az` le soluzioni in un account di automazione separato prima di importare i nuovi moduli. Eventuali modifiche ai moduli possono influire negativamente sulla soluzione [Start/Stop.](automation-solution-vm-management.md) Non è consigliabile modificare moduli e runbook negli account di automazione che contengono soluzioni. Questo comportamento non è specifico per i moduli Az. Questo comportamento deve essere preso in considerazione quando si introducono modifiche all'account di automazione.

L'importazione di un `Az` modulo nell'account di automazione non importa automaticamente il modulo nella sessione di PowerShell usata dai runbook. I moduli vengono importati nella sessione di PowerShell nelle situazioni seguenti:

* Quando un cmdlet da un modulo viene richiamato da un runbook
* Quando un runbook lo importa in modo esplicito con il cmdlet `Import-Module`
* Quando un altro modulo dipendente dal modulo viene importato in una sessione di PowerShell

> [!IMPORTANT]
> È importante assicurarsi che i runbook in `Az` un `AzureRM` account di automazione importino o moduli nelle sessioni di PowerShell usate dai runbook e non entrambi. Se `Az` viene importato in precedenza `AzureRM` in un runbook, il runbook viene completato, ma un errore che fa riferimento al [Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) cmdlet viene visualizzato nei flussi di lavoro e i cmdlet potrebbero non essere eseguiti correttamente. Se si `AzureRM` importa `Az`e quindi , il runbook continua a completare, ma `Az` `AzureRM` viene visualizzato un errore nei flussi di lavoro che indica che entrambi e non possono essere importati nella stessa sessione o utilizzati nello stesso runbook.

## <a name="migrating-to-az-modules"></a>Migrazione a moduli Az

È consigliabile testare la migrazione ai moduli Az in un account di automazione di test. Una volta creato l'account Automation, è possibile utilizzare le istruzioni riportate in questa sezione per lavorare con i moduli.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-cmdlets"></a>Arrestare e annullare la pianificazione di tutti i runbook che usano i cmdlet di AzureRMStop and unschedule all runbooks that use AzureRM cmdlets

Per assicurarsi di non eseguire runbook esistenti che usano cmdlet `AzureRM`, è necessario arrestare e annullare la pianificazione di tutti i runbook che usano moduli `AzureRM`. È possibile visualizzare le pianificazioni esistenti e le pianificazioni che devono essere rimosse eseguendo codice simile a questo esempio.

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

È importante esaminare ogni pianificazione separatamente per assicurarsi di poterla riprogrammare in futuro per i runbook, se necessario.

### <a name="import-the-az-modules"></a>Importare moduli Az

Importare solo i moduli Az richiesti per i runbook. Non importare il `Az` modulo di rollup, `Az.*` in quanto include tutti i moduli. Questa indicazione è valida per tutti i moduli.

Il modulo [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) è una dipendenza per gli altri moduli `Az.*`. Per questo motivo, questo modulo deve essere importato nel tuo account Automation prima di importare altri moduli.

Dall'account di automazione, selezionare **Moduli** in **Risorse condivise**. Fare clic su **Esplora raccolta** per aprire la pagina **Esplora raccolta**.  Nella barra di ricerca immettere il nome del modulo (ad esempio `Az.Accounts`). Nella pagina Modulo PowerShell fare clic su **Importa** per importare il modulo nell'account di automazione.

![Importare moduli dall'account di automazione](media/az-modules/import-module.png)

Questo processo di importazione può essere eseguito anche tramite [PowerShell Gallery](https://www.powershellgallery.com) cercando il modulo per l'importazione. Dopo aver trovato il modulo, selezionarlo e nella scheda **Automazione di Azure** fare clic su **Deploy to Azure Automation** (Distribuisci in Automazione di Azure).

![Importare moduli direttamente dalla raccolta](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Test dei runbook

Una `Az` volta importati i moduli nel tuo account Automation, puoi iniziare a modificare i tuoi runbook per usare i moduli Az. La maggior parte dei cmdlet ha `AzureRM` gli stessi `Az`nomi, ad eccezione del fatto che è stata modificata in . Per un elenco dei moduli che non seguono questa convenzione di denominazione, vedere [elenco di eccezioni](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Un modo per testare la modifica di un runbook `Enable-AzureRMAlias -Scope Process` per utilizzare i nuovi cmdlet consiste nell'utilizzare all'inizio del runbook. Aggiungendo questo comando al runbook, lo script può essere eseguito senza modifiche.

## <a name="after-migration-details"></a>Dettagli dopo la migrazione

Al termine della migrazione, non tentare più `AzureRM` di avviare runbook utilizzando i moduli nell'account di automazione. Si consiglia inoltre di non `AzureRM` importare o aggiornare i moduli sull'account. Considerare l'account `Az`migrato in `Az` e utilizzare solo i moduli. 

Quando viene creato un nuovo `AzureRM` account di automazione, i moduli esistenti vengono comunque installati. È comunque possibile aggiornare `AzureRM` i runbook dell'esercitazione con i cmdlet. Non eseguire questi runbook.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di moduli Az, vedere la [guida introduttiva ai moduli Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
