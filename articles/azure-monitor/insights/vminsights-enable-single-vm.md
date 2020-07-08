---
title: Abilitare Monitoraggio di Azure per le macchine virtuali nell'portale di Azure
description: Informazioni su come valutare Monitoraggio di Azure per le macchine virtuali in una singola macchina virtuale di Azure o in un set di scalabilità di macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507059"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Abilitare monitoraggio di Azure per una singola macchina virtuale o VMSS nel portale di Azure
Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per una singola macchina virtuale o un set di scalabilità di macchine virtuali usando il portale di Azure. Questa procedura può essere utilizzata per gli elementi seguenti:

- Macchina virtuale di Azure
- Set di scalabilità di macchine virtuali di Azure
- Computer Azure Arc

Prima di iniziare, esaminare i [prerequisiti](vminsights-enable-overview.md) e verificare che la sottoscrizione e le risorse soddisfino i requisiti.  

## <a name="enable-azure-monitor-for-vms"></a>Abilita Monitoraggio di Azure per le macchine virtuali

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **macchine virtuali**, **set di scalabilità**di macchine virtuali o **computer-Azure Arc**.

1. Selezionare una risorsa dall'elenco.

1. Nella sezione **monitoraggio** del menu selezionare **Insights** e quindi **Enable**. L'esempio seguente mostra una macchina virtuale di Azure, ma il menu è simile per Azure VMSS o Azure Arc.

    ![Abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Se la macchina virtuale non è già connessa a un'area di lavoro Log Analytics, verrà chiesto di selezionarne una. Se in precedenza non è stata [creata un'area di lavoro](../../azure-monitor/learn/quick-create-workspace.md), è possibile selezionare un valore predefinito per il percorso in cui è distribuita la macchina virtuale o vmss nella sottoscrizione. Questa area di lavoro verrà creata e configurata se non esiste già.

2. Si riceveranno messaggi di stato durante l'esecuzione della configurazione.

    >[!NOTE]
    >Se si usa un modello di aggiornamento manuale per il set di scalabilità, aggiornare le istanze per completare la configurazione. È possibile avviare gli aggiornamenti dalla pagina **istanze** , nella sezione **Impostazioni** .

    ![Abilitare Monitoraggio di Azure per le macchine virtuali - Monitoraggio dell'elaborazione della distribuzione](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Passaggi successivi

* Per visualizzare le dipendenze dell'applicazione individuate, vedere [usare monitoraggio di Azure per le macchine virtuali mappa](vminsights-maps.md). 
* Per identificare colli di bottiglia, utilizzo complessivo e prestazioni della VM, vedere visualizzare le [prestazioni delle VM di Azure](vminsights-performance.md).
