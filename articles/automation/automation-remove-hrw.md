---
title: Rimuovere ruoli di lavoro ibridi di Automazione di Azure
description: Questo articolo fornisce informazioni sulla gestione di un ruolo di lavoro ibrido per runbook di Automazione di Azure distribuito che consente di eseguire i runbook sui computer nel data center locale o nell'ambiente cloud.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0b8f951bbd9712e3d20c3286ef3571e287499c68
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Rimuovere ruoli di lavoro ibridi di Automazione di Azure

La funzionalità Ruolo di lavoro ibrido per runbook di Automazione di Azure consente di eseguire runbook direttamente sul computer che ospita il ruolo e su risorse nell'ambiente per gestire tali risorse locali. In questo articolo viene illustrata la procedura necessaria per rimuovere un ruolo di lavoro ibrido da un computer locale.

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
1. In **Automazione processi** selezionare **Gruppi di lavoro ibridi**. Selezionare il gruppo da eliminare.  Dopo aver selezionato il gruppo specifico, viene visualizzato il pannello delle proprietà del **Gruppo di lavoro ibrido**.<br> ![Pannello Gruppi di ruoli di lavoro ibridi ](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
2. Nel pannello delle proprietà del gruppo selezionato fare clic su **Elimina**.  Viene visualizzato un messaggio che chiede di confermare l'azione. Selezionare **Sì** se si è certi che si vuole procedere.<br> ![Finestra di dialogo di conferma dell'eliminazione del gruppo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [Esecuzione dei runbook per Hybrid Runbook Workers](automation-hrw-run-runbooks.md) per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud.
* Per informazioni sull'installazione di ruoli di lavoro ibridi per runbook di Windows, vedere [Ruolo di lavoro ibrido per runbook Windows di Automazione di Azure](automation-windows-hrw-install.md).
* Per informazioni sull'installazione di ruoli di lavoro ibridi per runbook di Linux, vedere [Ruolo di lavoro ibrido per runbook Linux di Automazione di Azure](automation-linux-hrw-install.md).