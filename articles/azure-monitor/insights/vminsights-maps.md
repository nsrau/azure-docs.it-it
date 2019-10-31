---
title: Visualizza le dipendenze dell'app con Monitoraggio di Azure per le macchine virtuali (anteprima)
description: Map è una funzionalità di Monitoraggio di Azure per le macchine virtuali. Individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue il mapping delle comunicazioni tra i servizi. Questo articolo fornisce informazioni dettagliate su come usare la funzionalità map in diversi scenari.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 7ca79b0df55dbfea6a0e2c016773f9f32bf29667
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199088"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Utilizzare la funzionalità di mapping di Monitoraggio di Azure per le macchine virtuali (anteprima) per comprendere i componenti dell'applicazione
In Monitoraggio di Azure per le macchine virtuali, è possibile visualizzare i componenti dell'applicazione individuati in macchine virtuali Windows e Linux in esecuzione in Azure o nell'ambiente in uso. È possibile osservare le macchine virtuali in due modi. Visualizzare una mappa direttamente da una macchina virtuale o visualizzare una mappa da monitoraggio di Azure per visualizzare i componenti tra gruppi di macchine virtuali. Questo articolo consente di comprendere questi due metodi di visualizzazione e come usare la funzionalità mappa. 

Per informazioni sulla configurazione di Monitoraggio di Azure per le macchine virtuali, vedere [Enable Azure Monitor for VMs](vminsights-enable-overview.md) (Abilitare Monitoraggio di Azure per le macchine virtuali).

## <a name="sign-in-to-azure"></a>Accedere a Azure
Accedere al [portale di Azure](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introduzione all'esperienza di mappa
Prima di approfondire l'esperienza della mappa, è necessario comprenderne il modo in cui presenta e visualizza le informazioni. Se si seleziona la funzionalità mappa direttamente da una macchina virtuale o da monitoraggio di Azure, la funzionalità di mappa presenta un'esperienza coerente. L'unica differenza è che da monitoraggio di Azure, una mappa Mostra tutti i membri di un cluster o di un'applicazione a più livelli.

La funzionalità map Visualizza le dipendenze della VM scoprendo i processi in esecuzione con: 

- Connessioni di rete attive tra i server.
- Latenza delle connessioni in ingresso e in uscita.
- Porte in qualsiasi architettura connessa a TCP in un intervallo di tempo specificato.  
 
Espandere una macchina virtuale per visualizzare i dettagli del processo e solo i processi che comunicano con la macchina virtuale. Il gruppo client Mostra il numero di client front-end che si connettono alla macchina virtuale. I gruppi di porte server indicano il numero di server back-end a cui si connette la macchina virtuale. Espandere un gruppo di porte server per visualizzare l'elenco dettagliato dei server che si connettono tramite tale porta.  

Quando si seleziona la macchina virtuale, il riquadro **Proprietà** a destra mostra le proprietà della macchina virtuale. Le proprietà includono le informazioni di sistema segnalate dal sistema operativo, le proprietà della macchina virtuale di Azure e un grafico ad anello che riepiloga le connessioni individuate. 

![Riquadro proprietà](./media/vminsights-maps/properties-pane-01.png)

Sul lato destro del riquadro selezionare **Registra eventi** per visualizzare un elenco di dati inviati dalla macchina virtuale a monitoraggio di Azure. Questi dati sono disponibili per l'esecuzione di query.  Selezionare un tipo di record per aprire la pagina **log** , in cui vengono visualizzati i risultati per il tipo di record. Viene anche visualizzata una query preconfigurata che viene filtrata in base alla VM.  

![Riquadro Registra eventi](./media/vminsights-maps/properties-pane-logs-01.png)

Chiudere la pagina **log** e tornare al riquadro **Proprietà** . Selezionare **avvisi** per visualizzare gli avvisi relativi ai criteri di integrità della macchina virtuale. La funzionalità mappa si integra con gli avvisi di Azure per visualizzare gli avvisi per il server selezionato nell'intervallo di tempo selezionato. Il server Visualizza un'icona per gli avvisi correnti e nel riquadro **avvisi computer** sono elencati gli avvisi. 

![Il riquadro avvisi](./media/vminsights-maps/properties-pane-alerts-01.png)

Per fare in modo che la funzionalità mappa visualizzi gli avvisi rilevanti, creare una regola di avviso che si applica a un computer specifico:

- Includere una clausola per raggruppare gli avvisi per computer (ad esempio, **in base all'intervallo del computer 1 minuto**).
- Basare l'avviso su una metrica.

Per altre informazioni sugli avvisi di Azure e sulla creazione di regole di avviso, vedere [avvisi unificati in monitoraggio di Azure](../../azure-monitor/platform/alerts-overview.md).

Nell'angolo in alto a destra, l'opzione **Legenda** descrive i simboli e i ruoli sulla mappa. Per un esame più approfondito della mappa e per spostarlo, usare i controlli zoom nell'angolo in basso a destra. È possibile impostare il livello di zoom e adattare la mappa alle dimensioni della pagina.  

## <a name="connection-metrics"></a>Metriche di connessione
Il riquadro **connessioni** Visualizza le metriche standard per la connessione selezionata dalla macchina virtuale sulla porta TCP. Le metriche includono tempo di risposta, richieste al minuto, velocità effettiva del traffico e collegamenti.  

![Grafici di connettività di rete nel riquadro connessioni](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Connessioni non riuscite
La mappa mostra le connessioni non riuscite per i processi e i computer. Una linea rossa tratteggiata indica che un sistema client non riesce a raggiungere un processo o una porta. Per i sistemi che utilizzano Dependency Agent, l'agente segnala i tentativi di connessione non riusciti. La funzionalità map monitora un processo osservando i socket TCP che non riescono a stabilire una connessione. Questo errore può essere causato da un firewall, da un errore di configurazione del client o del server o da un servizio remoto non disponibile.

![Connessione non riuscita sulla mappa](./media/vminsights-maps/map-group-failed-connection-01.png)

Le informazioni sulle connessioni non riuscite possono essere utili per la risoluzione dei problemi, la convalida della migrazione, l'analisi della sicurezza e l'architettura complessiva del servizio. Le connessioni non riuscite sono talvolta innocue, ma spesso puntano a un problema. Le connessioni potrebbero non riuscire, ad esempio, quando un ambiente di failover diventa improvvisamente non raggiungibile o quando due livelli applicazione non possono comunicare tra loro dopo una migrazione cloud.

### <a name="client-groups"></a>Gruppi di client
Sulla mappa, i gruppi di client rappresentano i computer client che si connettono al computer mappato. Un singolo gruppo di client rappresenta i client per un singolo processo o computer.

![Un gruppo di client nella mappa](./media/vminsights-maps/map-group-client-groups-01.png)

Per visualizzare i client monitorati e gli indirizzi IP dei sistemi in un gruppo di client, selezionare il gruppo. Il contenuto del gruppo viene visualizzato di seguito.  

![Elenco di indirizzi IP di un gruppo di client nella mappa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se il gruppo include client monitorati e non monitorati, è possibile selezionare la sezione appropriata del grafico ad anello del gruppo per filtrare i client.

### <a name="server-port-groups"></a>Server-gruppi di porte
I gruppi di porte server rappresentano le porte sui server che dispongono di connessioni in ingresso dal computer mappato. Il gruppo contiene la porta del server e un conteggio del numero di server con connessioni a tale porta. Selezionare il gruppo per visualizzare i singoli server e le connessioni. 

![Un gruppo di porte server nella mappa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se il gruppo include server monitorati e non monitorati, è possibile selezionare la sezione appropriata del grafico ad anello del gruppo per filtrare i server.

## <a name="view-a-map-from-a-vm"></a>Visualizzare una mappa da una macchina virtuale 

Per accedere Monitoraggio di Azure per le macchine virtuali direttamente da una macchina virtuale:

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Dall'elenco scegliere una macchina virtuale. Nella sezione **monitoraggio** scegliere **Insights (anteprima)** .  
3. Selezionare la scheda **Mappa**.

La mappa Visualizza le dipendenze della VM scoprendo i gruppi di processi e i processi in esecuzione con connessioni di rete attive in un intervallo di tempo specificato.  

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare le dipendenze in passato, è possibile eseguire una query per gli intervalli di tempo cronologici fino a un'ora. Per eseguire la query, usare il selettore **TimeRange** nell'angolo in alto a sinistra. È possibile eseguire una query, ad esempio, durante un evento imprevisto o visualizzare lo stato prima di una modifica.  

![Panoramica di mappe direttamente da macchina virtuale](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Visualizzare una mappa da un set di scalabilità di macchine virtuali

Per accedere Monitoraggio di Azure per le macchine virtuali direttamente da un set di scalabilità di macchine virtuali:

1. Nella portale di Azure selezionare set di **scalabilità di macchine virtuali**.
2. Dall'elenco scegliere una macchina virtuale. Quindi, nella sezione **monitoraggio** scegliere **Insights (anteprima)** .  
3. Selezionare la scheda **Mappa**.

La mappa Visualizza tutte le istanze nel set di scalabilità come nodo gruppo insieme alle dipendenze del gruppo. Il nodo espanso elenca le istanze nel set di scalabilità. È possibile scorrere le istanze 10 alla volta. 

Per caricare una mappa per un'istanza specifica, selezionare innanzitutto tale istanza sulla mappa. Quindi selezionare il pulsante con i **puntini** di sospensione (...) a destra e scegliere **Carica mappa server**. Nella mappa visualizzata vengono visualizzati i processi e i gruppi di processi con connessioni di rete attive in un intervallo di tempo specificato. 

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare le dipendenze in passato, è possibile eseguire una query per gli intervalli di tempo cronologici fino a un'ora. Per eseguire la query, usare il selettore **TimeRange** . È possibile eseguire una query, ad esempio, durante un evento imprevisto o visualizzare lo stato prima di una modifica.

![Panoramica di mappe direttamente da macchina virtuale](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>È anche possibile accedere a una mappa per un'istanza specifica dalla visualizzazione **istanze** per il set di scalabilità di macchine virtuali. Nella sezione **Impostazioni** passare a **istanze**  > **Insights (anteprima)** .

## <a name="view-a-map-from-azure-monitor"></a>Visualizzare una mappa da monitoraggio di Azure

In monitoraggio di Azure, la funzionalità mappa fornisce una visualizzazione globale delle VM e delle relative dipendenze. Per accedere alla funzionalità mappa in monitoraggio di Azure:

1. Nel portale di Azure selezionare **Monitor**. 
2. Nella sezione **Insights** scegliere **macchine virtuali (anteprima)** .
3. Selezionare la scheda **Mappa**.

   ![Panoramica di monitoraggio di Azure mappa di più macchine virtuali](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Scegliere un'area di lavoro usando il selettore dell' **area di lavoro** nella parte superiore della pagina. Se si dispone di più di un'area di lavoro Log Analytics, scegliere l'area di lavoro abilitata con la soluzione e con macchine virtuali che vi fanno riferimento. 

Il selettore di **gruppo** restituisce sottoscrizioni, gruppi di risorse, [gruppi di computer](../../azure-monitor/platform/computer-groups.md)e set di scalabilità di macchine virtuali di computer correlati all'area di lavoro selezionata. La selezione viene applicata solo alla funzionalità della mappa e non a prestazioni o integrità.

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare le dipendenze in passato, è possibile eseguire una query per gli intervalli di tempo cronologici fino a un'ora. Per eseguire la query, usare il selettore **TimeRange** . È possibile eseguire una query, ad esempio, durante un evento imprevisto o visualizzare lo stato prima di una modifica.  

## <a name="next-steps"></a>Passaggi successivi

Per identificare i colli di bottiglia, verificare le prestazioni e comprendere l'utilizzo complessivo delle macchine virtuali, vedere [visualizzare lo stato delle prestazioni per monitoraggio di Azure per le macchine virtuali](vminsights-performance.md). 
