---
title: Aggiornare i moduli di Azure in Automazione di Azure | Microsoft Docs
description: "L&quot;articolo descrive in che modo è ora possibile aggiornare i moduli di Azure PowerShell comuni disponibili per impostazione predefinita in Automazione di Azure."
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: ed8c97b642d406a05817ec6c67f31a1b4bce93b0
ms.contentlocale: it-it
ms.lasthandoff: 06/14/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

I moduli di Azure PowerShell più comuni sono forniti per impostazione predefinita in ogni account di Automazione.  Il team di Azure aggiorna regolarmente i moduli di Azure. Pertanto, l'account di Automazione prevede una modalità di aggiornamento dei moduli non appena nel portale sono disponibili nuove versioni.  

Poiché i moduli vengono aggiornati regolarmente dal gruppo di prodotto, possono essere eseguite modifiche con i cmdlet inclusi, che potrebbero influire negativamente sui runbook a seconda del tipo di modifica, ad esempio ridenominando un parametro o deprecando completamente un cmdlet. Per evitare conseguenze sui runbook e sui processi che vengono automatizzati, si consiglia di eseguire un test e di convalidare prima di procedere.  Se non si dispone di un account di automazione dedicato a questo scopo, è consigliabile crearne uno in modo da poter testare numerosi scenari e permutazioni diverse durante lo sviluppo dei runbook, oltre a poter eseguire modifiche iterative, ad esempio l'aggiornamento dei moduli PowerShell.  Dopo aver convalidato i risultati e applicato le modifiche necessarie, procedere con la coordinazione della migrazione di tutti i runbook modificati ed eseguire l'aggiornamento come descritto di seguito nella produzione.     

## <a name="updating-azure-modules"></a>Aggiornamento dei moduli di Azure

1. Nel pannello Moduli dell'account di Automazione è presente un'opzione denominata **Update Azure Modules** (Aggiorna i moduli di Azure).  Tale opzione è sempre abilitata.<br><br> ![Opzione di aggiornamento dei moduli di Azure nel pannello Moduli](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Fare clic su **Update Azure Modules** (Aggiorna i moduli di Azure), quindi viene visualizzata una notifica di conferma dell'intenzione di continuare.<br><br> ![Notifica di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Fare clic su **Sì** per avviare il processo di aggiornamento del modulo.  Il processo richiede circa 15-20 minuti per aggiornare i seguenti moduli:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Se i moduli sono già aggiornati, il processo verrà completato in pochi secondi.  Al termine del processo di aggiornamento si riceverà una notifica.<br><br> ![Stato di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Quando viene eseguito un nuovo processo pianificato, Automazione di Azure utilizzerà i moduli più recenti nell'account di automazione.    

Se si usano i cmdlet di questi moduli di Azure PowerShell nei runbook per gestire le risorse di Azure, si potrebbe voler eseguire il processo di aggiornamento all'incirca ogni mese per assicurarsi di disporre dei moduli più recenti.

## <a name="next-steps"></a>Passaggi successivi

* Per maggiori informazioni sui moduli di integrazione e sulla creazione di moduli personalizzati per l'ulteriore integrazione di Automazione con altri sistemi, servizi o soluzioni, vedere [Moduli di integrazione](automation-integration-modules.md).

* Valutare la possibilità di utilizzare l’integrazione del controllo del codice sorgente tramite [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) o [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) per gestire e controllare le versioni del portfolio di configurazione e del runbook di automazione.  
