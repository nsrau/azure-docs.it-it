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
ms.date: 04/20/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 6765ea93dd4e4e2594fb147dd19120aec058a2f5
ms.lasthandoff: 04/21/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

I moduli di Azure PowerShell più comuni sono forniti per impostazione predefinita in ogni account di Automazione.  Il team di Azure aggiorna regolarmente i moduli di Azure. Pertanto, l'account di Automazione prevede una modalità di aggiornamento dei moduli non appena nel portale sono disponibili nuove versioni.  

## <a name="updating-azure-modules"></a>Aggiornamento dei moduli di Azure

1. Nel pannello Moduli dell'account di Automazione è presente un'opzione denominata **Update Azure Modules** (Aggiorna i moduli di Azure).  Tale opzione è sempre abilitata.<br><br> ![Opzione di aggiornamento dei moduli di Azure nel pannello Moduli](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Fare clic su **Update Azure Modules** (Aggiorna i moduli di Azure), quindi viene visualizzata una notifica di conferma dell'intenzione di continuare.<br><br> ![Notifica di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Fare clic su **Sì** per avviare il processo di aggiornamento del modulo.  Il processo richiede circa 15-20 minuti per aggiornare i seguenti moduli:

  * Azure
  *    Azure.Storage
  *    AzureRm.Automation
  *    AzureRm.Compute
  *    AzureRm.Profile
  *    AzureRm.Resources
  *    AzureRm.Sql
  * AzureRm.Storage

    Se i moduli sono già aggiornati, il processo verrà completato in pochi secondi.  Al termine del processo di aggiornamento si riceverà una notifica.<br><br> ![Stato di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

Quando si crea una pianificazione, tutti i successivi processi in esecuzione in tale pianificazione usano i moduli presenti nell'account di automazione al momento della creazione della pianificazione.  Per iniziare a usare i moduli aggiornati con i runbook pianificati, è necessario scollegare e ricollegare la pianificazione con tale runbook.   

Se si usano i cmdlet di questi moduli di Azure PowerShell nei runbook per gestire le risorse di Azure, si potrebbe voler eseguire il processo di aggiornamento all'incirca ogni mese per assicurarsi di disporre dei moduli più recenti.

## <a name="next-steps"></a>Passaggi successivi

Per maggiori informazioni sui moduli di integrazione e sulla creazione di moduli personalizzati per l'ulteriore integrazione di Automazione con altri sistemi, servizi o soluzioni, vedere [Moduli di integrazione](automation-integration-modules.md).
