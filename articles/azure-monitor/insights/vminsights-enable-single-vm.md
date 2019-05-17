---
title: Abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) per la valutazione | Microsoft Docs
description: Questo articolo descrive come si abilita monitoraggio di Azure per le macchine virtuali per una singola macchina virtuale di Azure o per finalità di valutazione del set di scalabilità di macchine virtuali.
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
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524086"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Abilitare il monitoraggio di Azure per le macchine virtuali (anteprima) per la valutazione

Per valutare il monitoraggio di Azure per le macchine virtuali (anteprima) in un numero ridotto di macchine virtuali di Azure o una singola macchina virtuale o un set di scalabilità di macchine virtuali, l'approccio più semplice e più diretto per abilitare il monitoraggio sia dal portale di Azure. Alla fine di questo processo, verrà è stata avviata la loro di monitoraggio e Scopri se si verificano problemi di prestazioni o disponibilità. 

Prima di Guida introduttiva, è consigliabile consultare il [prerequisiti](vminsights-enable-overview.md) e verificare la sottoscrizione e le risorse soddisfino i requisiti.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Abilitare il monitoraggio per una singola macchina virtuale di Azure
Per abilitare il monitoraggio della macchina virtuale di Azure nel portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Macchine virtuali**.

1. Selezionare una macchina virtuale dall'elenco.

1. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)**.

1. Nella pagina **Insights (anteprima)** selezionare **Prova adesso**.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. Se nella pagina **Azure Monitor Insights Onboarding** (Onboarding di Insights per Monitoraggio di Azure) è già presente un'area di lavoro Log Analytics nella stessa sottoscrizione, selezionarla nell'elenco a discesa.  

    Nell'elenco sono preselezionate l'area di lavoro e la località predefinite in cui è distribuita la macchina virtuale nella sottoscrizione. 

    >[!NOTE]
    >Se si desidera creare una nuova area di lavoro di Log Analitica per archiviare i dati di monitoraggio dalla macchina virtuale, seguire le istruzioni in [creare un'area di lavoro di Log Analitica](../../azure-monitor/learn/quick-create-workspace.md) in una delle aree supportate elencate [qui](vminsights-enable-overview.md#log-analytics).

Dopo aver abilitato il monitoraggio, possono essere necessari circa 10 minuti prima di poter visualizzare le metriche relative all'integrità per la macchina virtuale.

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Abilitare il monitoraggio per un set di scalabilità di macchina virtuale singola

Per abilitare il monitoraggio del set di scalabilità macchine virtuali di Azure impostata nel portale di Azure, eseguire le operazioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **set di scalabilità di macchine virtuali**.

3. Nell'elenco, selezionare un set di scalabilità di macchine virtuali.

4. Nella macchina virtuale di set di scalabilità di pagina, nelle **Monitoring** sezione, selezionare **Insights (anteprima)**.

5. Nel **Insights (anteprima)** se si dispone di un'area di lavoro di Log Analitica esistente che si desidera usare, selezionarla nell'elenco a discesa scegliere pagina.

    Nell'elenco sono preselezionate l'area di lavoro e la località predefinite in cui è distribuita la macchina virtuale nella sottoscrizione. 

    ![Abilitare il monitoraggio di Azure per le macchine virtuali per un set di scalabilità di macchine virtuali](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >Se si desidera creare una nuova area di lavoro di Log Analitica per archiviare i dati di monitoraggio dalla macchina virtuale, seguire le istruzioni in [creare un'area di lavoro di Log Analitica](../learn/quick-create-workspace.md) in una delle aree supportate elencate [qui](vminsights-enable-overview.md#log-analytics).

Dopo aver abilitato il monitoraggio, potrebbe richiedere circa 10 minuti prima di poter visualizzare i dati di monitoraggio per il set di scalabilità.

>[!NOTE]
>Se si usa un modello di aggiornamento manuale per il set di scalabilità che sarà necessario aggiornare le istanze per completare l'installazione.  Questa operazione può essere eseguita dalla pagina delle istanze sotto il **impostazioni** sezione.

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è abilitato per la macchina virtuale o un set di scalabilità di macchine virtuali, queste informazioni sono disponibili per l'analisi con monitoraggio di Azure per le macchine virtuali. Per informazioni su come usare la funzionalità di integrità, vedere [Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali](vminsights-health.md). Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). Per individuare i colli di bottiglia e l'utilizzo generale con le prestazioni delle macchine virtuali, vedere [Visualizzare le prestazioni delle macchine virtuali di Azure](vminsights-performance.md); per visualizzare le dipendenze dell'applicazione individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).