---
title: Come disabilitare il monitoraggio con Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Questo articolo descrive come interrompere il monitoraggio delle macchine virtuali con Monitoraggio di Azure per le macchine virtuali.
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
ms.openlocfilehash: 0f35ea3e35277ee7f1afd8278a31f45ed20c6995
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522131"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>Come disabilitare il monitoraggio delle macchine virtuali con Monitoraggio di Azure per le macchine virtuali (anteprima)

Se dopo averlo abilitato, si decide che il monitoraggio con Monitoraggio di Azure per le macchine virtuali non è più necessario, è possibile disabilitarlo. Questo articolo illustra come eseguire questa operazione per una o più macchine virtuali.  

Attualmente Monitoraggio di Azure per le macchine virtuali non supporta la disabilitazione selettiva del monitoraggio delle macchine virtuali. Se l'area di lavoro Log Analytics è configurata per supportare questa e altre soluzioni e per a raccogliere altri dati di monitoraggio, è importante comprendere l'impatto e i metodi descritti di seguito prima di procedere.

Monitoraggio di Azure per le macchine virtuali si basa sui componenti seguenti per offrire:

* Un'area di lavoro Log Analytics nella quale vengono archiviati i dati di monitoraggio raccolti dalle macchine virtuali e da altre origini.
* Una raccolta dei contatori delle prestazioni configurati nell'area di lavoro, che aggiorna la configurazione di monitoraggio su tutte le macchine virtuali connesse all'area di lavoro.
* Due soluzioni di monitoraggio configurate nell'area di lavoro - **InfrastructureInsights** e **ServiceMap**, che aggiornano la configurazione di monitoraggio su tutte le macchine virtuali connesse all'area di lavoro.
* Due estensioni macchina virtuale di Azure, **MicrosoftMonitoringAgent** e il **DepenendencyAgent**, che raccolgono e inviano i dati all'area di lavoro.

Quando ci si prepara a disabilitare il monitoraggio delle macchine virtuali con Monitoraggio di Azure per le macchine virtuali è bene tenere conto dei seguenti aspetti:

* Se la valutazione avviene con una singola macchina virtuale ed è stata accettata l'area di lavoro Log Analytics predefinita pre-selezionata, è possibile disabilitare il monitoraggio disinstallando Dependency Agent dalla macchina virtuale e disconnettendo l'agente Log Analytics dall'area di lavoro. Questo approccio è appropriato se si intende usare la macchina virtuale per altri scopi e si decide in un secondo momento di riconnetterla a un'altra area di lavoro.
* Se si usa l'area di lavoro Log Analytics per supportare altre soluzioni di monitoraggio e raccolta dei dati da altre origini, è possibile rimuovere i componenti della soluzione Monitoraggio di Azure per le macchine virtuali dall'area di lavoro senza interruzioni né conseguenze sull'area di lavoro.  

>[!NOTE]
> Dopo aver rimosso i componenti della soluzione dall'area di lavoro, è possibile continuare a visualizzare lo stato di integrità dalle macchine virtuali di Azure, in particolare dati sulle prestazioni e mappa, quando si passa a qualsiasi altra visualizzazione nel portale. Dopo qualche istante, i dati non verranno più visualizzati nella visualizzazione delle prestazioni e della mappa; la visualizzazione dell'integrità continua a mostrare lo stato di integrità delle macchine virtuali. L'opzione **Prova adesso** disponibile nella macchina virtuale di Azure selezionata consente di riattivare il monitoraggio in un secondo momento.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>Rimozione completa di Monitoraggio di Azure per le macchine virtuali

I passaggi seguenti descrivono come rimuovere completamente Monitoraggio di Azure per le macchine virtuali qualora l'area di lavoro Log Analytics sia ancora necessaria. Le soluzioni **InfastructureInsights** e **ServiceMap** verranno rimosse dall'area di lavoro.  

>[!NOTE]
>Se la soluzione di monitoraggio Mapping dei servizi era usata prima di abilitare Monitoraggio di Azure per le macchine virtuali e si ha ancora intenzione di usarla, non rimuoverla come descritto nel passaggio 6 riportato di seguito.  
>

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare Log Analytics. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
3. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area scelta al momento della registrazione di Monitoraggio di Azure per le macchine virtuali.
4. Selezionare **Soluzioni** nel riquadro sinistro.  
5. Nell'elenco delle soluzioni, selezionare **InfrastructureInsights (nome dell'area di lavoro)** e quindi nella pagina **Panoramica** della soluzione, fare clic su **Elimina**.  Quando viene richiesto di confermare, fare clic su **Sì**.  
6. Nell'elenco delle soluzioni, selezionare **ServiceMap (nome dell'area di lavoro)** e quindi nella pagina **Panoramica** della soluzione fare clic su **Elimina**.  Quando viene richiesto di confermare, fare clic su **Sì**.  

Se prima della registrazione di Monitoraggio di Azure per le macchine virtuali non era abilitata la [raccolta dei contatori delle prestazioni](vminsights-enable-overview.md#performance-counters-enabled) per le macchine virtuali Windows o Linux dell'area di lavoro, è necessario disabilitare tali regole seguendo i passaggi indicati [qui](../platform/data-sources-performance-counters.md#configuring-performance-counters) per Windows e per Linux.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Disabilitare il monitoraggio per una macchina virtuale di Azure e conservare l'area di lavoro  

I passaggi seguenti indicano come disabilitare il monitoraggio per una macchina virtuale che è stata abilitata per la valutazione di Monitoraggio di Azure per le macchine virtuali, ma l'area di lavoro Log Analytics è ancora necessaria per supportare il monitoraggio da altre origini. Se si tratta di una macchina virtuale di Azure, vengono rimosse l'estensione Dependency Agent della macchina virtuale e Log Analytics per Windows/Linux direttamente dalla macchina virtuale. 

>[!NOTE]
>Se la macchina virtuale è gestita da Automazione di Azure per le attività di orchestrazione dei processi, gestione della configurazione o degli aggiornamenti oppure è gestita dal Centro sicurezza di Azure per le attività di protezione della gestione e rilevamento delle minacce, l'agente Log Analytics non deve essere rimosso. In caso contrario, si impedisce a tali servizi e soluzioni di gestire in modo proattivo la macchina virtuale. 

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com). 
2. Nel portale di Azure selezionare **Macchine virtuali**. 
3. Selezionare una macchina virtuale dall'elenco. 
4. Nel riquadro sinistro selezionare **Estensioni** e nella pagina **Estensioni** selezionare **DependencyAgent**.
5. Nella pagina delle proprietà dell'estensione fare clic su **Disinstalla**.
6. Nella pagina **Estensioni** selezionare **MicrosoftMonitoringAgent** e nella pagina delle proprietà dell'estensione fare clic su **Disinstalla**.  
