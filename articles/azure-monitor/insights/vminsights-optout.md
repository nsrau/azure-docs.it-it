---
title: Disabilitare il monitoraggio in Monitoraggio di Azure per le macchine virtualiDisable monitoring in Azure Monitor for VMs
description: Questo articolo descrive come interrompere il monitoraggio delle macchine virtuali in Monitoraggio di Azure per le macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480522"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Disabilitare il monitoraggio delle macchine virtuali in Monitoraggio di Azure per le macchine virtualiDisable monitoring of your VMs in Azure Monitor for VMs

Dopo aver abilitato il monitoraggio delle macchine virtuali (VM), è possibile scegliere in un secondo momento di disabilitare il monitoraggio in Monitoraggio di Azure per le macchine virtuali. Questo articolo illustra come disabilitare il monitoraggio per una o più macchine virtuali.  

Attualmente, Monitoraggio di Azure per le macchine virtuali non supporta la disabilitazione selettiva del monitoraggio delle macchine virtuali. L'area di lavoro di Log Analytics potrebbe supportare Monitoraggio di Azure per macchine virtuali e altre soluzioni. Potrebbe anche raccogliere altri dati di monitoraggio. Se l'area di lavoro di Log Analytics fornisce questi servizi, è necessario comprendere l'effetto e i metodi di disabilitazione del monitoraggio prima di iniziare.

Monitoraggio di Azure per le macchine virtuali si basa sui componenti seguenti per offrire:

* Un'area di lavoro di Log Analytics, che archivia i dati di monitoraggio provenienti da macchine virtuali e altre origini.
* Raccolta di contatori delle prestazioni configurati nell'area di lavoro. La raccolta aggiorna la configurazione di monitoraggio in tutte le macchine virtuali connesse all'area di lavoro.
* `VMInsights`, che è una soluzione di monitoraggio configurata nell'area di lavoro. Questa soluzione aggiorna la configurazione di monitoraggio in tutte le macchine virtuali connesse all'area di lavoro.
* `MicrosoftMonitoringAgent`e `DependencyAgent`, che sono estensioni di macchine virtuali di Azure. Queste estensioni raccolgono e inviano dati all'area di lavoro.

Durante la preparazione per disabilitare il monitoraggio delle macchine virtuali, tenere presenti le considerazioni seguenti:As prepare to disable monitoring of your VMs, keep these considerations in mind:

* Se si valuta con una singola macchina virtuale e si usa l'area di lavoro predefinita di Log Analytics preselezionata, è possibile disabilitare il monitoraggio disinstallando l'agente di dipendenza dalla macchina virtuale e disconnettendo l'agente log Analytics da questa area di lavoro. Questo approccio è appropriato se si intende usare la macchina virtuale per altri scopi e si decide in un secondo momento di riconnetterla a un'area di lavoro diversa.
* Se è stata selezionata un'area di lavoro di Log Analytics preesistente che supporta altre soluzioni di monitoraggio e la raccolta dei dati da altre origini, è possibile rimuovere i componenti della soluzione dall'area di lavoro senza interrompere o influire sull'area di lavoro.  

>[!NOTE]
> Dopo aver rimosso i componenti della soluzione dall'area di lavoro, è possibile continuare a visualizzare le prestazioni e mappare i dati per le macchine virtuali di Azure.After removing the solution components from your workspace, you might continue to see performance and map data for your Azure VMs. I dati smetteranno di apparire nelle viste **Prestazioni** e **Mappa.** L'opzione **Abilita** sarà disponibile nella macchina virtuale di Azure selezionata in modo da poter riattivare il monitoraggio in futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Rimuovere completamente Il monitoraggio di Azure per le macchine virtualiRemove Azure Monitor for VMs completely

Se è ancora necessaria l'area di lavoro Log Analytics, seguire questi passaggi per rimuovere completamente Monitoraggio di Azure per le macchine virtuali. La `VMInsights` soluzione verrà rimossa dall'area di lavoro.  

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Quando si inizia a digitare, l'elenco filtra i suggerimenti in base all'input. Selezionare **Log Analytics**.
3. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro scelta quando è stato abilitato Monitoraggio di Azure per le macchine virtuali.
4. A sinistra, selezionare **Soluzioni**.  
5. Nell'elenco delle soluzioni selezionare **VMInsights (nome area di lavoro)**. Nella pagina **Panoramica** della soluzione selezionare **Elimina**. Quando viene richiesto di confermare, selezionare **Sì**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Disabilitare il monitoraggio e mantenere l'area di lavoro  

Se l'area di lavoro di Log Analytics deve ancora supportare il monitoraggio da altre origini, seguire questi passaggi per disabilitare il monitoraggio nella macchina virtuale usata per valutare Monitoraggio di Azure per le macchine virtuali. Per le macchine virtuali di Azure, rimuoveremo l'estensione della macchina virtuale dell'agente di dipendenza e l'estensione della macchina virtuale dell'agente log Analytics per Windows o Linux direttamente dalla macchina virtuale. 

>[!NOTE]
>Non rimuovere l'agente di Log Analytics se:Don't remove the Log Analytics agent if: 
>
> * Automazione di Azure gestisce la macchina virtuale per orchestrare i processi o per gestire la configurazione o gli aggiornamenti. 
> * Il Centro sicurezza di Azure gestisce la macchina virtuale per la sicurezza e il rilevamento delle minacce. 
>
> Se si rimuove l'agente di Log Analytics, si impedirà a tali servizi e soluzioni di gestire in modo proattivo la macchina virtuale. 

1. Accedere al [portale](https://portal.azure.com)di Azure . 
2. Nel portale di Azure selezionare **Macchine virtuali**. 
3. Selezionare una macchina virtuale dall'elenco. 
4. A sinistra, seleziona **Estensioni**. Nella pagina **Estensioni** selezionare **DependencyAgent**.
5. Nella pagina delle proprietà dell'estensione selezionare **Disinstalla**.
6. Nella pagina **Estensioni** selezionare **MicrosoftMonitoringAgent**. Nella pagina delle proprietà dell'estensione selezionare **Disinstalla**.  
