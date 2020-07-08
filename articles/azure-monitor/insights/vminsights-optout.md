---
title: Disabilitare il monitoraggio in Monitoraggio di Azure per le macchine virtuali
description: Questo articolo descrive come arrestare il monitoraggio delle macchine virtuali in Monitoraggio di Azure per le macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79480522"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Disabilitare il monitoraggio delle macchine virtuali in Monitoraggio di Azure per le macchine virtuali

Dopo aver abilitato il monitoraggio delle macchine virtuali (VM), in un secondo momento è possibile scegliere di disabilitare il monitoraggio in Monitoraggio di Azure per le macchine virtuali. Questo articolo illustra come disabilitare il monitoraggio per una o più macchine virtuali.  

Attualmente, Monitoraggio di Azure per le macchine virtuali non supporta la disabilitazione selettiva del monitoraggio della macchina virtuale. L'area di lavoro Log Analytics potrebbe supportare Monitoraggio di Azure per le macchine virtuali e altre soluzioni. Potrebbe inoltre raccogliere altri dati di monitoraggio. Se l'area di lavoro Log Analytics fornisce questi servizi, è necessario comprendere l'effetto e i metodi di disabilitazione del monitoraggio prima di iniziare.

Monitoraggio di Azure per le macchine virtuali si basa sui componenti seguenti per offrire:

* Un'area di lavoro Log Analytics, che archivia i dati di monitoraggio da macchine virtuali e altre origini.
* Raccolta di contatori delle prestazioni configurati nell'area di lavoro. La raccolta aggiorna la configurazione di monitoraggio in tutte le VM connesse all'area di lavoro.
* `VMInsights`, ovvero una soluzione di monitoraggio configurata nell'area di lavoro. Questa soluzione aggiorna la configurazione di monitoraggio in tutte le VM connesse all'area di lavoro.
* `MicrosoftMonitoringAgent`e `DependencyAgent` , che sono estensioni di VM di Azure. Queste estensioni raccolgono e inviano dati all'area di lavoro.

Quando si prepara la disabilitazione del monitoraggio delle macchine virtuali, tenere presenti le considerazioni seguenti:

* Se è stata valutata con una singola macchina virtuale e si usa l'area di lavoro predefinita Log Analytics preselezionata, è possibile disabilitare il monitoraggio disinstallando Dependency Agent dalla macchina virtuale e disconnettendo l'agente Log Analytics da questa area di lavoro. Questo approccio è appropriato se si prevede di usare la macchina virtuale per altri scopi e decidere successivamente di riconnetterla a un'area di lavoro diversa.
* Se è stata selezionata un'area di lavoro Log Analytics preesistente che supporta altre soluzioni di monitoraggio e la raccolta di dati da altre origini, è possibile rimuovere i componenti della soluzione dall'area di lavoro senza interrompere o influire sull'area di lavoro.  

>[!NOTE]
> Dopo aver rimosso i componenti della soluzione dall'area di lavoro, è possibile continuare a visualizzare i dati relativi alle prestazioni e alla mappa per le macchine virtuali di Azure. Alla fine i dati verranno interrotti nelle viste **prestazioni** e **mappa** . L'opzione **Abilita** sarà disponibile dalla macchina virtuale di Azure selezionata per poter riabilitare il monitoraggio in futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Rimuovi Monitoraggio di Azure per le macchine virtuali completamente

Se è ancora necessaria l'area di lavoro Log Analytics, attenersi alla procedura seguente per rimuovere completamente Monitoraggio di Azure per le macchine virtuali. La soluzione verrà rimossa `VMInsights` dall'area di lavoro.  

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Quando si inizia a digitare, l'elenco Filtra i suggerimenti in base all'input. Selezionare **log Analytics**.
3. Nell'elenco delle aree di lavoro Log Analytics selezionare l'area di lavoro scelta quando è stata abilitata Monitoraggio di Azure per le macchine virtuali.
4. A sinistra selezionare **soluzioni**.  
5. Nell'elenco delle soluzioni selezionare **VMInsights (nome dell'area di lavoro)**. Nella pagina **Overview (panoramica** ) per la soluzione selezionare **Delete (Elimina**). Quando viene richiesto di confermare, selezionare **Sì**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Disabilitare il monitoraggio e lasciare l'area di lavoro  

Se l'area di lavoro di Log Analytics deve ancora supportare il monitoraggio da altre origini, seguire questa procedura per disabilitare il monitoraggio nella macchina virtuale usata per valutare Monitoraggio di Azure per le macchine virtuali. Per le macchine virtuali di Azure, l'estensione della macchina virtuale dell'agente di dipendenza e l'estensione della macchina virtuale dell'agente di Log Analytics per Windows o Linux verranno rimosse direttamente dalla macchina virtuale. 

>[!NOTE]
>Non rimuovere l'agente di Log Analytics se: 
>
> * Automazione di Azure gestisce la macchina virtuale per orchestrare i processi o gestire la configurazione o gli aggiornamenti. 
> * Il Centro sicurezza di Azure gestisce la VM per la sicurezza e il rilevamento delle minacce. 
>
> Se si rimuove l'agente di Log Analytics, si impedirà a tali servizi e soluzioni di gestire in modo proattivo la VM. 

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel portale di Azure selezionare **Macchine virtuali**. 
3. Selezionare una macchina virtuale dall'elenco. 
4. A sinistra selezionare **estensioni**. Nella pagina **estensioni** selezionare **DependencyAgent**.
5. Nella pagina Proprietà estensione selezionare **Disinstalla**.
6. Nella pagina **estensioni** selezionare **MicrosoftMonitoringAgent**. Nella pagina Proprietà estensione selezionare **Disinstalla**.  
