---
title: Abilita Monitoraggio di Azure per le macchine virtuali (anteprima) per la valutazione | Microsoft Docs
description: Informazioni su come valutare Monitoraggio di Azure per le macchine virtuali in una singola macchina virtuale di Azure o in un set di scalabilità di macchine virtuali.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 05/09/2019
ms.openlocfilehash: 1182f48d2d05c90cc90b1832f9305001dd2d1211
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553813"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Abilita Monitoraggio di Azure per le macchine virtuali (anteprima) per la valutazione

È possibile valutare Monitoraggio di Azure per le macchine virtuali (anteprima) in un numero ridotto di macchine virtuali (VM) di Azure o in una singola macchina virtuale o set di scalabilità di macchine virtuali. Il modo più semplice e diretto per abilitare il monitoraggio è dalla portale di Azure. L'obiettivo consiste nel monitorare le macchine virtuali e individuare eventuali problemi di prestazioni o disponibilità. 

Prima di iniziare, esaminare i [prerequisiti](vminsights-enable-overview.md) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Abilitare il monitoraggio per una singola macchina virtuale di Azure
Per abilitare il monitoraggio della macchina virtuale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Macchine virtuali**.

1. Selezionare una macchina virtuale dall'elenco.

1. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)** .

1. Nella pagina **Insights (anteprima)** selezionare **Prova adesso**.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  

    Nell'elenco sono preselezionati l'area di lavoro predefinita e la località in cui è distribuita la macchina virtuale nella sottoscrizione. 

    >[!NOTE]
    >Per creare una nuova area di lavoro Log Analytics per archiviare i dati di monitoraggio della macchina virtuale, vedere [Creare un'area di lavoro Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). L'area di lavoro Log Analytics deve risiedere in una delle [aree supportate](vminsights-enable-overview.md#log-analytics).

Dopo aver abilitato il monitoraggio, potrebbe essere necessario attendere circa 10 minuti prima di poter visualizzare le metriche di integrità per la macchina virtuale.

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Abilitare il monitoraggio per un singolo set di scalabilità di macchine virtuali

Per abilitare il monitoraggio del set di scalabilità di macchine virtuali di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **set di scalabilità di macchine virtuali**.

3. Nell'elenco selezionare un set di scalabilità di macchine virtuali.

4. Nella sezione **monitoraggio** della pagina del set di scalabilità di macchine virtuali selezionare **Insights (anteprima)** .

5. Nella pagina **Insights (anteprima)** , se si vuole usare un'area di lavoro log Analytics esistente, selezionarla nell'elenco a discesa.

    L'elenco preseleziona l'area di lavoro predefinita e la posizione in cui è distribuita la macchina virtuale nella sottoscrizione. 

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per un set di scalabilità di macchine virtuali](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Per creare una nuova area di lavoro di Log Analytics per archiviare i dati di monitoraggio dal set di scalabilità di macchine virtuali, vedere [creare un'area di lavoro di log Analytics](../learn/quick-create-workspace.md). L'area di lavoro Log Analytics deve risiedere in una delle [aree supportate](vminsights-enable-overview.md#log-analytics).

Dopo aver abilitato il monitoraggio, potrebbe essere necessario attendere circa 10 minuti prima di poter visualizzare i dati di monitoraggio per il set di scalabilità.

>[!NOTE]
>Se si usa un modello di aggiornamento manuale per il set di scalabilità, aggiornare le istanze per completare la configurazione. È possibile avviare gli aggiornamenti dalla pagina **istanze** , nella sezione **Impostazioni** .

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Ora che è stato abilitato il monitoraggio per la VM o il set di scalabilità di macchine virtuali, le informazioni di monitoraggio sono disponibili per l'analisi in Monitoraggio di Azure per le macchine virtuali. 

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come usare la funzionalità di integrità, vedere informazioni [sull'integrità delle macchine virtuali di monitoraggio di Azure](vminsights-health.md). 
* Per visualizzare le dipendenze dell'applicazione individuate, vedere [usare monitoraggio di Azure per le macchine virtuali mappa](vminsights-maps.md). 
* Per identificare colli di bottiglia, utilizzo complessivo e prestazioni della VM, vedere visualizzare le [prestazioni delle VM di Azure](vminsights-performance.md).