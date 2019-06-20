---
title: Disabilitare il monitoraggio in Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Questo articolo descrive come arrestare il monitoraggio delle macchine virtuali nel monitoraggio di Azure per le macchine virtuali.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155694"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Disabilitare il monitoraggio delle macchine virtuali in Monitoraggio di Azure per le macchine virtuali (anteprima)

Dopo aver abilitato il monitoraggio delle macchine virtuali (VM), in un secondo momento è possibile scegliere di disabilitare il monitoraggio in Monitoraggio di Azure per le macchine virtuali. Questo articolo viene illustrato come disabilitare il monitoraggio per uno o più macchine virtuali.  

Attualmente monitoraggio di Azure per le macchine virtuali non supporta selettivo la disabilitazione del monitoraggio delle VM. L'area di lavoro di Log Analitica potrebbe supportare monitoraggio di Azure per macchine virtuali e altre soluzioni. Potrebbe inoltre raccogliere altri dati di monitoraggio. Se l'area di lavoro di Log Analitica fornisce questi servizi, è necessario conoscere le effettive e i metodi di disabilitazione del monitoraggio prima di iniziare.

Monitoraggio di Azure per le macchine virtuali si basa sui componenti seguenti per offrire:

* Un Log Analitica dell'area di lavoro, che archivia i dati di monitoraggio da macchine virtuali e altre origini.
* Raccolta di contatori delle prestazioni configurati nell'area di lavoro. La raccolta degli aggiornamenti della configurazione del monitoraggio in tutte le VM connesse all'area di lavoro.
* `InfrastructureInsights` e `ServiceMap`, che eseguono il monitoraggio soluzioni configurate nell'area di lavoro. Queste soluzioni di aggiornamento della configurazione del monitoraggio in tutte le VM connesse all'area di lavoro.
* `MicrosoftMonitoringAgent` e `DependencyAgent`, quali sono le estensioni di macchina virtuale di Azure. Queste estensioni raccolgono e inviano dati all'area di lavoro.

Durante la preparazione disabilitare il monitoraggio delle macchine virtuali, tenere presenti queste considerazioni:

* Se è valutato con una singola macchina virtuale e l'area di lavoro di Log Analitica predefinita preselezionate, è possibile disabilitare il monitoraggio disinstallando l'agente di dipendenza dalla macchina virtuale e la disconnessione dell'agente di Log Analitica dall'area di lavoro. Questo approccio è appropriato se si prevede di usare la macchina virtuale per altri scopi e in un secondo momento si decide di riconnetterlo a un'altra area di lavoro.
* Se si seleziona un'area di lavoro di Log Analitica di preesistente che supporta altre soluzioni di monitoraggio e la raccolta dei dati da altre origini, è possibile rimuovere i componenti della soluzione dall'area di lavoro senza interrompere o che interessano l'area di lavoro.  

>[!NOTE]
> Dopo la rimozione dei componenti della soluzione dall'area di lavoro, è possibile continuare a visualizzare lo stato di integrità da macchine virtuali di Azure; in particolare, si delle prestazioni e mapping dei dati quando si passa a una visualizzazione nel portale. Dati smetterà di visualizzazione nel **Performance** e **mappa** viste. Ma il **integrità** visualizzazione Continuerai a visualizzare lo stato di integrità per le macchine virtuali. Il **prova ora** opzione saranno disponibile dalla macchina virtuale di Azure selezionati in modo che è possibile riattivare il monitoraggio in futuro.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Rimuovere completamente il monitoraggio di Azure per le macchine virtuali

Se è necessario comunque l'area di lavoro di Log Analitica, seguire questa procedura per rimuovere completamente il monitoraggio di Azure per le macchine virtuali. È possibile rimuovere il `InfrastructureInsights` e `ServiceMap` soluzioni nell'area di lavoro.  

>[!NOTE]
>Se è stato usato il mapping dei servizi soluzione di monitoraggio prima di monitoraggio di Azure è abilitata per le macchine virtuali e ancora si basano su di essa, non rimuovere tale soluzione, come descritto nel passaggio precedente della procedura seguente.  
>

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Come si inizia a digitare, l'elenco viene filtrato i suggerimenti in base all'input. Selezionare **Log Analytics**.
3. Nell'elenco delle aree di lavoro di Log Analitica, selezionare l'area di lavoro si è scelto quando è abilitato il monitoraggio di Azure per le macchine virtuali.
4. A sinistra, selezionare **soluzioni**.  
5. Nell'elenco delle soluzioni, selezionare **InfrastructureInsights (nome dell'area di lavoro)** . Nel **Overview** pagina relativa alla soluzione, seleziona **eliminare**. Quando viene richiesto di confermare, selezionare **Sì**.  
6. Nell'elenco delle soluzioni, selezionare **ServiceMap (nome dell'area di lavoro)** . Nel **Overview** pagina relativa alla soluzione, seleziona **eliminare**. Quando viene richiesto di confermare, selezionare **Sì**.  

Prima di attivare il monitoraggio di Azure per le macchine virtuali, se non è stato fatto [raccogliere contatori delle prestazioni](vminsights-enable-overview.md#performance-counters-enabled) per le macchine virtuali basate su Windows o basato su Linux nell'area di lavoro [disabilitino](../platform/data-sources-performance-counters.md#configuring-performance-counters) per Windows e Linux.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Disabilitare il monitoraggio e mantenere l'area di lavoro  

Se è ancora all'area di lavoro di Log Analitica deve supportare il monitoraggio da altre origini, attenendosi alla procedura seguente per disabilitare il monitoraggio nella macchina virtuale che è usata per valutare monitoraggio di Azure per le macchine virtuali. Per le VM di Azure, si rimuoverà l'agente di dipendenza estensione della macchina virtuale e l'agente di Log Analitica estensione della macchina virtuale per Windows o Linux direttamente dalla macchina virtuale. 

>[!NOTE]
>Non rimuovere l'agente di Log Analitica se: 
>
> * Automazione di Azure consente di gestire la macchina virtuale per orchestrare i processi o gestire gli aggiornamenti o configurazione. 
> * Centro sicurezza di Azure consente di gestire la macchina virtuale per la sicurezza e il rilevamento delle minacce. 
>
> Se si rimuove l'agente di Log Analitica, si impedirà tali servizi e soluzioni in modo proattivo la gestione della macchina virtuale. 

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel portale di Azure selezionare **Macchine virtuali**. 
3. Selezionare una macchina virtuale dall'elenco. 
4. A sinistra, selezionare **estensioni**. Nel **Extensions** pagina, selezionare **DependencyAgent**.
5. Nella pagina delle proprietà di estensione, selezionare **Disinstalla**.
6. Nel **Extensions** pagina, selezionare **MicrosoftMonitoringAgent**. Nella pagina delle proprietà di estensione, selezionare **Disinstalla**.  
