---
title: Aggiornare i moduli di Azure in Automazione di Azure
description: L'articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 11/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fc605ab45241280d9331ad7d515ba007a015daa
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583654"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

I moduli di Azure PowerShell più comuni sono forniti per impostazione predefinita in ogni account di Automazione. Il team di Azure aggiorna regolarmente i moduli di Azure. Nell'account di Automazione viene fornito un modo per aggiornare i moduli nell'account quando diventano disponibili nuove versioni dal portale.

Dato che i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono subentrare modifiche per i cmdlet inclusi. Questa azione può influire negativamente sui runbook in base al tipo di modifica, ad esempio la ridenominazione di un parametro o la completa deprecazione di un cmdlet. Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, eseguire test e convalide prima di procedere. Se non è disponibile un account di Automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari diversi durante lo sviluppo dei runbook. Questo test deve includere modifiche iterative, ad esempio l'aggiornamento dei moduli di PowerShell. Dopo aver convalidato i risultati e applicato le modifiche necessarie, è possibile spostare le modifiche in produzione.

> [!NOTE]
> Un nuovo account di automazione potrebbe non contenere i moduli più recenti.

## <a name="updating-azure-modules"></a>Aggiornamento dei moduli di Azure

1. Nella pagina Moduli dell'account di Automazione è presente un'opzione denominata **Aggiorna moduli di Azure**. Tale opzione è sempre abilitata.<br><br> ![Opzione Aggiorna i moduli di Azure nella pagina Moduli](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Prima di aggiornare i moduli di Azure, è consigliabile aggiornarli in un account di Automazione di test per assicurarsi che gli script esistenti funzionino come previsto prima di aggiornare i moduli di Azure.
  >
  > Il pulsante **Aggiorna moduli di Azure** è disponibile solo nel cloud pubblico. Non è disponibile nelle [aree sovrane](https://azure.microsoft.com/global-infrastructure/). Per altre informazioni, vedere la sezione [Modi alternativi per aggiornare i moduli](#alternative-ways-to-update-your-modules).


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

## <a name="alternative-ways-to-update-your-modules"></a>Modi alternativi per aggiornare i moduli

Come indicato, il pulsante **Aggiorna moduli di Azure** non è disponibile nei cloud sovrani, ma solo nel cloud di Azure globale, perché la versione più recente dei moduli di Azure PowerShell di PowerShell Gallery potrebbe non funzionare con le risorse di Resource Manager attualmente distribuite in questi cloud.

È possibile importare ed eseguire il runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) per tentare di aggiornare i moduli di Azure nell'account di Automazione. Questo processo potrebbe non riuscire se le versioni che si sta tentando di importare dalla raccolta non sono compatibili con i servizi di Azure attualmente distribuiti nell'ambiente di Azure di destinazione. Potrebbe essere necessario verificare che le versioni compatibili dei moduli siano specificate nei parametri del runbook.

Usare il parametro `AzureRmEnvironment` per passare l'ambiente corretto al runbook.  I valori accettabili sono **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernment**. Questi valori possono essere ottenuti tramite `Get-AzureRmEnvironment | select Name`. Se non si passa un valore a questo parametro, l'impostazione predefinita del runbook sarà il cloud pubblico di Azure **AzureCloud**.

Per usare una versione del modulo di Azure PowerShell specifica invece di quella più recente disponibile in PowerShell Gallery, passare queste versioni al parametro `ModuleVersionOverrides` facoltativo del runbook **Update-AzureModule**. Per esempi, vedere il runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1). I moduli di Azure PowerShell non indicati nel parametro `ModuleVersionOverrides` vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery. Se non si passa nulla al parametro `ModuleVersionOverrides`, tutti i moduli vengono aggiornati con le ultime versioni dei moduli in PowerShell Gallery. Questo comportamento è lo stesso del pulsante **Aggiorna moduli di Azure**.

## <a name="next-steps"></a>Passaggi successivi

* Per maggiori informazioni sui moduli di integrazione e sulla creazione di moduli personalizzati per l'ulteriore integrazione di Automazione con altri sistemi, servizi o soluzioni, vedere [Moduli di integrazione](automation-integration-modules.md).

