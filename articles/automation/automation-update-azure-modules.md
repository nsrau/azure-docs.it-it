---
title: Aggiornare i moduli di Azure in Automazione di Azure
description: L'articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/30/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c09c9df1b8af7adca5c0169cf31881121ba6cc99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427477"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

Per aggiornare i moduli di Azure nell'Account di automazione è necessario usare il [runbook di moduli di Azure Aggiorna](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), che è disponibile come open source. Per iniziare a usare il runbook **Update-AutomationAzureModulesForAccount** per aggiornare i moduli di Azure, scaricarlo dall'[archivio runbook Aggiorna moduli di Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) su GitHub. È quindi possibile importarlo nell'Account di automazione o eseguirlo come script. Per informazioni su come importare un runbook nell'Account di automazione, vedere [importare un runbook](manage-runbooks.md#import-a-runbook).

I moduli AzureRM di PowerShell più comuni vengono forniti per impostazione predefinita in ogni account di automazione. Il team di Azure aggiorna regolarmente i moduli di Azure, pertanto per mantenere aggiornato sarà opportuno usare la [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook per aggiornare i moduli nell'account di automazione.

Dato che i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono subentrare modifiche per i cmdlet inclusi. Questa azione può influire negativamente sui runbook in base al tipo di modifica, ad esempio la ridenominazione di un parametro o la completa deprecazione di un cmdlet.

Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, eseguire test e convalide prima di procedere. Se non è disponibile un account di Automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari diversi durante lo sviluppo dei runbook. Questo test deve includere modifiche iterative, ad esempio l'aggiornamento dei moduli di PowerShell.

Se si sviluppano gli script in locale, è consigliabile avere in locale le stesse versioni del modulo usate nell'account di Automazione durante i test, per assicurarsi di ottenere gli stessi risultati. Dopo aver convalidato i risultati e applicato le modifiche necessarie, è possibile spostare le modifiche in produzione.

> [!NOTE]
> Un nuovo account di automazione potrebbe non contenere i moduli più recenti.

## <a name="considerations"></a>Considerazioni

Di seguito sono riportate alcune considerazioni di cui tenere conto quando si usa questo processo per aggiornare i moduli di Azure:

* Questo runbook supporta l'aggiornamento solo il **Azure** e **AzureRm** attualmente i moduli. [I moduli di Azure PowerShell Az](/powershell/azure/new-azureps-module-az) sono supportate negli account di automazione, ma non possono essere aggiornati con questo runbook. Vi sono fattori importanti che è necessario prendere in considerazione quando si usa la `Az` moduli nell'Account di automazione, per altre informazioni, vedere [moduli Az Using nell'Account di automazione](az-modules.md).

* Evitare di avviare questo runbook per gli Account di automazione che contengono i moduli Az.

* Prima di avviare il runbook, assicurarsi che l'Account di automazione disponga di una [credenziale dell'account RunAs di Azure](manage-runas-account.md).

* È possibile usare questo codice come un normale script di PowerShell invece di un runbook: è sufficiente accedere ad Azure tramite il [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) comando prima di tutto, quindi passare `-Login $false` allo script.

* Per usare questo runbook nel cloud sovrano, usare il parametro `AzureRmEnvironment` per inviare l'ambiente corretto al runbook.  I valori accettabili sono **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernment**. Questi valori possono essere recuperati tramite `Get-AzureRmEnvironment | select Name`. Se non si passa un valore a questo parametro, l'impostazione predefinita del runbook sarà il cloud pubblico di Azure **AzureCloud**.

* Per usare una versione specifica del modulo Azure PowerShell invece di quella più recente disponibile in PowerShell Gallery, inviare queste versioni al parametro `ModuleVersionOverrides` facoltativo del runbook **Update-AutomationAzureModulesForAccount**. Per avere esempi, vedere il runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). I moduli di Azure PowerShell non indicati nel parametro `ModuleVersionOverrides` vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery. Se non si passa nulla al parametro `ModuleVersionOverrides`, tutti i moduli vengono aggiornati con le ultime versioni dei moduli in PowerShell Gallery. Questo comportamento è lo stesso del pulsante **Aggiorna moduli di Azure**.

## <a name="known-issues"></a>Problemi noti

Si verifica un problema noto con l'aggiornamento dei moduli AzureRM in un Account di automazione che si trova in un gruppo di risorse con un nome numerico che inizia con 0. Per aggiornare i moduli di Azure nell'Account di automazione, deve essere in un gruppo di risorse con un nome alfanumerico. Gruppi di risorse con numerici nomi che iniziano con 0 vengono non è possibile aggiornare i moduli AzureRM in questo momento.

## <a name="next-steps"></a>Passaggi successivi

Visitare il [runbook Aggiorna moduli Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source per altre informazioni.
