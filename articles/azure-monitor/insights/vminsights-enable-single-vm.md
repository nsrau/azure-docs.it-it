---
title: Abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) per la valutazione | Microsoft Docs
description: Informazioni su come valutare monitoraggio di Azure per le macchine virtuali in una singola macchina virtuale di Azure o in un set di scalabilità di macchine virtuali.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122471"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) per la valutazione

È possibile valutare di monitoraggio di Azure per le macchine virtuali (anteprima) in un numero ridotto di macchine virtuali di Azure (VM) o in un unico scalabilità della macchina virtuale o una macchina virtuale impostata. Il modo più semplice e più diretto per attivare il monitoraggio sia dal portale di Azure. L'obiettivo consiste nel monitorare le macchine virtuali e individuare eventuali problemi di prestazioni o disponibilità. 

Prima di iniziare, rivedere le [prerequisiti](vminsights-enable-overview.md) e assicurarsi che la sottoscrizione e tutte le risorse soddisfino i requisiti.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Abilitare il monitoraggio per una singola macchina virtuale di Azure
Per abilitare il monitoraggio della VM di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Macchine virtuali**.

1. Selezionare una macchina virtuale dall'elenco.

1. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)** .

1. Nella pagina **Insights (anteprima)** selezionare **Prova adesso**.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  

    Nell'elenco vengono preselezionate le area di lavoro predefinita e la posizione in cui la macchina virtuale viene distribuita nella sottoscrizione. 

    >[!NOTE]
    >Per creare una nuova area di lavoro di Log Analitica per archiviare i dati di monitoraggio dalla macchina virtuale, vedere [creare un'area di lavoro di Log Analitica](../../azure-monitor/learn/quick-create-workspace.md). L'area di lavoro di Log Analitica deve appartenere a uno dei [aree supportate](vminsights-enable-overview.md#log-analytics).

Dopo aver abilitato il monitoraggio, potrebbe essere necessario attendere circa 10 minuti prima di poter visualizzare le metriche di integrità per la macchina virtuale.

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Abilitare il monitoraggio per un set di scalabilità di macchina virtuale singola

Per abilitare il monitoraggio del set di scalabilità di macchine virtuali di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **set di scalabilità di macchine virtuali**.

3. Nell'elenco, selezionare un set di scalabilità di macchine virtuali.

4. Nella macchina virtuale di set di scalabilità di pagina, nelle **Monitoring** sezione, selezionare **Insights (anteprima)** .

5. Nel **Insights (anteprima)** pagina, se si desidera utilizzare un'area di lavoro di Log Analitica esistente, selezionarlo nell'elenco a discesa.

    Nell'elenco vengono preselezionate le area di lavoro predefinita e la posizione che la VM viene distribuita nella sottoscrizione. 

    ![Abilitare il monitoraggio di Azure per le macchine virtuali per un set di scalabilità di macchine virtuali](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Per creare una nuova area di lavoro di Log Analitica per archiviare i dati di monitoraggio da set di scalabilità di macchine virtuali, vedere [creare un'area di lavoro di Log Analitica](../learn/quick-create-workspace.md). L'area di lavoro di Log Analitica deve appartenere a uno dei [aree supportate](vminsights-enable-overview.md#log-analytics).

Dopo aver abilitato il monitoraggio, potrebbe essere necessario attendere circa 10 minuti prima di poter visualizzare i dati di monitoraggio per il set di scalabilità.

>[!NOTE]
>Se si usa un modello di aggiornamento manuale per il set di scalabilità, aggiornare le istanze per completare l'installazione. È possibile avviare gli aggiornamenti dal **istanze** pagina il **impostazioni** sezione.

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Ora che è stato abilitato il monitoraggio per il set di scalabilità della macchina virtuale o una macchina virtuale, le informazioni di monitoraggio sono disponibile per l'analisi in Monitoraggio di Azure per le macchine virtuali. 

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come usare la funzionalità di integrità, vedere [comprendere l'integrità delle macchine virtuali Azure Monitor](vminsights-health.md). 
* Per visualizzare le dipendenze individuate dell'applicazione, vedere [usare monitoraggio di Azure per la mappa VMs](vminsights-maps.md). 
* Per identificare i colli di bottiglia, l'utilizzo complessivo e le prestazioni della VM, vedere [prestazioni delle VM di Azure Visualizza](vminsights-performance.md).