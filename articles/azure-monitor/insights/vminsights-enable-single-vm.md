---
title: Abilitare Monitoraggio di Azure per le macchine virtuali nel portale di AzureEnable Azure Monitor for VMs in the Azure portal
description: Informazioni su come valutare Monitoraggio di Azure per le macchine virtuali in una singola macchina virtuale di Azure o in un set di scalabilità di macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480709"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Abilitare Monitoraggio di Azure per le macchine virtuali nel portale di AzureEnable Azure Monitor for VMs in the Azure portal

Questo articolo descrive come abilitare Monitoraggio di Azure per macchine virtuali in un numero ridotto di macchine virtuali di Azure usando il portale di Azure.This article describes how to enable Azure Monitor for VMs on a small number of Azure virtual machines (VMs) using the Azure portal. L'obiettivo è monitorare le macchine virtuali e individuare eventuali problemi di prestazioni o disponibilità. 

Prima di iniziare, esaminare i [prerequisiti](vminsights-enable-overview.md) e assicurarsi che la sottoscrizione e le risorse soddisfino i requisiti.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Abilitare il monitoraggio per una singola macchina virtuale di AzureEnable monitoring for a single Azure VM
Per abilitare il monitoraggio della macchina virtuale di Azure:To enable monitoring of your Azure VM:

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Selezionare **Macchine virtuali**.

1. Selezionare una macchina virtuale dall'elenco.

1. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Informazioni dettagliate** e quindi **Abilita.**

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  

    Nell'elenco sono preselezionati l'area di lavoro predefinita e la località in cui è distribuita la macchina virtuale nella sottoscrizione. 

    >[!NOTE]
    >Per creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio della macchina virtuale, vedere [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). L'area di lavoro Log Analytics deve risiedere in una delle [aree supportate](vminsights-enable-overview.md#log-analytics).

6. Durante l'esecuzione della configurazione verranno visualizzati messaggi di stato.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Abilitare il monitoraggio per un singolo set di scalabilità di macchine virtualiEnable monitoring for a single virtual machine scale set

Per abilitare il monitoraggio del set di scalabilità delle macchine virtuali di Azure:To enable monitoring of your Azure virtual machine scale set:

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Selezionare Set di **scalabilità macchina virtuale**.

3. Nell'elenco selezionare un set di scalabilità di macchine virtuali.

4. Nella sezione **Monitoraggio** della pagina del set di scalabilità della macchina virtuale selezionare **Insights** e quindi **Abilita**.

5. Nella pagina **Insights,** se si vuole usare un'area di lavoro di Log Analytics esistente, selezionarla nell'elenco a discesa.

    L'elenco preseleziona l'area di lavoro predefinita e il percorso in cui viene distribuita la macchina virtuale nella sottoscrizione. 

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per un set di scalabilità di macchine virtualiEnable Azure Monitor for VMs for a virtual machine scale set](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Per creare una nuova area di lavoro di Log Analytics per archiviare i dati di monitoraggio dal set di scalabilità della macchina virtuale, vedere [Creare un'area](../learn/quick-create-workspace.md)di lavoro di Log Analytics. L'area di lavoro Log Analytics deve risiedere in una delle [aree supportate](vminsights-enable-overview.md#log-analytics).

6. Durante l'esecuzione della configurazione verranno visualizzati messaggi di stato.

    >[!NOTE]
    >Se si utilizza un modello di aggiornamento manuale per il set di scalabilità, aggiornare le istanze per completare la configurazione. È possibile avviare gli aggiornamenti dalla pagina **Istanze,** nella sezione **Impostazioni.**
    
    ![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Dopo aver abilitato il monitoraggio per la macchina virtuale o il set di scalabilità di macchine virtuali, le informazioni di monitoraggio sono disponibili per l'analisi in Monitoraggio di Azure per le macchine virtuali. 

## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare le dipendenze rilevate dall'applicazione, vedere Usare Monitoraggio di Azure per il mapping di [macchine virtuali.](vminsights-maps.md) 
* Per identificare i colli di bottiglia, l'utilizzo complessivo e le prestazioni della macchina virtuale, vedere [Visualizzare le prestazioni delle macchine virtuali](vminsights-performance.md)di Azure.To identify bottlenecks, overall utilization, and the VM's performance, see View Azure VM performance .
