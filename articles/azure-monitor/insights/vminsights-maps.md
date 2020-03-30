---
title: Visualizzare le dipendenze delle app con Monitoraggio di Azure per le macchine virtualiView app dependencies with Azure Monitor for VMs
description: La mappa è una funzionalità di Monitoraggio di Azure per le macchine virtuali. Rileva automaticamente i componenti dell'applicazione nei sistemi Windows e Linux ed esegue automaticamente il mapping della comunicazione tra i servizi. In questo articolo vengono fornite informazioni dettagliate su come utilizzare la funzionalità di mappa in vari scenari.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283855"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Usare la funzionalità Mappa di Monitoraggio di Azure per le macchine virtuali per comprendere i componenti dell'applicazioneUse the Map feature of Azure Monitor for VMs to understand application components
In Monitoraggio di Azure per le macchine virtuali è possibile visualizzare i componenti dell'applicazione individuati nelle macchine virtuali (VM) Windows e Linux eseguite in Azure o nell'ambiente. È possibile osservare le macchine virtuali in due modi. Visualizzare una mappa direttamente da una macchina virtuale o visualizzare una mappa da Monitoraggio di Azure per visualizzare i componenti tra gruppi di macchine virtuali. Questo articolo ti aiuterà a comprendere questi due metodi di visualizzazione e come utilizzare la funzione Mappa. 

Per informazioni sulla configurazione di Monitoraggio di Azure per le macchine virtuali, vedere [Enable Azure Monitor for VMs](vminsights-enable-overview.md) (Abilitare Monitoraggio di Azure per le macchine virtuali).

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Accedere al [portale](https://portal.azure.com)di Azure .

## <a name="introduction-to-the-map-experience"></a>Introduzione all'esperienza della mappa
Prima di tuffarsi nell'esperienza della mappa, è necessario comprendere come presenta e visualizza le informazioni. Se si seleziona la funzionalità mappa direttamente da una macchina virtuale o da Monitoraggio di Azure, la funzionalità mappa offre un'esperienza coerente. L'unica differenza è che da Monitoraggio di Azure, una mappa mostra tutti i membri di un'applicazione o di un cluster a più livelli.

La funzionalità Mappa visualizza le dipendenze delle macchine virtuali individuando i processi in esecuzione con:The Map feature visualizes the VM dependencies by discovering running processes that have: 

- Connessioni di rete attive tra i server.
- Latenza delle connessioni in ingresso e in uscita.
- Porte su qualsiasi architettura connessa a TCP in un intervallo di tempo specificato.  
 
Espandere una macchina virtuale per visualizzare i dettagli del processo e solo i processi che comunicano con la macchina virtuale. Il gruppo di client mostra il numero di client front-end che si connettono alla macchina virtuale. I gruppi server-porta mostrano il numero di server back-end a cui la macchina virtuale si connette. Espandere un gruppo server-porta per visualizzare l'elenco dettagliato dei server che si connettono tramite tale porta.  

Quando si seleziona la macchina virtuale, il riquadro **Proprietà a** destra mostra le proprietà della macchina virtuale. Le proprietà includono le informazioni di sistema segnalate dal sistema operativo, le proprietà della macchina virtuale di Azure e un grafico ad anello che riepiloga le connessioni individuate. 

![Riquadro Proprietà](./media/vminsights-maps/properties-pane-01.png)

Sul lato destro del riquadro selezionare Registra eventi per visualizzare un elenco di dati inviati dalla macchina virtuale ad Azure Monitor.On the right side of the pane, select **Log Events** to show a list of data that the VM has sent to Azure Monitor. Questi dati sono disponibili per l'esecuzione di query.  Selezionare un tipo di record per aprire la pagina **Registri,** in cui vengono visualizzati i risultati per tale tipo di record. Viene inoltre visualizzata una query preconfigurata che viene filtrata in base alla macchina virtuale.  

![Riquadro Registra eventi](./media/vminsights-maps/properties-pane-logs-01.png)

Chiudere la pagina **Registri** e tornare al riquadro **Proprietà.** Selezionare **Avvisi** per visualizzare gli avvisi dei criteri di integrità della macchina virtuale. La funzionalità Mappa si integra con Avvisi di Azure per visualizzare gli avvisi per il server selezionato nell'intervallo di tempo selezionato. Il server visualizza un'icona per gli avvisi correnti e il riquadro **Avvisi computer** elenca gli avvisi. 

![Riquadro Avvisi](./media/vminsights-maps/properties-pane-alerts-01.png)

Per fare in modo che la funzione Mappa visualizzi avvisi pertinenti, creare una regola di avviso che si applica a un computer specifico:

- Includere una clausola per raggruppare gli avvisi in base al computer, ad esempio **in base all'intervallo di computer 1 minuto.**
- Basare l'avviso su una metrica.

Per altre informazioni sugli avvisi di Azure e sulla creazione di regole di avviso, vedere [Avvisi unificati in Monitoraggio di Azure.For](../../azure-monitor/platform/alerts-overview.md)more information about Azure Alerts and creating alert rules, see Unified alerts in Azure Monitor.

Nell'angolo superiore destro, l'opzione **Legenda** descrive i simboli e i ruoli sulla mappa. Per un'occhiata più da vicino alla mappa e per spostarla, usa i controlli di zoom nell'angolo in basso a destra. È possibile impostare il livello di zoom e adattare la mappa alle dimensioni della pagina.  

## <a name="connection-metrics"></a>Metriche di connessione
Nel riquadro **Connessioni** vengono visualizzate le metriche standard per la connessione selezionata dalla macchina virtuale tramite la porta TCP. Le metriche includono tempo di risposta, richieste al minuto, velocità effettiva del traffico e collegamenti.  

![Grafici di connettività di rete nel riquadro Connessioni](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Connessioni non riuscite
La mappa mostra le connessioni non riuscite per processi e computer. Una linea rossa tratteggiata indica che un sistema client non riesce a raggiungere un processo o una porta. Per i sistemi che utilizzano l'agente di dipendenza, l'agente segnala i tentativi di connessione non riusciti. La funzionalità Mappa esegue il monitoraggio di un processo osservando i socket TCP che non riescono a stabilire una connessione. Questo errore potrebbe derivare da un firewall, da un errore di configurazione nel client o nel server o da un servizio remoto non disponibile.

![Una connessione non riuscita sulla mappa](./media/vminsights-maps/map-group-failed-connection-01.png)

La comprensione delle connessioni non riuscite consente di risolvere i problemi, convalidare la migrazione, analizzare la sicurezza e comprendere l'architettura complessiva del servizio. Le connessioni non riuscite sono talvolta innocue, ma spesso indicano un problema. Le connessioni potrebbero non riuscire, ad esempio, quando un ambiente di failover diventa improvvisamente irraggiungibile o quando due livelli applicazione non possono comunicare tra loro dopo una migrazione cloud.

### <a name="client-groups"></a>Gruppi di client
Sulla mappa, i gruppi client rappresentano i computer client che si connettono al computer mappato. Un singolo gruppo di client rappresenta i client per un singolo processo o computer.

![Un gruppo di clienti sulla mappa](./media/vminsights-maps/map-group-client-groups-01.png)

Per visualizzare i client monitorati e gli indirizzi IP dei sistemi in un gruppo di client, selezionare il gruppo. Il contenuto del gruppo viene visualizzato di seguito.  

![Elenco di indirizzi IP di un gruppo di client sulla mappa](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Se il gruppo include client monitorati e non monitorati, è possibile selezionare la sezione appropriata del grafico ad anello del gruppo per filtrare i client.

### <a name="server-port-groups"></a>Gruppi porte server
I gruppi porte server rappresentano le porte nei server con connessioni in ingresso dal computer mappato. Il gruppo contiene la porta del server e un conteggio del numero di server che dispongono di connessioni a tale porta. Selezionare il gruppo per visualizzare i singoli server e le singole connessioni. 

![Un gruppo di porte server sulla mappa](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Se il gruppo include server monitorati e non monitorati, è possibile selezionare la sezione appropriata del grafico ad anello del gruppo per filtrare i server.

## <a name="view-a-map-from-a-vm"></a>Visualizzare una mappa da una macchina virtualeView a map from a VM 

Per accedere a Monitoraggio di Azure per le macchine virtuali direttamente da una macchina virtuale:To access Azure Monitor for VMs directly from a VM:

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Nell'elenco scegliere una macchina virtuale. Nella sezione **Monitoraggio** scegliere **Insights**.  
3. Selezionare la scheda **Mappa**.

La mappa visualizza le dipendenze della macchina virtuale individuando i gruppi di processi in esecuzione e i processi con connessioni di rete attive in un intervallo di tempo specificato.  

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare l'aspetto delle dipendenze in passato, è possibile eseguire una query per intervalli di tempo cronologici fino a un'ora. Per eseguire la query, utilizzare il selettore **TimeRange** nell'angolo superiore sinistro. È possibile eseguire una query, ad esempio, durante un evento imprevisto o per visualizzare lo stato prima di una modifica.  

![Panoramica di mappe direttamente da macchina virtuale](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Visualizzare una mappa da un set di scalabilità di macchine virtualiView a map from a virtual machine scale set

Per accedere a Monitoraggio di Azure per le macchine virtuali direttamente da un set di scalabilità di macchine virtuali:To access Azure Monitor for VMs directly from a virtual machine scale set:

1. Nel portale di Azure selezionare Set di **scalabilità macchina virtuale**.
2. Nell'elenco scegliere una macchina virtuale. Nella sezione **Monitoraggio** scegliere **Insights**.  
3. Selezionare la scheda **Mappa**.

La mappa visualizza tutte le istanze nel set di scalabilità come nodo di gruppo insieme alle dipendenze del gruppo. Il nodo espanso elenca le istanze nel set di scalabilità. È possibile scorrere queste istanze 10 alla volta. 

Per caricare una mappa per un'istanza specifica, selezionare innanzitutto tale istanza sulla mappa. Selezionare quindi il pulsante con **i conielli** (...) a destra e scegliere **Carica mappa server**. Nella mappa visualizzata vengono visualizzati i gruppi di processi e i processi con connessioni di rete attive in un intervallo di tempo specificato. 

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare l'aspetto delle dipendenze in passato, è possibile eseguire una query per intervalli di tempo cronologici fino a un'ora. Per eseguire la query, utilizzare il selettore **TimeRange.** È possibile eseguire una query, ad esempio, durante un evento imprevisto o per visualizzare lo stato prima di una modifica.

![Panoramica di mappe direttamente da macchina virtuale](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>È anche possibile accedere a una mappa per un'istanza specifica dalla visualizzazione **Istanze** per il set di scalabilità della macchina virtuale. Nella sezione **Impostazioni** passare a **Informazioni dettagliate sulle istanze** > **Insights**.

## <a name="view-a-map-from-azure-monitor"></a>Visualizzare una mappa da Monitoraggio di AzureView a map from Azure Monitor

In Monitoraggio di Azure la funzionalità Mappa offre una visualizzazione globale delle macchine virtuali e delle relative dipendenze. Per accedere alla funzionalità Mappa in Monitoraggio di Azure:To access the Map feature in Azure Monitor:

1. Nel portale di Azure selezionare **Monitor .** 
2. Nella sezione **Insights** scegliere **Macchine virtuali**.
3. Selezionare la scheda **Mappa**.

   ![Azure Monitor overview map of multiple VMs](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Scegliere un'area di lavoro utilizzando il selettore **Area di lavoro** nella parte superiore della pagina. Se si dispone di più aree di lavoro di Log Analytics, scegliere l'area di lavoro abilitata con la soluzione e con macchine virtuali che segnalano a essa. 

Il selettore **Gruppo** restituisce sottoscrizioni, gruppi di risorse, gruppi di [computer](../../azure-monitor/platform/computer-groups.md)e set di scalabilità di macchine virtuali correlati all'area di lavoro selezionata. La selezione si applica solo alla funzione Mappa e non viene riportata a Prestazioni o Salute.

Per impostazione predefinita, la mappa visualizza gli ultimi 30 minuti. Se si desidera visualizzare l'aspetto delle dipendenze in passato, è possibile eseguire una query per intervalli di tempo cronologici fino a un'ora. Per eseguire la query, utilizzare il selettore **TimeRange.** È possibile eseguire una query, ad esempio, durante un evento imprevisto o per visualizzare lo stato prima di una modifica.  

## <a name="next-steps"></a>Passaggi successivi

Per identificare colli di bottiglia, controllare le prestazioni e comprendere l'utilizzo complessivo delle macchine virtuali, vedere [Visualizzare lo stato delle prestazioni per Monitoraggio di Azure per le macchine virtuali.](vminsights-performance.md) 
