---
title: Abilitazione di Gestione aggiornamenti di Automazione di Azure da un account di Automazione
description: Questo articolo illustra come abilitare Gestione aggiornamenti da un account di Automazione.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: a521ff690f59b6beafd1113b177b43193dc7447e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743997"
---
# <a name="enable-update-management-from-an-automation-account"></a>Abilitare Gestione aggiornamenti da un account di Automazione

Questo articolo descrive come usare il proprio account di Automazione per abilitare la funzionalità [Gestione aggiornamenti](automation-update-management.md) per le macchine virtuali dell'ambiente. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Gestione aggiornamenti. 

> [!NOTE]
> Quando si abilita Gestione aggiornamenti, sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com.

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Scegliere l'area di lavoro Log Analytics e l'account di Automazione da usare e fare clic su **Abilita** per abilitare Gestione aggiornamenti. L'installazione richiede fino a 15 minuti.

    ![Abilitare la gestione degli aggiornamenti](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Controllo della configurazione dell'ambito

Gestione aggiornamenti usa una configurazione dell'ambito all'interno dell'area di lavoro per individuare i computer nei quali abilitare la funzionalità. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della funzionalità a computer specifici. Per altre informazioni vedere [Usare configurazioni dell'ambito per Gestione aggiornamenti](automation-scope-configurations-update-management.md).

## <a name="enable-azure-vms"></a>Abilitare le macchine virtuali di Azure

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Fare clic su **+ Aggiungi macchine virtuali di Azure** e selezionare una o più macchine virtuali dall'elenco. Le macchine virtuali che non possono essere abilitate sono visualizzate in grigio e non sono selezionabili. Le macchine virtuali possono trovarsi in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione. 

3. Fare clic su **Abilita** per aggiungere le macchine virtuali selezionate alla ricerca salvata del gruppo di computer per la funzionalità.

    ![Abilitare le macchine virtuali di Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Abilitazione di macchine virtuali non di Azure

I computer non in Azure devono essere aggiunti manualmente. 

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Fare clic su **Aggiungi computer non di Azure**. Questa azione apre una nuova finestra del browser con [istruzioni per installare e configurare l'agente di Log Analytics per Windows](../azure-monitor/platform/log-analytics-agent.md) in modo che il computer possa iniziare a segnalare le operazioni di Gestione aggiornamenti. In caso di abilitazione di un computer attualmente gestito da Operations Manager, non è richiesto un nuovo agente e le informazioni sull'area di lavoro vengono inserite nell'agente esistente.

## <a name="enable-machines-in-the-workspace"></a>Abilitazione dei computer nell'area di lavoro

I computer installati manualmente o che inviano già report all'area di lavoro devono essere aggiunti a Automazione di Azure per abilitare Gestione aggiornamenti. 

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Selezionare **Gestisci computer**. Il pulsante **Gestisci computer** può essere disabilitato se in precedenza si è scelta l'opzione **Abilita in tutti i computer disponibili e futuri**

    ![Ricerche salvate](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Per abilitare Gestione aggiornamenti per tutti i computer disponibili selezionare **Abilita in tutti i computer disponibili** nella pagina Gestisci computer. Con questa azione viene disabilitato il controllo per aggiungere computer singolarmente. Questa attività aggiunge tutti i nomi dei computer che inviano report all'area di lavoro alla query di ricerca salvata nel gruppo di computer. Se l'opzione è selezionata, il pulsante **Gestisci computer** viene disabilitato.

5. Per abilitare la soluzione per tutti i computer disponibili e per tutti i computer futuri, selezionare **Abilita in tutti i computer disponibili e futuri**. Questa opzione elimina le ricerche salvate e le configurazioni dell'ambito dall'area di lavoro e avvia la funzione per tutti i computer Azure e non Azure che inviano report all'area di lavoro. Se selezionata, questa azione disabilita il pulsante **Gestisci computer** in modo permanente poiché non è più presente alcuna configurazione dell'ambito.

6. Se necessario è possibile aggiungere di nuovo le configurazioni dell'ambito aggiungendo nuovamente le ricerche salvate iniziali. Per altre informazioni vedere [Usare configurazioni dell'ambito per Gestione aggiornamenti](automation-scope-configurations-update-management.md).

7. Per abilitare la funzionalità per uno o più computer, selezionare **Abilita nei computer selezionati** e fare clic su **Aggiungi** accanto a ogni computer da abilitare per la funzionalità. Questa attività aggiunge i nomi dei computer selezionati alla query di ricerca salvata nel gruppo di computer per la funzionalità.

## <a name="next-steps"></a>Passaggi successivi

* Per usare Gestione aggiornamenti per le macchine virtuali, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
* Per le configurazioni dell'ambito, vedere [Usare configurazioni dell'ambito per Gestione aggiornamenti](automation-scope-configurations-update-management.md).
* Se l'area di lavoro Log Analytics non è più necessaria, vedere le istruzioni riportate in [Scollegare l'area di lavoro dall'account di Automazione per Gestione aggiornamenti](automation-unlink-workspace-update-management.md).
* Per eliminare macchine virtuali da Gestione aggiornamenti, vedere [Rimuovere macchine virtuali da Gestione aggiornamenti](automation-remove-vms-from-update-management.md).
* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](troubleshoot/update-management.md).
* Per risolvere i problemi relativi all'agente di Windows Update, vedere [Risolvere i problemi relativi all'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per risolvere i problemi relativi all'agente di aggiornamento di Linux, vedere[Risolvere i problemi relativi all'agente di aggiornamento Linux](troubleshoot/update-agent-issues-linux.md).
