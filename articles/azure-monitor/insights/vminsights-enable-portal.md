---
title: Abilitare monitoraggio di Azure per una singola macchina virtuale o un set di scalabilità di macchine virtuali nella portale di Azure
description: Informazioni su come abilitare Monitoraggio di Azure per le macchine virtuali in una singola macchina virtuale o un set di scalabilità di macchine virtuali di Azure usando il portale di Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ba6ae9262fb0e55c53e1b1421c075e555fae8d98
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328237"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Abilitare monitoraggio di Azure per una singola macchina virtuale o un set di scalabilità di macchine virtuali nella portale di Azure
Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale o un set di scalabilità di macchine virtuali usando il portale di Azure. Questa procedura può essere utilizzata per gli elementi seguenti:

- Macchina virtuale di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchina virtuale ibrida connessa ad Azure Arc

## <a name="prerequisites"></a>Prerequisiti

- [Creare e configurare un'area di lavoro log Analytics](vminsights-configure-workspace.md). In alternativa, è possibile creare una nuova area di lavoro durante questo processo.
- Vedere [sistemi operativi supportati](vminsights-enable-overview.md#supported-operating-systems) per assicurarsi che il sistema operativo della macchina virtuale o del set di scalabilità di macchine virtuali che si sta abilitando sia supportato. 

## <a name="enable-azure-monitor-for-vms"></a>Abilita Monitoraggio di Azure per le macchine virtuali

Dal portale di Azure selezionare **macchine virtuali**, set di **scalabilità**di macchine virtuali o **computer-Azure Arc** e selezionare una risorsa nell'elenco. Nella sezione **monitoraggio** del menu selezionare **Insights** e quindi **Enable**. L'esempio seguente mostra una macchina virtuale di Azure, ma il menu è simile per il set di scalabilità di macchine virtuali di Azure o Azure Arc.

![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

Se la macchina virtuale non è già connessa a un'area di lavoro Log Analytics, verrà chiesto di selezionarne una. Se in precedenza non è stata [creata un'area di lavoro](../../azure-monitor/learn/quick-create-workspace.md), è possibile selezionare un valore predefinito per il percorso in cui è distribuita la macchina virtuale o il set di scalabilità di macchine virtuali nella sottoscrizione. Questa area di lavoro verrà creata e configurata se non esiste già. Se si seleziona un'area di lavoro esistente, viene configurata per Monitoraggio di Azure per le macchine virtuali se non è già stata eseguita.

> [!NOTE]
> Se si seleziona un'area di lavoro non configurata in precedenza per Monitoraggio di Azure per le macchine virtuali, l'Management Pack *VMInsights* verrà aggiunta all'area di lavoro. Questa operazione verrà applicata a qualsiasi agente già connesso all'area di lavoro, indipendentemente dal fatto che sia abilitato o meno per Monitoraggio di Azure per le macchine virtuali. I dati sulle prestazioni verranno raccolti da queste macchine virtuali e archiviati nella tabella *InsightsMetrics* .

![Selezionare l'area di lavoro](media/vminsights-configure-workspace/select-workspace.png)

Si riceveranno messaggi di stato durante l'esecuzione della configurazione.

>[!NOTE]
>Se si usa un modello di aggiornamento manuale per il set di scalabilità di macchine virtuali, aggiornare le istanze per completare la configurazione. È possibile avviare gli aggiornamenti dalla pagina **istanze** , nella sezione **Impostazioni** .

![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Passaggi successivi

* Vedere [usare monitoraggio di Azure per le macchine virtuali mappa](vminsights-maps.md) per visualizzare le dipendenze dell'applicazione individuate. 
* Vedere [visualizzare le prestazioni delle macchine virtuali di Azure](vminsights-performance.md) per identificare colli di bottiglia, utilizzo complessivo e prestazioni della VM.
