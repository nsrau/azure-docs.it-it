---
title: Aggiornare i moduli di Azure in Automazione di Azure
description: L'articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbb57753117f3c60010fe910616b8d0af5178360
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434824"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

I moduli di Azure PowerShell più comuni sono forniti per impostazione predefinita in ogni account di Automazione. Il team di Azure aggiorna regolarmente i moduli di Azure. Pertanto, l'account di Automazione prevede una modalità di aggiornamento dei moduli non appena nel portale sono disponibili nuove versioni.

Poiché i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono essere eseguite modifiche con i cmdlet inclusi, che potrebbero influire negativamente sui runbook a seconda del tipo di modifica, ad esempio ridenominando un parametro o deprecando completamente un cmdlet. Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, si consiglia di eseguire un test e di convalidare prima di procedere. Se non si dispone di un account di automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari e permutazioni diverse durante lo sviluppo dei runbook, oltre a poter eseguire modifiche iterative, ad esempio l'aggiornamento dei moduli PowerShell. Dopo aver convalidato i risultati e applicato le modifiche necessarie, procedere coordinando la migrazione di tutti i runbook modificati ed eseguire l'aggiornamento di seguito come descritto nella produzione.

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

    I moduli AzureRm core di .NET (AzureRm.*.Core) non sono supportati in Automazione di Azure e non possono essere importati.

> [!NOTE]
> Quando viene eseguito un nuovo processo pianificato, Automazione di Azure usa i moduli più recenti nell'account di automazione.  

Se si usano i cmdlet di questi moduli di Azure PowerShell nei runbook, si potrebbe voler eseguire il processo di aggiornamento all'incirca ogni mese per assicurarsi di disporre dei moduli più recenti. Automazione di Azure usa la connessione AzureRunAsConnection per eseguire l'autenticazione quando si aggiornano i moduli, se l'entità servizio è scaduta o non esiste più nel livello di sottoscrizione, l'aggiornamento dei moduli non verrà eseguito.

## <a name="alternative-ways-to-update-your-modules"></a>Modi alternativi per aggiornare i moduli

Come indicato, il pulsante **Aggiorna moduli di Azure** non è disponibile nei cloud sovrani, ma solo nel cloud di Azure globale perché la versione più recente dei moduli di Azure PowerShell di PowerShell Gallery potrebbe non funzionare con i servizi di Resource Manager attualmente distribuiti in questi cloud.

L'aggiornamento dei moduli può comunque essere eseguito importando il runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) nell'account di Automazione ed eseguendolo.

Usare il parametro `AzureRmEnvironment` per passare l'ambiente corretto al runbook.  I valori accettabili sono **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernmentCloud**. Se non si passa un valore a questo parametro, l'impostazione predefinita del runbook sarà il cloud pubblico di Azure **AzureCloud**.

Per usare una versione del modulo di Azure PowerShell specifica invece di quella più recente disponibile in PowerShell Gallery, passare queste versioni al parametro `ModuleVersionOverrides` facoltativo del runbook **Update-AzureModule**. Per esempi, vedere il runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1). I moduli di Azure PowerShell non indicati nel parametro `ModuleVersionOverrides` vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery. Se non vengono passati valori al parametro `ModuleVersionOverrides`, tutti i moduli vengono aggiornati con le versioni più recenti dei moduli in PowerShell Gallery, che è il comportamento determinato dal pulsante **Aggiorna moduli di Azure**.

## <a name="next-steps"></a>Passaggi successivi

* Per maggiori informazioni sui moduli di integrazione e sulla creazione di moduli personalizzati per l'ulteriore integrazione di Automazione con altri sistemi, servizi o soluzioni, vedere [Moduli di integrazione](automation-integration-modules.md).

* Valutare la possibilità di usare l'integrazione del controllo del codice sorgente tramite [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) o [Azure DevOps](automation-scenario-source-control-integration-with-vsts.md) per gestire e controllare le versioni del portfolio di configurazione e del runbook di Automazione.  
