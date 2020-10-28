---
title: Abilitare Rilevamento modifiche e inventario dall'account di Automazione
description: Questo articolo descrive come abilitare Rilevamento modifiche e inventario da un account di Automazione.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 50188ad5fea0ee34a6896f0045e3bbcbfb553aaa
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677302"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Abilitare Rilevamento modifiche e inventario da un account di Automazione

Questo articolo descrive come usare l'account di automazione per abilitare [rilevamento modifiche e l'inventario](overview.md) per le macchine virtuali nell'ambiente. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Rilevamento modifiche e inventario.

> [!NOTE]
> Quando si abilita Rilevamento modifiche e inventario, sono supportate solo determinate aree geografiche per il collegamento a un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../index.yml) per gestire i computer.
* Una [macchina virtuale](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

1. Passare all'account di Automazione e selezionare **Inventario** o **Rilevamento modifiche** in **Gestione della configurazione** .

2. Scegliere l'area di lavoro Log Analytics e l'account di Automazione e fare clic su **Abilita** per abilitare Rilevamento modifiche e inventario. L'installazione richiede fino a 15 minuti.

    ![Abilitare il rilevamento delle modifiche e l'inventario](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Abilitare le macchine virtuali di Azure

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione** .

2. Fare clic su **+ Aggiungi macchine virtuali di Azure** e selezionare una o più macchine virtuali dall'elenco. Le macchine virtuali che non possono essere abilitate sono visualizzate in grigio e non sono selezionabili. Le macchine virtuali di Azure possono trovarsi in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione. 

3. Fare clic su **Abilita** per aggiungere le macchine virtuali selezionate alla ricerca salvata del gruppo di computer per la funzionalità. Per altre informazioni, vedere [Limit rilevamento modifiche and Inventory Deployment scope](manage-scope-configurations.md).

      [![Abilitare le macchine virtuali di Azure](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Abilitare macchine virtuali non Azure

I computer non in Azure devono essere aggiunti manualmente. Si consiglia di installare l'agente di Log Analytics per Windows o Linux connettendo il computer ai [server abilitati per Azure Arc](../../azure-arc/servers/overview.md)e quindi usando i criteri di Azure per assegnare l' [agente di distribuzione log Analytics a un criterio predefinito per computer *Linux* o *Windows* Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) . Se si prevede anche di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [Enable monitoraggio di Azure per le macchine virtuali](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione** .

2. Fare clic su **Aggiungi computer non di Azure** . Questa azione apre una nuova finestra del browser con [istruzioni per installare e configurare l'agente Log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md) in modo che il computer possa iniziare a segnalare le operazioni di Rilevamento modifiche e inventario. In caso di abilitazione di un computer attualmente gestito da Operations Manager, non è richiesto un nuovo agente e le informazioni sull'area di lavoro vengono inserite nell'agente esistente.

## <a name="enable-machines-in-the-workspace"></a>Abilitare i computer nell'area di lavoro

I computer installati manualmente o i computer che inviano già report all'area di lavoro devono essere aggiunti ad Automazione di Azure per l'abilitazione di Rilevamento modifiche e inventario.

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione** .

2. Selezionare **Gestisci computer** . L'opzione **Gestisci computer** potrebbe essere disabilitata se in precedenza è stata selezionata l'opzione **Abilita in tutti i computer disponibili e futuri**

    ![Ricerche salvate](media/enable-from-automation-account/manage-machines.png)

3. Per abilitare Rilevamento modifiche e l'inventario per tutti i computer disponibili, selezionare **Abilita in tutti i computer disponibili** nella pagina **Gestisci computer** . Questa azione Disabilita il controllo per aggiungere i computer singolarmente e aggiunge tutti i computer che inviano report all'area di lavoro alla query di ricerca salvata nel gruppo di computer. Quando questa opzione è selezionata, questa azione Disabilita l'opzione **Gestisci computer** .

4. Per abilitare la funzionalità per tutti i computer disponibili e per tutti i computer futuri, selezionare **Abilita in tutti i computer disponibili e futuri** . Questa opzione consente di eliminare la configurazione di ricerca e ambito salvata dall'area di lavoro e di aprire la funzionalità per tutti i computer Azure e non Azure che inviano report all'area di lavoro. Quando questa opzione è selezionata, questa azione Disabilita l'opzione **Gestisci computer** in modo permanente, perché non è stata lasciata alcuna configurazione dell'ambito.

    > [!NOTE]
    > Poiché questa opzione Elimina la ricerca salvata e la configurazione dell'ambito all'interno Log Analytics, è importante rimuovere i blocchi di eliminazione nell'area di lavoro Log Analytics prima di selezionare questa opzione. In caso contrario, l'opzione non riuscirà a rimuovere le configurazioni ed è necessario rimuoverle manualmente.

5. Se necessario, è possibile aggiungere di nuovo la configurazione dell'ambito aggiungendo nuovamente la ricerca salvata iniziale. Per altre informazioni, vedere [Limit rilevamento modifiche and Inventory Deployment scope](manage-scope-configurations.md).

6. Per abilitare la funzionalità per uno o più computer, selezionare **Abilita nei computer selezionati** e fare clic su **Aggiungi** accanto a ogni computer da abilitare per la funzionalità. Questa attività aggiunge i nomi dei computer selezionati alla query di ricerca salvata nel gruppo di computer per la funzionalità.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso della funzionalità, vedere [gestire rilevamento modifiche](manage-change-tracking.md) e [gestire l'inventario](manage-inventory-vms.md).

* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](../troubleshoot/change-tracking.md).
