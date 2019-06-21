---
title: Come visualizzare le dipendenze delle app con Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Map è una funzionalità di monitoraggio di Azure per le macchine virtuali. Automaticamente rileva componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione tra servizi. Questo articolo fornisce informazioni dettagliate su come usare la funzionalità mappa in vari scenari.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206768"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Usare la funzionalità di mappa di monitoraggio di Azure per le macchine virtuali (anteprima) per comprendere i componenti dell'applicazione
In Monitoraggio di Azure per le macchine virtuali, è possibile visualizzare i componenti delle applicazioni individuate in Windows e Linux macchine virtuali (VM) che vengono eseguiti in Azure o l'ambiente. È possibile osservare le macchine virtuali in due modi. Consente di visualizzare una mappa direttamente da una macchina virtuale o visualizzare una mappa da monitoraggio di Azure per visualizzare i componenti tra gruppi di macchine virtuali. Questo articolo aiuterà a comprendere questi due metodi di visualizzazione e su come usare la funzionalità mappa. 

Per informazioni sulla configurazione di Monitoraggio di Azure per le macchine virtuali, vedere [Enable Azure Monitor for VMs](vminsights-enable-overview.md) (Abilitare Monitoraggio di Azure per le macchine virtuali).

## <a name="sign-in-to-azure"></a>Accedi ad Azure
Accedere al [portale di Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introduzione all'esperienza della mappa
Prima di approfondire l'esperienza della mappa, è necessario comprendere come presenta e visualizza le informazioni. Se si seleziona la funzionalità mappa direttamente da una macchina virtuale o da monitoraggio di Azure, la funzionalità mappa presenta un'esperienza coerente. L'unica differenza è che da monitoraggio di Azure, una mappa Mostra tutti i membri di un'applicazione a più livelli o il cluster.

La funzionalità mappa Visualizza le dipendenze della macchina virtuale mediante l'individuazione dei processi in esecuzione con: 

- Connessioni di rete attive tra server.
- Latenza delle connessioni in ingresso e in uscita.
- Porte in qualsiasi architettura connessa a TCP in un intervallo di tempo specificato.  
 
Espandere una macchina virtuale per visualizzare i dettagli dei processi e solo i processi che comunicano con la macchina virtuale. Il gruppo di client Mostra il conteggio dei client front-end che si connettono alla macchina virtuale. I gruppi di porte di server mostrano il conteggio dei server back-end a che della macchina virtuale si connette. Espandere un gruppo di porte di server per visualizzare l'elenco dettagliato dei server che si connettono tramite quella porta.  

Quando si seleziona la macchina virtuale, il **proprietà** riquadro a destra mostra le proprietà della macchina virtuale. Le proprietà includono informazioni di sistema segnalate dal sistema operativo, le proprietà della macchina virtuale di Azure e un grafico ad anello che riepiloga le connessioni individuate. 

![Il riquadro proprietà](./media/vminsights-maps/properties-pane-01.png)

Sul lato destro del riquadro, selezionare **gli eventi del Log** per visualizzare un elenco di dati che la macchina virtuale è inviati a monitoraggio di Azure. Questi dati sono disponibili per l'esecuzione di query.  Selezionare qualsiasi tipo di record per aprire la **registri** pagina, in cui vengono visualizzati i risultati per tale tipo di record. È anche possibile visualizzare una query preconfigurata che verrà filtrata sulla macchina virtuale.  

![Nel riquadro Log eventi](./media/vminsights-maps/properties-pane-logs-01.png)

Chiudi il **registri** pagina e tornare al **proprietà** riquadro. Non esiste, selezionare **avvisi** per visualizzare gli avvisi di criteri di integrità della macchina virtuale. La funzionalità mappa si integra con avvisi di Azure per visualizzare gli avvisi per il server selezionato nell'intervallo di tempo selezionato. Il server viene visualizzata un'icona per gli avvisi correnti e il **gli avvisi della macchina** riquadro vengono elencati gli avvisi. 

![Riquadro avvisi](./media/vminsights-maps/properties-pane-alerts-01.png)

Per rendere la funzionalità mappa Visualizza gli avvisi pertinenti, creare una regola di avviso che si applica a un computer specifico:

- Includere una clausola per raggruppare gli avvisi da computer (ad esempio, **per intervallo Computer 1 minuto**).
- Su una metrica di base dell'avviso.

Per altre informazioni sulla creazione di regole avvisi e avvisi di Azure, vedere [unificata gli avvisi in Monitoraggio di Azure](../../azure-monitor/platform/alerts-overview.md).

Nell'angolo superiore destro, il **legenda** opzione descrive i simboli e i ruoli nella mappa. Per informazioni dettagliate in una mappa e trascinandolo, usare i controlli zoom nell'angolo in basso a destra. È possibile impostare il livello di zoom e adattare la mappa per la dimensione della pagina.  

## <a name="connection-metrics"></a>Metriche di connessione
Il **connessioni** riquadro vengono visualizzate le metriche standard per la connessione selezionata dalla macchina virtuale tramite la porta TCP. Le metriche includono tempo di risposta, richieste al minuto, velocità effettiva del traffico e collegamenti.  

![Grafici di connettività di rete nel riquadro connessioni](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Connessioni non riuscite
La mappa Mostra le connessioni non riuscite per i processi e computer. Una linea rossa tratteggiata indica che un sistema client non riesce a raggiungere un processo o una porta. Per i sistemi che utilizzano l'agente di dipendenza, l'agente di un report sui tentativi di connessione non riuscita. La funzionalità mappa consente di monitorare un processo osservando i socket TCP che non riescono a stabilire una connessione. Questo errore potrebbe essere causato da un firewall, un errore di configurazione del client o server oppure un servizio remoto non disponibile.

![Una connessione non riuscita sulla mappa](./media/vminsights-maps/map-group-failed-connection-01.png)

Informazioni sulle connessioni non riuscite consentono di risolvere i problemi, convalidare la migrazione, analizzare la protezione e comprendere l'architettura complessiva del servizio. Le connessioni non riuscite sono talvolta innocue, spesso tuttavia indicano un problema. Le connessioni potrebbero non riuscire, ad esempio, quando un ambiente di failover diventa improvvisamente non raggiungibile o due livelli di applicazione non possono comunicare tra loro dopo una migrazione cloud.

### <a name="client-groups"></a>Gruppi di client
Sulla mappa, gruppi di client rappresentano i computer client che si connettono al computer con mapping. Un singolo gruppo di client rappresenta i client per un unico processo o computer.

![Un gruppo di client sulla mappa](./media/vminsights-maps/map-group-client-groups-01.png)

Per visualizzare i client monitorati e gli indirizzi IP dei sistemi in un gruppo di client, selezionare il gruppo. Il contenuto del gruppo vengono visualizzate sotto.  

![Elenco di un gruppo di client di indirizzi IP sulla mappa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se il gruppo include i client monitorati e non monitorati, è possibile selezionare la sezione appropriata del grafico ad anello del gruppo per filtrare i client.

### <a name="server-port-groups"></a>Gruppi di porte di server
Gruppi di porte di server rappresentano le porte nei server dotati di connessioni in ingresso dai computer mappata. Il gruppo che contiene la porta del server e un conteggio del numero di server con connessioni a tale porta. Selezionare il gruppo per visualizzare i singoli server e le connessioni. 

![Un gruppo di porte di server sulla mappa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se il gruppo include i server monitorati e non monitorati, è possibile selezionare la sezione del grafico ad anello del gruppo per filtrare i server appropriata.

## <a name="view-a-map-from-a-vm"></a>Visualizzare una mappa da una macchina virtuale 

Per accedere a monitoraggio di Azure per le macchine virtuali direttamente da una macchina virtuale:

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Nell'elenco, scegliere una macchina virtuale. Nel **Monitoring** keychains **Insights (anteprima)** .  
3. Selezionare la scheda **Mappa**.

La mappa Visualizza le dipendenze della macchina virtuale tramite l'individuazione in esecuzione di processo gruppi e i processi con connessioni di rete attive in un intervallo di tempo specificato.  

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare l'aspetto delle dipendenze nel passato, è possibile eseguire una query per gli intervalli di tempo cronologici della durata di fino a un'ora. Per eseguire la query, usare il **TimeRange** selettore nell'angolo superiore sinistro. È possibile eseguire una query, ad esempio, durante un evento imprevisto o per visualizzare lo stato prima di una modifica.  

![Panoramica di mappe direttamente da macchina virtuale](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Visualizzare una mappa da un set di scalabilità di macchine virtuali

Per accedere a monitoraggio di Azure per le macchine virtuali direttamente da un set di scalabilità di macchine virtuali:

1. Nel portale di Azure, selezionare **set di scalabilità di macchine virtuali**.
2. Nell'elenco, scegliere una macchina virtuale. Quindi nel **Monitoring** keychains **Insights (anteprima)** .  
3. Selezionare la scheda **Mappa**.

La mappa Visualizza tutte le istanze nel set di scalabilità in un nodo di gruppo con le dipendenze del gruppo. Il nodo espanso Elenca le istanze nel set di scalabilità. È possibile scorrere le 10 istanze alla volta. 

Per caricare una mappa per un'istanza specifica, selezionare prima di tutto tale istanza sulla mappa. Quindi selezionare il **puntini di sospensione** pulsante (...) a destra e scegliere **carica mappa del Server**. La mappa in cui viene visualizzato, vengono visualizzati gruppi di processi e i processi con connessioni di rete attive in un intervallo di tempo specificato. 

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare l'aspetto delle dipendenze nel passato, è possibile eseguire una query per gli intervalli di tempo cronologici della durata di fino a un'ora. Per eseguire la query, usare il **TimeRange** selettore. È possibile eseguire una query, ad esempio, durante un evento imprevisto o per visualizzare lo stato prima di una modifica.

![Panoramica di mappe direttamente da macchina virtuale](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>È anche possibile accedere a una mappa per un'istanza specifica del **istanze** visualizzazione per il set di scalabilità di macchine virtuali. Nel **le impostazioni** passare alla sezione **istanze** > **Insights (anteprima)** .

## <a name="view-a-map-from-azure-monitor"></a>Visualizzare una mappa da monitoraggio di Azure
In Monitoraggio di Azure, la funzionalità mappa offre una visualizzazione globale le macchine virtuali e le relative dipendenze. Per accedere alla funzionalità di mappa in Monitoraggio di Azure:

1. Nel portale di Azure selezionare **Monitoraggio**. 
2. Nel **Insights** keychains **macchine virtuali (anteprima)** .
3. Selezionare la scheda **Mappa**.

   ![Azure carta panoramica di monitoraggio di più macchine virtuali](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Scegliere un'area di lavoro utilizzando il **dell'area di lavoro** selettore nella parte superiore della pagina. Se si hanno più di un'area di lavoro di Log Analitica, scegliere l'area di lavoro che è abilitata con la soluzione e che dispone di macchine virtuali a esso collegati. 

Il **gruppo** selettore restituisce le sottoscrizioni, gruppi di risorse [gruppi di computer](../../azure-monitor/platform/computer-groups.md)e set di scalabilità di macchine virtuali dei computer in cui sono correlati all'area di lavoro selezionato. La selezione si applica solo alla funzionalità di mappa e non si applica alla prestazioni o all'integrità.

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare l'aspetto delle dipendenze nel passato, è possibile eseguire una query per gli intervalli di tempo cronologici della durata di fino a un'ora. Per eseguire la query, usare il **TimeRange** selettore. È possibile eseguire una query, ad esempio, durante un evento imprevisto o per visualizzare lo stato prima di una modifica.  

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni su come usare la funzionalità di integrità, vedere [integrità macchina virtuale di Azure Visualizza](vminsights-health.md). 
- Per identificare i colli di bottiglia, controllare le prestazioni e informazioni sull'utilizzo complessivo delle macchine virtuali, vedere [visualizzare lo stato delle prestazioni per monitoraggio di Azure per le macchine virtuali](vminsights-performance.md). 
