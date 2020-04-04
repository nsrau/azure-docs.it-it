---
title: Supporto per i moduli Az in Automazione di Azure
description: Questo articolo fornisce informazioni sull'uso dei moduli Az in Automazione di Azure.This article provides information about using Az modules in Azure Automation.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/08/2019
ms.topic: conceptual
ms.openlocfilehash: a8d6d25a2ba7f0040b13982f14f3d6081ac32f15
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638003"
---
# <a name="az-module-support-in-azure-automation"></a>Supporto per i moduli Az in Automazione di Azure

Automazione di Azure supporta l'uso del [modulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) nei runbook. Il modulo Az di rollup non viene importato automaticamente in alcun account di automazione nuovo o esistente. 

## <a name="considerations"></a>Considerazioni

Esistono diversi aspetti da considerare quando si usano i moduli di Az in Automazione di Azure:There are several things to take into consideration when using the Az modules in Azure Automation:

* Le soluzioni di livello superiore dell'account Di automazione possono usare runbook e moduli. Pertanto, la modifica dei runbook o l'aggiornamento dei moduli può causare problemi con le soluzioni. È consigliabile testare attentamente tutti i runbook e le soluzioni in un account di automazione separato prima di importare nuovi moduli Az.You should test all runbooks and solutions carefully in a separate Automation account before importing new Az modules. 

* Eventuali modifiche ai moduli possono influire negativamente sulla soluzione [Start/Stop.](automation-solution-vm-management.md) 

* L'importazione di un modulo Az nell'account di automazione non importa automaticamente il modulo nella sessione di PowerShell usata dai runbook. I moduli vengono importati nella sessione di PowerShell nelle situazioni seguenti:

    * Quando un runbook richiama un cmdlet da un modulo
    * Quando un runbook importa il `Import-Module` modulo in modo esplicito con il cmdlet
    * Quando un runbook importa un altro modulo a seconda del modulo

> [!NOTE]
> Non è consigliabile modificare moduli e runbook negli account di automazione che contengono soluzioni. Questa disposizione non è specifica per i moduli Az. Deve essere preso in considerazione quando si introducono modifiche al tuo account Di automazione.

> [!IMPORTANT]
> Assicurarsi che i runbook in un account di automazione importino moduli Az o [AzureRM,](https://www.powershellgallery.com/packages/AzureRM/6.13.1) ma non entrambi, in una sessione di PowerShell.Make sure that runbooks in an Automation account import either Az modules or AzureRM modules, but not both, into a PowerShell session. Se un runbook importa i moduli Az prima dei moduli di AzureRM, il runbook viene completato. Tuttavia, un errore che fa riferimento al [cmdlet Get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) viene visualizzato nei flussi di lavoro e cmdlet potrebbe non essere eseguito correttamente. Se il runbook importa i moduli di AzureRM prima dei moduli di Az, viene completato anche il runbook. In questo caso, tuttavia, viene visualizzato un errore nei flussi di lavoro che indica che sia Az che AzureRM non possono essere importati nella stessa sessione o usati nello stesso runbook.

## <a name="migrating-to-az-modules"></a>Migrazione a moduli Az

È consigliabile testare una migrazione ai moduli Az in un account di automazione di test. Una volta creato l'account, è possibile utilizzare le istruzioni riportate in questa sezione per lavorare con i moduli.

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Arrestare e annullare la pianificazione di tutti i runbook che usano i moduli di AzureRMStop and unschedule all runbooks that use AzureRM modules

Per assicurarsi di non eseguire runbook esistenti che usano i moduli di AzureRM, arrestare e annullare la pianificazione di tutti i runbook interessati. È possibile visualizzare le pianificazioni esistenti e le pianificazioni da rimuovere eseguendo codice simile a questo esempio:You can see what schedules exist and which schedules to remove by running code similar to this example:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

È importante esaminare ogni pianificazione separatamente per assicurarsi di poterla riprogrammare in futuro per i runbook, se necessario.

### <a name="import-the-az-modules"></a>Importare moduli Az

>[!NOTE]
>Fare in modo che i runbook importino solo i moduli Az necessari. Non importare il modulo Az di rollup, in quanto include tutti i moduli Az. Questa indicazione è valida per tutti i moduli.

Il modulo Az.Accounts è una dipendenza per gli altri moduli di Az.The [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) module is a dependency for the other Az modules. Per questo motivo, i runbook devono importare questo modulo nel tuo account Automation prima di importare qualsiasi altro modulo.

Per importare i moduli nel portale di Azure:To import the modules in the Azure portal:

1. Dall'account di automazione, selezionare **Moduli** in **Risorse condivise**. 
2. Fare clic su **Esplora raccolta** per aprire la pagina Esplora raccolta.  
3. Nella barra di ricerca immettere il `Az.Accounts`nome del modulo, ad esempio . 
4. Nella pagina Modulo PowerShell fare clic su **Importa** per importare il modulo nell'account di automazione.

![Importare moduli dall'account di automazione](media/az-modules/import-module.png)

Questo processo di importazione può essere eseguito anche tramite [PowerShell Gallery](https://www.powershellgallery.com) cercando il modulo per l'importazione. Dopo aver trovato il modulo, selezionarlo, scegliere la scheda **Automazione di Azure** e fare clic su **Distribuisci in Automazione di Azure**.

![Importare moduli direttamente dalla raccolta](media/az-modules/import-gallery.png)

## <a name="testing-your-runbooks"></a>Test dei runbook

Dopo aver importato i moduli Az nell'account di automazione, è possibile iniziare a modificare i runbook per utilizzare i moduli. La maggior parte dei cmdlet ha gli stessi nomi del modulo AzureRM, ad eccezione del fatto che il prefisso AzureRM (o AzureRm) è stato modificato in Az. Per un elenco dei moduli che non seguono questa convenzione di denominazione, vedere [elenco di eccezioni](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters).

Un modo per testare la modifica di un runbook `Enable-AzureRmAlias -Scope Process` per utilizzare i nuovi cmdlet consiste nell'utilizzare all'inizio del runbook. Aggiungendo questo comando al runbook, lo script può essere eseguito senza modifiche.

## <a name="after-migration-details"></a>Dettagli dopo la migrazione

Al termine della migrazione, non provare ad avviare runbook usando i moduli di AzureRM nell'account di automazione. È inoltre consigliabile non importare o aggiornare i moduli di AzureRM nell'account. Considerare l'account migrato in Az e operare solo con i moduli Az. 

Quando si crea un nuovo account di automazione, i moduli di AzureRM esistenti vengono comunque installati. È comunque possibile aggiornare i runbook dell'esercitazione con i cmdlet di AzureRM.You can still update the tutorial runbooks with AzureRM cmdlets. Tuttavia, è consigliabile non eseguire questi runbook.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di moduli Az, vedere la [guida introduttiva ai moduli Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
