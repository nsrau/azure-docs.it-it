---
title: Aggiornare i moduli di Azure in Automazione di Azure | Documentazione Microsoft
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
ms.date: 02/13/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7a2999b3b1a54668f6ef45433efabd5a495418fe
ms.openlocfilehash: ec84df70d4a77e3b81a88aa286fc492d92e3e753


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Come aggiornare i moduli di Azure PowerShell in Automazione di Azure

I moduli di Azure PowerShell più comuni sono forniti per impostazione predefinita in ogni account di Automazione.  Il team di Azure aggiorna regolarmente i moduli di Azure. Pertanto, l'account di Automazione prevede una modalità di aggiornamento dei moduli non appena sono disponibili nuove versioni.

## <a name="updating-azure-modules"></a>Aggiornamento dei moduli di Azure

1. Nel pannello Moduli dell'account di Automazione è presente un'opzione denominata **Update Azure Modules** (Aggiorna i moduli di Azure).  Tale opzione è sempre abilitata.<br><br> ![Opzione di aggiornamento dei moduli di Azure nel pannello Moduli](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Fare clic su **Update Azure Modules** (Aggiorna i moduli di Azure), quindi viene visualizzata una notifica di conferma dell'intenzione di continuare.<br><br> ![Notifica di aggiornamento dei moduli di Azure](media/automation-update-azure-modules/automation-update-azure-modules-notification.png)

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

Come parte del processo di aggiornamento, le pianificazioni per i runbook pianificati verranno aggiornate per usare la versione più recente di modulo.

Se si usano i cmdlet di questi moduli di Azure PowerShell nei runbook per gestire le risorse di Azure, si potrebbe voler eseguire il processo di aggiornamento all'incirca ogni mese per assicurarsi di disporre dei moduli più recenti.

## <a name="next-steps"></a>Passaggi successivi

Per maggiori informazioni sui moduli di integrazione e sulla creazione di moduli personalizzati per l'ulteriore integrazione di Automazione con altri sistemi, servizi o soluzioni, vedere [Moduli di integrazione](automation-integration-modules.md).


<!--HONumber=Feb17_HO2-->


