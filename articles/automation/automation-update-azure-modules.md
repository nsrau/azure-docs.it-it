---
title: Aggiornare i moduli di Azure in Automazione di Azure
description: L'articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: ba926ccbb069c8620259514e0a64c56957529a0f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617477"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

Per aggiornare i moduli di Azure nell'account di automazione, è necessario usare il [runbook Aggiorna moduli](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)di Azure, disponibile come open source. Per iniziare a usare il runbook **Update-AutomationAzureModulesForAccount** per aggiornare i moduli di Azure, scaricarlo dall'[archivio runbook Aggiorna moduli di Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) su GitHub. È quindi possibile importarlo nell'account di automazione o eseguirlo come script. Per informazioni su come importare un runbook nell'account Di automazione, vedere [Importare un runbook.](manage-runbooks.md#importing-a-runbook)

I moduli di PowerShell più comuni vengono forniti per impostazione predefinita in ogni account di automazione. Il team di Azure aggiorna regolarmente i moduli di Azure. Pertanto, per mantenere aggiornati i moduli negli account di automazione, è necessario usare il runbook Update-AutomationAzureModulesForAccount.Therefore, to keep the modules in your Automation accounts up to date, you should use the [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook.

Dato che i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono subentrare modifiche per i cmdlet inclusi. Queste modifiche, ad esempio la ridenominazione di un parametro o la deprecazione di un cmdlet, possono influire negativamente sui runbook.

Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, eseguire test e convalide prima di procedere. Se non è disponibile un account di Automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari diversi durante lo sviluppo dei runbook. Questo test deve includere modifiche iterative, ad esempio l'aggiornamento dei moduli di PowerShell.This testing should include iterative changes, such as updating the PowerShell modules.

Se sviluppi gli script in locale, ti consigliamo di avere le stesse versioni del modulo in locale che hai nel tuo account di automazione durante il test per assicurarti di ricevere gli stessi risultati. Dopo aver convalidato i risultati e applicato le modifiche necessarie, è possibile spostare le modifiche in produzione.

> [!NOTE]
> Un nuovo account di automazione potrebbe non contenere i moduli più recenti.

> [!NOTE]
> Non sarà possibile eliminare i moduli globali, ovvero i moduli forniti dall'automazione.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . 

## <a name="considerations"></a>Considerazioni

Di seguito sono riportate alcune considerazioni da prendere in considerazione quando si usa questo articolo per aggiornare i moduli di Azure:The following are some considerations to take into account when using this article to update your Azure Modules:

* Il runbook descritto in questo articolo supporta l'aggiornamento dei moduli Azure, AzureRM e Az per impostazione predefinita. Esaminare il [runbook dei moduli](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) di Azure README per altre informazioni sull'aggiornamento dei moduli Az.Automation con questo runbook. Ci sono altri fattori importanti che è necessario prendere in considerazione quando si utilizzano i moduli Az nel tuo account di automazione. Per ulteriori informazioni, vedere [Utilizzo dei moduli Az nell'account di automazione](az-modules.md).

* Prima di avviare il runbook, assicurarsi che l'Account di automazione disponga di una [credenziale dell'account RunAs di Azure](manage-runas-account.md).

* È possibile usare questo codice come uno script di PowerShell normale anziché come runbook: è `-Login $false` sufficiente accedere ad Azure utilizzando prima il cmdlet [Connect-AzAccount,](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) quindi passare allo script.

* Per usare questo runbook nel cloud sovrano, usare il parametro `AzEnvironment` per inviare l'ambiente corretto al runbook.  I valori accettabili sono AzureCloud (cloud pubblico di Azure), AzureChinaCloud, AzureGermanCloud e AzureUSGovernment.Acceptable values are AzureCloud (Azure public cloud), AzureChinaCloud, AzureGermanCloud, and AzureUSGovernment. Questi valori possono `Get-AzEnvironment | select Name`essere recuperati utilizzando . Se non si passa un valore a questo cmdlet, il runbook predefinito è AzureCloud.If you don't pass a value to this cmdlet, the runbook defaults to AzureCloud.

* Se si vuole usare una versione specifica del modulo di Azure PowerShell anziché il `ModuleVersionOverrides` modulo più recente disponibile in PowerShell Gallery, passare queste versioni al parametro facoltativo del runbook **Update-AutomationAzureModulesForAccount.** Per avere esempi, vedere il runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). I moduli di Azure PowerShell non indicati nel parametro `ModuleVersionOverrides` vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery. Se non si passa nulla al parametro `ModuleVersionOverrides`, tutti i moduli vengono aggiornati con le ultime versioni dei moduli in PowerShell Gallery. Questo comportamento è lo stesso del pulsante **Aggiorna moduli di Azure**.

## <a name="next-steps"></a>Passaggi successivi

Visitare il [runbook Aggiorna moduli Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source per altre informazioni.
