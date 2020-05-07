---
title: Aggiornare i moduli Azure PowerShell in automazione di Azure
description: L'articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3f2ceda6e87ce16e910f3d215c1fabe81d522f32
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855548"
---
# <a name="update-azure-powershell-modules-in-azure-automation"></a>Aggiornare i moduli Azure PowerShell in automazione di Azure

Per aggiornare i moduli di Azure nell'account di automazione, è necessario usare l' [aggiornamento dei moduli di Azure Runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), disponibile come open source. Per iniziare a usare il runbook **Update-AutomationAzureModulesForAccount** per aggiornare i moduli di Azure, scaricarlo dall'[archivio runbook Aggiorna moduli di Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) su GitHub. È quindi possibile importarlo nell'account di automazione o eseguirlo come script. Per informazioni su come importare un Runbook nell'account di automazione, vedere [importare un Runbook](manage-runbooks.md#import-a-runbook).

I moduli di PowerShell più comuni sono forniti per impostazione predefinita in ogni account di automazione. Il team di Azure aggiorna regolarmente i moduli di Azure. Pertanto, per tenere aggiornati i moduli degli account di automazione, è necessario usare il Runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) .

Dato che i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono subentrare modifiche per i cmdlet inclusi. Queste modifiche, ad esempio la ridenominazione di un parametro o la deprecazione di un cmdlet completamente, possono influire negativamente sul manuali operativi.

Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, eseguire test e convalide prima di procedere. Se non è disponibile un account di Automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari diversi durante lo sviluppo dei runbook. Questo test deve includere modifiche iterative, ad esempio l'aggiornamento dei moduli di PowerShell.

Se si sviluppano gli script localmente, è consigliabile avere le stesse versioni del modulo localmente presenti nell'account di automazione durante i test per assicurarsi di ricevere gli stessi risultati. Dopo aver convalidato i risultati e applicato le modifiche necessarie, è possibile spostare le modifiche in produzione.

> [!NOTE]
> Un nuovo account di automazione potrebbe non contenere i moduli più recenti.

> [!NOTE]
> Non sarà possibile eliminare i moduli globali, ovvero i moduli forniti dall'automazione.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). 

## <a name="considerations"></a>Considerazioni

Di seguito sono riportate alcune considerazioni da tenere in considerazione quando si usa questo articolo per aggiornare i moduli di Azure:

* Il Runbook descritto in questo articolo supporta l'aggiornamento dei moduli Azure, AzureRM e AZ per impostazione predefinita. Vedere il [file Leggimi relativo all'aggiornamento dei moduli di Azure Runbook](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) per altre informazioni sull'aggiornamento dei moduli AZ. Automation con questo runbook. Quando si usa il comando AZ Modules nell'account di automazione, è necessario prendere in considerazione altri fattori importanti. Per altre informazioni, vedere [gestire i moduli in automazione di Azure](shared-resources/modules.md).

* Prima di avviare il runbook, assicurarsi che l'Account di automazione disponga di una [credenziale dell'account RunAs di Azure](manage-runas-account.md).

* È possibile usare questo codice come uno script di PowerShell normale invece che con Runbook: è sufficiente accedere ad Azure usando prima il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0) , quindi `-Login $false` passare allo script.

* Per usare questo runbook nel cloud sovrano, usare il parametro `AzEnvironment` per inviare l'ambiente corretto al runbook.  I valori accettabili sono AzureCloud (cloud pubblico di Azure), AzureChinaCloud, AzureGermanCloud e AzureUSGovernment. Questi valori possono essere recuperati `Get-AzEnvironment | select Name`utilizzando. Se non si passa un valore a questo cmdlet, il valore predefinito di Runbook è AzureCloud.

* Se si vuole usare una versione specifica del modulo Azure PowerShell invece del modulo più recente disponibile sul PowerShell Gallery, passare queste versioni al parametro facoltativo `ModuleVersionOverrides` del Runbook **Update-AutomationAzureModulesForAccount** . Per avere esempi, vedere il runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). I moduli di Azure PowerShell non indicati nel parametro `ModuleVersionOverrides` vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery. Se non si passa nulla al parametro `ModuleVersionOverrides`, tutti i moduli vengono aggiornati con le ultime versioni dei moduli in PowerShell Gallery. Questo comportamento è lo stesso del pulsante **Aggiorna moduli di Azure**.

## <a name="next-steps"></a>Passaggi successivi

Visitare il [runbook Aggiorna moduli Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source per altre informazioni.
