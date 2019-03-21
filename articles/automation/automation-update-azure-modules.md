---
title: Aggiornare i moduli di Azure in Automazione di Azure
description: L'articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03174e6336589f8aa49a7fc7197da1301ff54400
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009774"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

Per aggiornare i moduli di Azure nell'Account di automazione è consigliabile usare [Aggiorna il runbook dei moduli di Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), che è ora open source. Inoltre, è comunque possibile usare il pulsante **Aggiorna moduli di Azure** nel portale per aggiornare i moduli di Azure. Per altre informazioni sull'uso di runbook open source, vedere [Aggiorna moduli di Azure con runbook open source](#open-source).

I moduli di Azure PowerShell più comuni sono forniti per impostazione predefinita in ogni account di Automazione. Il team di Azure aggiorna regolarmente i moduli di Azure. Nell'account di Automazione viene fornito un modo per aggiornare i moduli nell'account quando diventano disponibili nuove versioni dal portale.

Dato che i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono subentrare modifiche per i cmdlet inclusi. Questa azione può influire negativamente sui runbook in base al tipo di modifica, ad esempio la ridenominazione di un parametro o la completa deprecazione di un cmdlet.

Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, eseguire test e convalide prima di procedere. Se non è disponibile un account di Automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari diversi durante lo sviluppo dei runbook. Questo test deve includere modifiche iterative, ad esempio l'aggiornamento dei moduli di PowerShell. 

Se si sviluppano gli script in locale, è consigliabile avere in locale le stesse versioni del modulo usate nell'account di Automazione durante i test, per assicurarsi di ottenere gli stessi risultati. Dopo aver convalidato i risultati e applicato le modifiche necessarie, è possibile spostare le modifiche in produzione.

> [!NOTE]
> Un nuovo account di automazione potrebbe non contenere i moduli più recenti.

## <a name="open-source"></a>Aggiornare moduli di Azure con runbook open source

Per iniziare a usare il runbook **Update-AutomationAzureModulesForAccount** per aggiornare i moduli di Azure, scaricarlo dall'[archivio runbook Aggiorna moduli di Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) su GitHub. È quindi possibile importarlo nell'Account di automazione o eseguirlo come script. Le istruzioni su come eseguire questa operazione sono reperibili nell'[archivio runbook Aggiorna i moduli di Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

### <a name="considerations"></a>Considerazioni

Di seguito sono riportate alcune considerazioni di cui tenere conto quando si usa questo processo per aggiornare i moduli di Azure:

* Se si importa questo runbook con il nome originale `Update-AutomationAzureModulesForAccount`, esso sostituirà il runbook interno con tale nome. Di conseguenza, il runbook importato verrà eseguito quando si preme il pulsante **Aggiorna moduli di Azure** o quando il runbook viene richiamato direttamente tramite API di Azure Resource Manager per questo Account di automazione.

* Questo runbook supporta l'aggiornamento solo il **Azure** e **AzureRm** attualmente i moduli. [I moduli di Azure PowerShell Az](/powershell/azure/new-azureps-module-az) sono supportate negli account di automazione, ma non possono essere aggiornati con questo runbook.

* Evitare di avviare questo runbook per gli Account di automazione che contengono i moduli Az.

* Prima di avviare il runbook, assicurarsi che l'Account di automazione disponga di una [credenziale dell'account RunAs di Azure](manage-runas-account.md).

* È possibile usare questo codice come un normale script di PowerShell invece di un runbook: è sufficiente accedere ad Azure usando il comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) prima di tutto, quindi passare `-Login $false` allo script.

* Per usare questo runbook nel cloud sovrano, usare il parametro `AzureRmEnvironment` per inviare l'ambiente corretto al runbook.  I valori accettabili sono **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernment**. Questi valori possono essere recuperati tramite `Get-AzureRmEnvironment | select Name`. Se non si passa un valore a questo parametro, l'impostazione predefinita del runbook sarà il cloud pubblico di Azure **AzureCloud**.

* Per usare una versione specifica del modulo Azure PowerShell invece di quella più recente disponibile in PowerShell Gallery, inviare queste versioni al parametro `ModuleVersionOverrides` facoltativo del runbook **Update-AutomationAzureModulesForAccount**. Per avere esempi, vedere il runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). I moduli di Azure PowerShell non indicati nel parametro `ModuleVersionOverrides` vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery. Se non si passa nulla al parametro `ModuleVersionOverrides`, tutti i moduli vengono aggiornati con le ultime versioni dei moduli in PowerShell Gallery. Questo comportamento è lo stesso del pulsante **Aggiorna moduli di Azure**.

## <a name="update-azure-modules-in-the-azure-portal"></a>Aggiornare i moduli di Azure nel portale di Azure

1. Nella pagina Moduli dell'account di Automazione è presente un'opzione denominata **Aggiorna moduli di Azure**. Tale opzione è sempre abilitata.<br><br> ![Opzione Aggiorna i moduli di Azure nella pagina Moduli](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

   > [!NOTE]
   > Prima di aggiornare i moduli di Azure, è consigliabile aggiornarli in un account di Automazione di test per assicurarsi che gli script esistenti funzionino come previsto prima di aggiornare i moduli di Azure.
   >
   > Il pulsante **Aggiorna moduli di Azure** è disponibile solo nel cloud pubblico. Non è disponibile nelle [aree sovrane](https://azure.microsoft.com/global-infrastructure/). Usare il runbook **Update-AutomationAzureModulesForAccount** per aggiornare i moduli di Azure. È possibile scaricarlo dall'[archivio runbook Aggiorna moduli Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update). Per altre informazioni sull'uso di runbook open source, vedere [Aggiorna moduli di Azure con runbook open source](#open-source).

2. Fare clic su **Aggiorna i moduli di Azure**. Viene visualizzata una notifica di conferma che chiede se si desidera continuare.<br><br> ![Notifica di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Fare clic su **Sì** per avviare il processo di aggiornamento del modulo. Il processo richiede circa 15-20 minuti per aggiornare i seguenti moduli:

   * Azure
   * Azure.Storage
   * AzureRm.Automation
   * AzureRm.Compute
   * AzureRm.Profile
   * AzureRm.Resources
   * AzureRm.Sql
   * AzureRm.Storage

     Se i moduli sono già aggiornati, il processo verrà completato in pochi secondi. Al termine del processo di aggiornamento, si riceverà una notifica.<br><br> ![Stato di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

     I moduli AzureRm di .NET Core (AzureRm.*.Core) non sono supportati in Automazione di Azure e non possono essere importati.

> [!NOTE]
> Quando viene eseguito un nuovo processo pianificato, Automazione di Azure usa i moduli più recenti nell'account di automazione.  

Se si usano i cmdlet di questi moduli di Azure PowerShell nei runbook, si potrebbe voler eseguire il processo di aggiornamento all'incirca ogni mese per assicurarsi di disporre dei moduli più recenti. Automazione di Azure usa la connessione `AzureRunAsConnection` per l'autenticazione quando si aggiornano i moduli. Se l'entità servizio è scaduta o non esiste più a livello della sottoscrizione, l'aggiornamento dei moduli non riuscirà.

## <a name="known-issues"></a>Problemi noti

Si verifica un problema noto con l'aggiornamento dei moduli AzureRM in un Account di automazione che si trova in un gruppo di risorse con un nome numerico che inizia con 0. Per aggiornare i moduli di Azure nell'Account di automazione, deve essere in un gruppo di risorse con un nome alfanumerico. Gruppi di risorse con numerici nomi che iniziano con 0 vengono non è possibile aggiornare i moduli AzureRM in questo momento.

## <a name="next-steps"></a>Passaggi successivi

Visitare il [runbook Aggiorna moduli Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) open source per altre informazioni.
