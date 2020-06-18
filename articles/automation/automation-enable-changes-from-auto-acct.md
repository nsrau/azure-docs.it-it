---
title: Abilitare Rilevamento modifiche e inventario dall'account di Automazione
description: Questo articolo descrive come abilitare Rilevamento modifiche e inventario da un account di Automazione.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 28c61e637a37c158dcd80c0f02b748b4813945fb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826843"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Abilitare Rilevamento modifiche e inventario da un account di Automazione

Questo articolo descrive come usare l'account di Automazione per abilitare la funzionalità [Rilevamento modifiche e inventario](change-tracking.md) per le macchine virtuali dell'ambiente. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Rilevamento modifiche e inventario. 

> [!NOTE]
> Quando si abilita Rilevamento modifiche e inventario, sono supportate solo determinate aree geografiche per il collegamento a un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

1. Passare all'account di Automazione e selezionare **Inventario** o **Rilevamento modifiche** in **Gestione della configurazione**.

2. Scegliere l'area di lavoro Log Analytics e l'account di Automazione e fare clic su **Abilita** per abilitare Rilevamento modifiche e inventario. L'installazione richiede fino a 15 minuti.

    ![Abilitare il rilevamento delle modifiche e l'inventario](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Controllare la configurazione dell'ambito

Rilevamento modifiche e inventario usa una configurazione dell'ambito all'interno dell'area di lavoro per individuare i computer che ricevono le modifiche. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della funzionalità a computer specifici. Per altre informazioni, vedere [Usare configurazioni dell'ambito per Rilevamento modifiche e inventario](automation-scope-configurations-change-tracking.md).

## <a name="enable-azure-vms"></a>Abilitare le macchine virtuali di Azure

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione**.

2. Fare clic su **+ Aggiungi macchine virtuali di Azure** e selezionare una o più macchine virtuali dall'elenco. Le macchine virtuali che non possono essere abilitate sono visualizzate in grigio e non sono selezionabili. Le macchine virtuali di Azure possono trovarsi in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione. 

3. Fare clic su **Abilita** per aggiungere le macchine virtuali selezionate alla ricerca salvata del gruppo di computer per la funzionalità. Per altre informazioni, vedere [Usare configurazioni dell'ambito per Rilevamento modifiche e inventario](automation-scope-configurations-change-tracking.md).

    ![Abilitare le macchine virtuali di Azure](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Abilitare macchine virtuali non Azure

I computer non in Azure devono essere aggiunti manualmente. 

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione**.

2. Fare clic su **Aggiungi computer non di Azure**. Questa azione apre una nuova finestra del browser con [istruzioni per installare e configurare l'agente Log Analytics per Windows](../azure-monitor/platform/log-analytics-agent.md) in modo che il computer possa iniziare a segnalare le operazioni di Rilevamento modifiche e inventario. In caso di abilitazione di un computer attualmente gestito da Operations Manager, non è richiesto un nuovo agente e le informazioni sull'area di lavoro vengono inserite nell'agente esistente.

## <a name="enable-machines-in-the-workspace"></a>Abilitare i computer nell'area di lavoro

I computer installati manualmente o i computer che inviano già report all'area di lavoro devono essere aggiunti ad Automazione di Azure per l'abilitazione di Rilevamento modifiche e inventario. 

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione**.

2. Selezionare **Gestisci computer**. Il pulsante **Gestisci computer** può essere disabilitato se in precedenza si è scelta l'opzione **Abilita in tutti i computer disponibili e futuri**

    ![Ricerche salvate](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. Per abilitare Rilevamento modifiche e inventario per tutti i computer disponibili, selezionare **Abilita in tutti i computer disponibili** nella pagina Gestisci computer. Con questa azione viene disabilitato il controllo per aggiungere computer singolarmente. Questa attività aggiunge tutti i nomi dei computer che inviano report all'area di lavoro alla query di ricerca salvata nel gruppo di computer. Se l'opzione è selezionata, il pulsante **Gestisci computer** viene disabilitato.

4. Per abilitare la funzionalità per tutti i computer disponibili e per tutti i computer futuri, selezionare **Abilita in tutti i computer disponibili e futuri**. Questa opzione elimina le ricerche salvate e le configurazioni dell'ambito dall'area di lavoro e avvia la funzionalità per tutti i computer Azure e non Azure che inviano report all'area di lavoro. Se selezionata, questa azione disabilita il pulsante **Gestisci computer** in modo permanente perché non è più presente alcuna configurazione dell'ambito.

5. Se necessario è possibile aggiungere di nuovo le configurazioni dell'ambito aggiungendo nuovamente le ricerche salvate iniziali. Per altre informazioni, vedere [Usare configurazioni dell'ambito per Rilevamento modifiche e inventario](automation-scope-configurations-change-tracking.md).

6. Per abilitare la funzionalità per uno o più computer, selezionare **Abilita nei computer selezionati** e fare clic su **Aggiungi** accanto a ogni computer da abilitare per la funzionalità. Questa attività aggiunge i nomi dei computer selezionati alla query di ricerca salvata nel gruppo di computer per la funzionalità.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sulla funzionalità, vedere [Gestire Rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per informazioni sulle configurazioni dell'ambito, vedere [Usare configurazioni dell'ambito per Rilevamento modifiche e inventario](automation-scope-configurations-change-tracking.md).
* Per informazioni su come usare la funzionalità per identificare il software installato nell'ambiente, vedere [Individuare il software installato nelle VM](automation-tutorial-installed-software.md).
* Se non si vuole integrare l'account di Automazione con un'area di lavoro Log Analytics quando si abilita la funzionalità, vedere [Scollegare l'area di lavoro dall'account di Automazione](automation-unlink-workspace-change-tracking.md).
* Al termine della distribuzione delle modifiche nelle macchine virtuali, è possibile rimuoverle come descritto in [Rimuovere macchine virtuali da Rilevamento modifiche e inventario](automation-remove-vms-from-change-tracking.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).