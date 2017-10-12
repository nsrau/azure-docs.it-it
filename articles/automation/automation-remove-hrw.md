---
title: Rimuovere ruoli di lavoro ibridi per runbook di Automazione di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sulla gestione di un ruolo di lavoro ibrido per runbook di Automazione di Azure distribuito che consente di eseguire i runbook sui computer nel data center locale o nell'ambiente cloud.
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 68031ba2ce5c47870c3e60dc469dcda2971b4d87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Rimuovere ruoli di lavoro ibridi di Automazione di Azure

È possibile 

## <a name="removing-hybrid-runbook-worker"></a>Rimozione del ruolo di lavoro ibrido per runbook

È possibile rimuovere uno o più ruoli di lavoro ibrido per runbook da un gruppo o è possibile rimuovere il gruppo, a seconda dei requisiti.  Per rimuovere un ruolo di lavoro ibrido per runbook da un computer locale, seguire questa procedura.

1. Nel portale di Azure passare all'account di Automazione.  
2. Nel pannello **Impostazioni** selezionare **Chiavi** e prendere nota dei valori per il campo **URL** e **Chiave di accesso primaria**.  Queste informazioni saranno necessarie per il passaggio successivo.
3. Aprire una sessione di PowerShell in modalità amministratore ed eseguire il comando seguente - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Per un log dettagliato del processo di rimozione, usare l'opzione **-Verbose** .

> [!NOTE]
> Questa operazione non rimuove Microsoft Monitoring Agent dal computer, ma solo la funzionalità e la configurazione del ruolo di lavoro ibrido per runbook.  

## <a name="remove-hybrid-worker-groups"></a>Rimuovere gruppi di ruoli di lavoro ibridi

Per rimuovere un gruppo, è innanzitutto necessario rimuovere il ruolo di lavoro ibrido per runbook da ogni computer membro del gruppo usando la procedura descritta in precedenza e quindi attenersi alla procedura seguente per rimuovere il gruppo.  

1. Nel portale di Azure aprire l'account di automazione.
1. Selezionare il riquadro **Gruppi di ruoli di lavoro ibridi** e nel pannello **Gruppi di lavoro ibridi** selezionare il gruppo che si desidera eliminare.  Dopo aver selezionato il gruppo specifico, viene visualizzato il pannello delle proprietà del **Gruppo di lavoro ibrido**.<br> ![Pannello Gruppi di ruoli di lavoro ibridi ](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
1. Nel pannello delle proprietà del gruppo selezionato fare clic su **Elimina**.  Viene visualizzato un messaggio che chiede di confermare l'azione. Selezionare **Sì** se si è certi che si vuole procedere.<br> ![Finestra di dialogo di conferma dell'eliminazione del gruppo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.
* Per informazioni sull'installazione di ruoli di lavoro ibridi per runbook di Windows, vedere [Ruolo di lavoro ibrido per runbook Windows di Automazione di Azure](automation-windows-hrw-install.md).
* Per informazioni sull'installazione di ruoli di lavoro ibridi per runbook di Linux, vedere [Ruolo di lavoro ibrido per runbook Linux di Automazione di Azure](automation-linux-hrw-install.md).