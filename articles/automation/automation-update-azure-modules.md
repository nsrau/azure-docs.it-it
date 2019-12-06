---
title: Aggiornare i moduli di Azure in Automazione di Azure
description: L'articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76514e620f044b78b992db2b88733e69dbabf135
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850636"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

Per aggiornare i moduli di Azure nell'account di automazione, è necessario usare l' [aggiornamento dei moduli di Azure Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), disponibile come open source. Per iniziare a usare il runbook **Update-AutomationAzureModulesForAccount** per aggiornare i moduli di Azure, scaricarlo dall'[archivio runbook Aggiorna moduli di Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) su GitHub. È quindi possibile importarlo nell'Account di automazione o eseguirlo come script. Per informazioni su come importare un Runbook nell'account di automazione, vedere [importare un Runbook](manage-runbooks.md#import-a-runbook).

I moduli AzureRM PowerShell più comuni sono forniti per impostazione predefinita in ogni account di automazione. Il team di Azure aggiorna regolarmente i moduli di Azure, pertanto per restare aggiornati è necessario usare il Runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) per aggiornare i moduli negli account di automazione.

Dato che i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono subentrare modifiche per i cmdlet inclusi. Questa azione può influire negativamente sui runbook in base al tipo di modifica, ad esempio la ridenominazione di un parametro o la completa deprecazione di un cmdlet.

Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, eseguire test e convalide prima di procedere. Se non è disponibile un account di Automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari diversi durante lo sviluppo dei runbook. Questo test deve includere modifiche iterative, ad esempio l'aggiornamento dei moduli di PowerShell.

Se si sviluppano gli script in locale, è consigliabile avere in locale le stesse versioni del modulo usate nell'account di Automazione durante i test, per assicurarsi di ottenere gli stessi risultati. Dopo aver convalidato i risultati e applicato le modifiche necessarie, è possibile spostare le modifiche in produzione.

> [!NOTE]
> Un nuovo account di automazione potrebbe non contenere i moduli più recenti.

> [!NOTE]
> Non sarà possibile eliminare i moduli globali. i moduli forniti dall'automazione sono predefiniti.

## <a name="considerations"></a>Considerazioni

Di seguito sono riportate alcune considerazioni di cui tenere conto quando si usa questo processo per aggiornare i moduli di Azure:

* Questo runbook supporta l'aggiornamento dei moduli **Azure** e **AzureRm** per impostazione predefinita. Questo runbook supporta anche l'aggiornamento di **AZ** modules. Per altre informazioni sull'aggiornamento dei moduli di `Az` con questo Runbook, vedere il [file Leggimi relativo all'aggiornamento dei moduli di Azure Runbook](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) . Quando si usano i moduli `Az` nell'account di automazione, è necessario prendere in considerazione altri fattori importanti, per altre informazioni, vedere uso di [AZ Modules nell'account di automazione](az-modules.md).

* Prima di avviare il runbook, assicurarsi che l'Account di automazione disponga di una [credenziale dell'account RunAs di Azure](manage-runas-account.md).

* È possibile usare questo codice come uno script di PowerShell normale invece che con un Runbook: basta accedere ad Azure usando prima il comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) , quindi passare `-Login $false` allo script.

* Per usare questo runbook nel cloud sovrano, usare il parametro `AzureRmEnvironment` per inviare l'ambiente corretto al runbook.  I valori accettabili sono **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernment**. Questi valori possono essere recuperati tramite `Get-AzureRmEnvironment | select Name`. Se non si passa un valore a questo parametro, l'impostazione predefinita del runbook sarà il cloud pubblico di Azure **AzureCloud**.

* Per usare una versione specifica del modulo Azure PowerShell invece di quella più recente disponibile in PowerShell Gallery, inviare queste versioni al parametro `ModuleVersionOverrides` facoltativo del runbook **Update-AutomationAzureModulesForAccount**. Per avere esempi, vedere il runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). I moduli di Azure PowerShell non indicati nel parametro `ModuleVersionOverrides` vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery. Se non si passa nulla al parametro `ModuleVersionOverrides`, tutti i moduli vengono aggiornati con le ultime versioni dei moduli in PowerShell Gallery. Questo comportamento è lo stesso del pulsante **Aggiorna moduli di Azure**.

## <a name="next-steps"></a>Passaggi successivi

Visitare il [runbook Aggiorna moduli Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source per altre informazioni.
