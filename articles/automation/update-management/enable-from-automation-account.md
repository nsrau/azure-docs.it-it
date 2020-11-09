---
title: Abilitazione di Gestione aggiornamenti di Automazione di Azure da un account di Automazione
description: Questo articolo illustra come abilitare Gestione aggiornamenti da un account di Automazione.
services: automation
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: b97e1e61401697204f79004e4678e6f2286f4a98
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380539"
---
# <a name="enable-update-management-from-an-automation-account"></a>Abilitare Gestione aggiornamenti da un account di Automazione

Questo articolo descrive come usare l'account di automazione per abilitare la funzionalità [Gestione aggiornamenti](overview.md) per le macchine virtuali nell'ambiente, inclusi i computer o i server registrati con i [server abilitati per Azure Arc](../../azure-arc/servers/overview.md). Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale di Azure esistente usando Gestione aggiornamenti.

> [!NOTE]
> Quando si abilita Gestione aggiornamenti, sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../index.yml) per gestire i computer.
* Una [macchina virtuale di Azure](../../virtual-machines/windows/quick-create-portal.md), una VM o un server registrato con i server abilitati per Arc. Per le macchine virtuali o i server non di Azure è necessario che l' [agente di log Analytics](../../azure-monitor/platform/log-analytics-agent.md) per Windows o Linux sia installato e che la creazione di report nell'area di lavoro collegata all'account di automazione gestione aggiornamenti sia abilitata in. Si consiglia di installare l'agente di Log Analytics per Windows o Linux connettendo il computer ai [server abilitati per Azure Arc](../../azure-arc/servers/overview.md)e quindi usando i criteri di Azure per assegnare i criteri predefiniti [Distribuisci agente di log Analytics a computer *Linux* o *Windows* Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) . In alternativa, se si prevede di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [abilita monitoraggio di Azure per le macchine virtuali](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Scegliere l'area di lavoro Log Analytics e l'account di automazione e selezionare **Abilita** per abilitare Gestione aggiornamenti. L'installazione richiede fino a 15 minuti.

    ![Abilitare la gestione degli aggiornamenti](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Abilitare le macchine virtuali di Azure

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Selezionare **+ Aggiungi macchine virtuali di Azure** e selezionare una o più VM dall'elenco. Le macchine virtuali che non possono essere abilitate sono visualizzate in grigio e non sono selezionabili. Le macchine virtuali di Azure possono trovarsi in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione.

3. Selezionare **Abilita** per aggiungere le macchine virtuali selezionate alla ricerca salvata nel gruppo di computer per la funzionalità.

    ![Abilitare le macchine virtuali di Azure](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Abilitare macchine virtuali non Azure

Per i computer o i server ospitati all'esterno di Azure, inclusi quelli registrati con i server abilitati per Azure Arc, seguire questa procedura per abilitarli con Gestione aggiornamenti.  

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Selezionare **Aggiungi computer non Azure**. Questa azione consente di aprire una nuova finestra [del browser con le istruzioni per installare e configurare l'agente di log Analytics per Windows](../../azure-monitor/platform/log-analytics-agent.md) in modo che il computer possa iniziare a segnalare gestione aggiornamenti. Se si Abilita un computer attualmente gestito da Operations Manager, non è necessario un nuovo agente. Le informazioni sull'area di lavoro vengono aggiunte alla configurazione degli agenti.

## <a name="enable-machines-in-the-workspace"></a>Abilitazione dei computer nell'area di lavoro

I computer installati manualmente o che inviano già report all'area di lavoro devono essere aggiunti a Automazione di Azure per abilitare Gestione aggiornamenti.

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Selezionare **Gestisci computer**. Il pulsante **Gestisci computer** può essere disabilitato se in precedenza si è scelta l'opzione **Abilita in tutti i computer disponibili e futuri**

    ![Ricerche salvate](media/enable-from-automation-account/managemachines.png)

3. Per abilitare Gestione aggiornamenti per tutti i computer disponibili che inviano report all'area di lavoro, selezionare **Abilita in tutti i computer disponibili** nella pagina Gestisci computer. Questa azione Disabilita il controllo per aggiungere i computer singolarmente e aggiunge tutti i computer che inviano report all'area di lavoro alla query di ricerca salvata nel gruppo di computer `MicrosoftDefaultComputerGroup` . Quando questa opzione è selezionata, questa azione Disabilita l'opzione **Gestisci computer** .

4. Per abilitare la funzionalità per tutti i computer disponibili e per tutti i computer futuri, selezionare **Abilita in tutti i computer disponibili e futuri**. Questa opzione Elimina la configurazione di ricerca e ambito salvata dall'area di lavoro e consente alla funzionalità di includere tutti i computer Azure e non Azure attualmente o in futuro, che possono essere segnalati all'area di lavoro. Quando questa opzione è selezionata, questa azione Disabilita l'opzione **Gestisci computer** in modo permanente, perché non è disponibile alcuna configurazione dell'ambito.

    > [!NOTE]
    > Poiché questa opzione Elimina la ricerca salvata e la configurazione dell'ambito all'interno Log Analytics, è importante rimuovere i blocchi di eliminazione nell'area di lavoro Log Analytics prima di selezionare questa opzione. In caso contrario, l'opzione non riuscirà a rimuovere le configurazioni ed è necessario rimuoverle manualmente.

5. Se necessario, è possibile aggiungere di nuovo la configurazione dell'ambito aggiungendo nuovamente la query di ricerca salvata iniziale. Per altre informazioni, vedere [Limitare l'ambito di distribuzione di Gestione aggiornamenti](scope-configuration.md).

6. Per abilitare la funzionalità per uno o più computer, selezionare **Abilita nei computer selezionati** e selezionare **Aggiungi** accanto a ogni computer. Questa attività aggiunge i nomi dei computer selezionati alla query di ricerca salvata nel gruppo di computer per la funzionalità.

## <a name="next-steps"></a>Passaggi successivi

* Per usare Gestione aggiornamenti per le macchine virtuali, vedere [gestire gli aggiornamenti e le patch per le macchine virtuali](manage-updates-for-vm.md).

* Quando non è più necessario gestire macchine virtuali o server con Gestione aggiornamenti, vedere [rimuovere VM da Gestione aggiornamenti](remove-vms.md).

* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](../troubleshoot/update-management.md).
