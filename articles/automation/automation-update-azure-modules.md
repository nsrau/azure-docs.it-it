---
title: Aggiornare i moduli di Azure PowerShell in Automazione di Azure
description: Questo articolo illustra come aggiornare i comuni moduli di Azure PowerShell disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 9cc5e5107784d447640ef0ae77d412885b1e5668
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185773"
---
# <a name="update-azure-powershell-modules"></a>Aggiornare i moduli di Azure PowerShell

I moduli più comuni di Azure PowerShell vengono forniti per impostazione predefinita in ogni account di Automazione. Vedere [Moduli predefiniti](shared-resources/modules.md#default-modules). Man mano che il team di Azure aggiorna regolarmente i moduli di Azure, è possibile che si verifichino modifiche nei cmdlet inclusi. Queste modifiche, ad esempio la ridenominazione di un parametro o la deprecazione completa di un cmdlet, possono influire negativamente sui runbook. 

> [!NOTE]
> Non è possibile eliminare i moduli globali, ovvero i moduli forniti automaticamente da Automazione.

## <a name="set-up-an-automation-account"></a>Configurare un account di Automazione

Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, assicurarsi di eseguire test e convalide prima di procedere con gli aggiornamenti. Se non è disponibile un account di Automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari diversi durante lo sviluppo dei runbook. Questi test devono includere le modifiche iterative, ad esempio l'aggiornamento dei moduli di PowerShell.

Assicurarsi che per l'account di Automazione siano state create [credenziali dell'account RunAs di Azure](manage-runas-account.md).

Se si sviluppano gli script in locale, è consigliabile avere in locale le stesse versioni del modulo usate nell'account di Automazione durante i test, per assicurarsi di ottenere gli stessi risultati. Dopo aver convalidato i risultati e applicato le modifiche necessarie, è possibile spostare le modifiche in produzione.

> [!NOTE]
> Un nuovo account di automazione potrebbe non contenere i moduli più recenti.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Ottenere un runbook da usare per gli aggiornamenti

Per aggiornare i moduli di Azure nell'account di Automazione, è necessario usare il runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), disponibile come open source. Per iniziare a usare questo runbook per aggiornare i moduli di Azure, scaricarlo dal repository GitHub. È quindi possibile importarlo nell'account di Automazione o eseguirlo come script. Per informazioni su come importare un runbook nell'account di Automazione, vedere [Importare un runbook](manage-runbooks.md#import-a-runbook).

Il runbook **Update-AutomationAzureModulesForAccount** supporta l'aggiornamento dei moduli di Azure, AzureRM e Az per impostazione predefinita. Per altre informazioni sull'aggiornamento dei moduli Az.Automation con questo runbook, vedere il [runbook per aggiornare i moduli di Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md). Quando si usano i moduli Az nell'account di Automazione, è necessario tenere presenti altri fattori importanti. Per altre informazioni, vedere [Gestire i moduli in Automazione di Azure](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Usare il codice del runbook di aggiornamento come normale script di PowerShell

È possibile usare il codice del runbook come un normale script di PowerShell invece di un runbook. A tale scopo, accedere prima ad Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0), quindi passare `-Login $false` allo script.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Usare il runbook di aggiornamento nei cloud sovrani

Per usare questo runbook nei cloud sovrani, usare il parametro `AzEnvironment` per passare l'ambiente corretto al runbook. I valori accettabili sono AzureCloud (cloud pubblico di Azure), AzureChinaCloud, AzureGermanCloud e AzureUSGovernment. Questi valori possono essere recuperati tramite `Get-AzEnvironment | select Name`. Se non si passa un valore a questo cmdlet, il runbook usa per impostazione predefinita AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Usare il runbook di aggiornamento per aggiornare una specifica versione del modulo

Per usare una versione specifica del modulo di Azure PowerShell invece di quella più recente disponibile in PowerShell Gallery, passare queste versioni al parametro `ModuleVersionOverrides` facoltativo del runbook **Update-AutomationAzureModulesForAccount**. Per avere esempi, vedere il runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1). I moduli di Azure PowerShell non indicati nel parametro `ModuleVersionOverrides` vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery. Se non si passa nulla al parametro `ModuleVersionOverrides`, tutti i moduli vengono aggiornati con le ultime versioni dei moduli in PowerShell Gallery. Questo comportamento è lo stesso del pulsante **Aggiorna moduli di Azure** del portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui moduli, vedere [Gestire i moduli in Automazione di Azure](shared-resources/modules.md).
* Per informazioni sul runbook di aggiornamento, vedere il [runbook per l'aggiornamento dei moduli di Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
