---
title: Disabilitare il monitoraggio in Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Questo articolo descrive come arrestare il monitoraggio delle macchine virtuali in Monitoraggio di Azure per le macchine virtuali.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/05/2018
ms.openlocfilehash: 70e71688fde5aff4002c7d49b1408bcefeab1eed
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555152"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Disabilitare il monitoraggio delle macchine virtuali in Monitoraggio di Azure per le macchine virtuali (anteprima)

Dopo aver abilitato il monitoraggio delle macchine virtuali (VM), in un secondo momento è possibile scegliere di disabilitare il monitoraggio in Monitoraggio di Azure per le macchine virtuali. Questo articolo illustra come disabilitare il monitoraggio per una o più macchine virtuali.  

Attualmente, Monitoraggio di Azure per le macchine virtuali non supporta la disabilitazione selettiva del monitoraggio della macchina virtuale. L'area di lavoro Log Analytics potrebbe supportare Monitoraggio di Azure per le macchine virtuali e altre soluzioni. Potrebbe inoltre raccogliere altri dati di monitoraggio. Se l'area di lavoro Log Analytics fornisce questi servizi, è necessario comprendere l'effetto e i metodi di disabilitazione del monitoraggio prima di iniziare.

Monitoraggio di Azure per le macchine virtuali si basa sui componenti seguenti per offrire:

* Un'area di lavoro Log Analytics, che archivia i dati di monitoraggio da macchine virtuali e altre origini.
* Raccolta di contatori delle prestazioni configurati nell'area di lavoro. La raccolta aggiorna la configurazione di monitoraggio in tutte le VM connesse all'area di lavoro.
* `InfrastructureInsights` e `ServiceMap`, che eseguono il monitoraggio di soluzioni configurate nell'area di lavoro. Queste soluzioni aggiornano la configurazione del monitoraggio in tutte le VM connesse all'area di lavoro.
* `MicrosoftMonitoringAgent` e `DependencyAgent`, ovvero le estensioni della macchina virtuale di Azure. Queste estensioni raccolgono e inviano dati all'area di lavoro.

Quando si prepara la disabilitazione del monitoraggio delle macchine virtuali, tenere presenti le considerazioni seguenti:

* Se è stata valutata con una singola macchina virtuale e si usa l'area di lavoro predefinita Log Analytics preselezionata, è possibile disabilitare il monitoraggio disinstallando Dependency Agent dalla macchina virtuale e disconnettendo l'agente Log Analytics da questa area di lavoro. Questo approccio è appropriato se si prevede di usare la macchina virtuale per altri scopi e decidere successivamente di riconnetterla a un'area di lavoro diversa.
* Se è stata selezionata un'area di lavoro Log Analytics preesistente che supporta altre soluzioni di monitoraggio e la raccolta di dati da altre origini, è possibile rimuovere i componenti della soluzione dall'area di lavoro senza interrompere o influire sull'area di lavoro.  

>[!NOTE]
> Dopo aver rimosso i componenti della soluzione dall'area di lavoro, è possibile continuare a visualizzare lo stato di integrità dalle macchine virtuali di Azure. in particolare, verranno visualizzati i dati relativi alle prestazioni e alla mappa quando si accede a una delle visualizzazioni nel portale. Alla fine i dati verranno interrotti nelle viste **prestazioni** e **mappa** . Tuttavia, la visualizzazione **integrità** continuerà a mostrare lo stato di integrità delle macchine virtuali. L'opzione **prova ora** sarà disponibile dalla macchina virtuale di Azure selezionata per poter riabilitare il monitoraggio in futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Rimuovi Monitoraggio di Azure per le macchine virtuali completamente

Se è ancora necessaria l'area di lavoro Log Analytics, attenersi alla procedura seguente per rimuovere completamente Monitoraggio di Azure per le macchine virtuali. Le soluzioni `InfrastructureInsights` e `ServiceMap` verranno rimosse dall'area di lavoro.  

>[!NOTE]
>Se è stata usata la soluzione di monitoraggio Mapping dei servizi prima di abilitare Monitoraggio di Azure per le macchine virtuali e si basa ancora su di essa, non rimuovere tale soluzione come descritto nell'ultimo passaggio della procedura seguente.  
>

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Quando si inizia a digitare, l'elenco Filtra i suggerimenti in base all'input. Selezionare **Log Analytics**.
3. Nell'elenco delle aree di lavoro Log Analytics selezionare l'area di lavoro scelta quando è stata abilitata Monitoraggio di Azure per le macchine virtuali.
4. A sinistra selezionare **soluzioni**.  
5. Nell'elenco delle soluzioni selezionare **InfrastructureInsights (nome dell'area di lavoro)** . Nella pagina **Overview (panoramica** ) per la soluzione selezionare **Delete (Elimina**). Quando viene richiesto di confermare, selezionare **Sì**.  
6. Nell'elenco delle soluzioni selezionare **ServiceMap (nome dell'area di lavoro)** . Nella pagina **Overview (panoramica** ) per la soluzione selezionare **Delete (Elimina**). Quando viene richiesto di confermare, selezionare **Sì**.  

Prima di abilitare Monitoraggio di Azure per le macchine virtuali, se non sono stati [raccolti contatori delle prestazioni](vminsights-enable-overview.md#performance-counters-enabled) per le macchine virtuali basate su Windows o Linux nell'area di lavoro, [disabilitare tali regole](../platform/data-sources-performance-counters.md#configuring-performance-counters) per Windows e per Linux.

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
