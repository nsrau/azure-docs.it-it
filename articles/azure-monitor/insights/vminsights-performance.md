---
title: Come creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Prestazioni è una funzionalità di Monitoraggio di Azure per le macchine virtuali che rileva automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione tra i servizi. Questo articolo contiene informazioni dettagliate su come usare la funzionalità in un'ampia gamma di scenari.
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
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: cdfc0115beecd69ec50e8b7fd026563d145e1761
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515286"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Come creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali (anteprima)

Monitoraggio di Azure per le macchine virtuali include un set di grafici delle prestazioni che rappresentano diversi indicatori di prestazioni chiave (KPI) per stabilire l'efficacia delle prestazioni di una macchina virtuale. I grafici mostrano l'utilizzo delle risorse in un periodo di tempo per consentire di identificare colli di bottiglia e anomalie o passare a una prospettiva che elenchi ogni macchina per visualizzare l'utilizzo delle risorse in base alla metrica selezionata. Sebbene esistano numerosi elementi da considerare quando si gestiscono le prestazioni, Monitoraggio di Azure per le macchine virtuali monitora gli indicatori di prestazioni chiave del sistema operativo correlati al processore, alla memoria, alla scheda di rete e all'utilizzo del disco. Prestazioni si integra alla funzionalità di monitoraggio dell'integrità e consente di esporre i problemi che indicano un possibile errore dei componenti di sistema, agevolare l'ottimizzazione per aumentare l'efficienza o supportare la pianificazione della capacità.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Prospettiva di più macchine virtuali da Monitoraggio di Azure

Da monitoraggio di Azure, la funzionalità prestazioni fornisce una visualizzazione di tutte le macchine virtuali monitorate distribuite tra gruppi di lavoro nelle sottoscrizioni o nell'ambiente in uso. Per accedere da Monitoraggio di Azure, eseguire la procedura seguente. 

1. Nel portale di Azure selezionare **Monitor**. 
2. Scegliere **Macchine virtuali (anteprima)** nella sezione **Soluzioni**.
3. Selezionare la scheda **Prestazioni**.

![Visualizzazione elenco Primi N in Prestazioni di Informazioni dettagliate macchina virtuale](./media/vminsights-performance/vminsights-performance-aggview-01.png)

Nella scheda **Primi N grafici**, se si dispone di più aree di lavoro Log Analytics, scegliere quella abilitata con la soluzione dal selettore **Area di lavoro** nella parte superiore della pagina. Il selettore **Gruppo** restituirà le sottoscrizioni, i gruppi di risorse, i [gruppi di computer](../platform/computer-groups.md) e i set di scalabilità delle macchine virtuali dei computer correlati all'area di lavoro selezionata, che potranno essere usati per filtrare ulteriormente i risultati presentati nei grafici di questa e di altre pagine. La selezione è valida solo per la funzionalità Prestazioni e non si applica alla funzionalità Integrità o Mappa.  

Per impostazione predefinita, i grafici mostrano le ultime 24 ore. Usando il selettore **Intervallo di tempo**, è possibile eseguire una query per gli intervalli di tempo cronologici di un massimo di 30 giorni per esaminare le prestazioni di un periodo precedente.

I cinque grafici dell'utilizzo della capacità visualizzati nella pagina sono:

* % utilizzo CPU: mostra i primi cinque computer con il valore medio più alto di utilizzo del processore 
* Memoria disponibile: mostra i primi cinque computer con il valore medio più basso di quantità di memoria disponibile 
* % spazio su disco logico utilizzato: mostra i primi cinque computer con il valore medio più alto di percentuale di spazio su disco in tutti i volumi del disco 
* Frequenza byte inviati: mostra i primi cinque computer con la media più alta di byte inviati 
* Frequenza byte ricevuti: mostra i primi cinque computer con la media più alta di byte ricevuti 

Facendo clic sull'icona Aggiungi nell'angolo superiore destro di uno dei cinque grafici, il grafico selezionato viene aggiunto all'ultimo dashboard di Azure visualizzato per ultimo.  Dal dashboard è possibile ridimensionare e riposizionare il grafico. Selezionando il grafico dal dashboard si reindirizza l'utente a Monitoraggio di Azure per le macchine virtuali e si caricano l'ambito e la visualizzazione corretti.  

Facendo clic sull'icona che si trova a sinistra dell'icona Aggiungi in uno dei cinque grafici viene aperta la visualizzazione **elenco superiore N** .  Qui si vede l'utilizzo delle risorse per tale metrica delle prestazioni per singola macchina virtuale in una visualizzazione elenco e quale macchina ha la tendenza più alta.  

![Visualizzazione Elenco primi N per una metrica delle prestazioni selezionata](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando si fa clic sulla macchina virtuale, il riquadro **Proprietà** viene espanso a destra per visualizzare le proprietà dell'elemento selezionato, ad esempio le informazioni di sistema segnalate dal sistema operativo, le proprietà della VM di Azure e così via. Quando si fa clic su una delle opzioni disponibili nella sezione **collegamenti rapidi** viene reindirizzata la funzionalità direttamente dalla macchina virtuale selezionata.  

![Riquadro Proprietà macchina virtuale](./media/vminsights-performance/vminsights-properties-pane-01.png)

Passare alla scheda **Grafici aggregati** per visualizzare le metriche delle prestazioni filtrate per media o percentile.  

![Visualizzazione aggregata delle prestazioni di Informazioni dettagliate macchina virtuale](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Vengono forniti i grafici di utilizzo della capacità seguenti:

* % utilizzo CPU: per impostazione predefinita mostra la media e il 95° percentile 
* Memoria disponibile: valori predefiniti che mostrano la media, il quinto e il 10 ° percentile 
* % spazio su disco logico utilizzato: per impostazione predefinita mostra la media e il 95° percentile 
* Frequenza byte inviati: per impostazione predefinita mostra il numero medio di byte inviati 
* Frequenza byte ricevuti: per impostazione predefinita mostra il numero medio di byte ricevuti

È anche possibile modificare la granularità dei grafici entro l'intervallo di tempo selezionando **Media**, **Min**, **Max**, **50°**, **90°** e **95°** nel selettore del percentile.

Per visualizzare l'utilizzo delle risorse da parte di una singola macchina virtuale in una visualizzazione elenco e vedere quale computer sta eseguendo la tendenza con l'utilizzo più elevato, selezionare la scheda **Top N list** .  La pagina **Top N list** Mostra i primi 20 computer ordinati in base al 95 ° percentile per la percentuale di *utilizzo della CPU*metrica.  È possibile vedere più macchine selezionando **Carica altro**; i risultati si espandono per mostrare le prime 500 macchine. 

>[!NOTE]
>L'elenco non può visualizzare più di 500 macchine alla volta.  
>

![Esempio di pagina Elenco primi N](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Per filtrare i risultati in una macchina virtuale specifica nell'elenco, immettere il relativo nome di computer nella casella di testo **Cerca per nome**.  

Per visualizzare l'utilizzo in base a una diversa metrica delle prestazioni, dall'elenco a discesa **Metrica** selezionare **Memoria disponibile**, **% spazio su disco logico utilizzato**, **Byte di rete ricevuti/sec** oppure **Byte di rete inviati/sec** e l'elenco viene aggiornato per mostrare l'utilizzo nell'ambito di tale metrica.  

Selezionando una macchina virtuale dall'elenco si apre il riquadro **Proprietà** sul lato destro della pagina dal quale è possibile selezionare **Dettagli prestazioni**.  Si apre la pagina **Dettagli macchina virtuale** il cui ambito è la macchina virtuale in questione. L'esperienza è analoga all'accesso a Prestazioni di Informazioni dettagliate macchina virtuale direttamente dalla macchina virtuale di Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Visualizzare le prestazioni direttamente da una macchina virtuale di Azure

Per accedere direttamente da una macchina virtuale, seguire questa procedura.

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Dall'elenco scegliere una macchina virtuale e nella sezione **Monitoraggio** scegliere **Informazioni dettagliate (anteprima)** .  
3. Selezionare la scheda **Prestazioni**. 

Questa pagina non solo include i grafici di utilizzo delle prestazioni, ma anche una tabella che mostra capacità, utilizzo e media totale di ogni disco logico individuato per ogni misura.  

Vengono forniti i grafici di utilizzo della capacità seguenti:

* % utilizzo CPU: per impostazione predefinita mostra la media e il 95° percentile 
* Memoria disponibile: valori predefiniti che mostrano la media, il quinto e il 10 ° percentile 
* % spazio su disco logico utilizzato: per impostazione predefinita mostra la media e il 95° percentile 
* Operazioni di I/O al secondo disco logico: per impostazione predefinita mostra la media e il 95° percentile
* MB/sec disco logico: per impostazione predefinita mostra la media e il 95° percentile
* % utilizzo massimo disco logico: per impostazione predefinita mostra la media e il 95° percentile
* Frequenza byte inviati: per impostazione predefinita mostra il numero medio di byte inviati 
* Frequenza byte ricevuti: per impostazione predefinita mostra il numero medio di byte ricevuti

Facendo clic sull'icona Aggiungi nell'angolo superiore destro di uno dei grafici, il grafico selezionato viene aggiunto all'ultimo dashboard di Azure visualizzato. Dal dashboard è possibile ridimensionare e riposizionare il grafico. Se si seleziona il grafico dal dashboard, viene reindirizzato a Monitoraggio di Azure per le macchine virtuali e viene caricata la visualizzazione Dettagli prestazioni per la macchina virtuale.  

![Vista di Prestazioni di Informazioni dettagliate macchina virtuale direttamente dalla macchina virtuale](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Visualizzare le prestazioni direttamente da un set di scalabilità di macchine virtuali di Azure

Per accedere direttamente da un set di scalabilità di macchine virtuali di Azure, seguire questa procedura.

1. Nella portale di Azure selezionare set di **scalabilità di macchine virtuali**.
2. Dall'elenco scegliere una macchina virtuale e nella sezione **monitoraggio** scegliere **Insights (anteprima)** per visualizzare la scheda **prestazioni** .

Questa pagina carica la vista prestazioni di monitoraggio di Azure, con ambito nel set di scalabilità selezionato. Ciò consente di visualizzare le prime N istanze nel set di scalabilità nel set di metriche monitorate, visualizzare le prestazioni aggregate nel set di scalabilità e visualizzare le tendenze per le metriche selezionate tra le singole istanze N il set di scalabilità. Selezionando un'istanza dalla visualizzazione elenco è possibile caricare la mappa o spostarsi in una visualizzazione dettagliata delle prestazioni per tale istanza.

Facendo clic sull'icona Aggiungi nell'angolo superiore destro di uno dei grafici, il grafico selezionato viene aggiunto all'ultimo dashboard di Azure visualizzato. Dal dashboard è possibile ridimensionare e riposizionare il grafico. Se si seleziona il grafico dal dashboard, viene reindirizzato a Monitoraggio di Azure per le macchine virtuali e viene caricata la visualizzazione Dettagli prestazioni per la macchina virtuale.  

![Prestazioni di VM Insights direttamente dalla visualizzazione del set di scalabilità di macchine virtuali](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>È anche possibile accedere a una visualizzazione dettagliata delle prestazioni per un'istanza specifica dalla visualizzazione istanze per il set di scalabilità. Passare a **istanze** nella sezione **Impostazioni** , quindi scegliere **Insights (anteprima)** .

## <a name="alerts"></a>Avvisi  

Le metriche delle prestazioni abilitate nell'ambito di Monitoraggio di Azure per macchine virtuali non includono regole di avviso preconfigurate. Sono presenti [avvisi di integrità](vminsights-health.md#alerts) corrispondenti ai problemi di prestazioni rilevati nella macchina virtuale di Azure, ad esempio un utilizzo elevato della CPU, una memoria insufficiente, spazio su disco insufficiente e così via.  Tuttavia, questi avvisi di integrità si applicano solo a tutte le macchine virtuali abilitate per Monitoraggio di Azure per le macchine virtuali. 

È possibile tuttavia raccogliere e archiviare solo un subset delle metriche delle prestazioni necessarie nell'area di lavoro Log Analytics. Se la strategia di monitoraggio richiede analisi o avvisi con altre metriche delle prestazioni per valutare in modo efficace la capacità o l'integrità della macchina virtuale o è necessaria la flessibilità di specificare dei criteri o una logica per gli avvisi personalizzata, è possibile configurare la [raccolta dei contatori delle prestazioni](../platform/data-sources-performance-counters.md) in Log Analytics e definire gli [avvisi dei log](../platform/alerts-log.md). Mentre Log Analytics consente di eseguire un'analisi complessa con altri tipi di dati e di offrire un periodo di conservazione più lungo per l'analisi delle tendenze, le metriche sono leggere e in grado di supportare scenari quasi in tempo reale. Vengono raccolte dall'[agente Diagnostica di Azure](../../virtual-machines/windows/monitor.md) e memorizzate nell'archivio metriche di Monitoraggio di Azure permettendo di creare avvisi con minor latenza e costi inferiori.

Rivedere la panoramica della [raccolta di metriche e log con Monitoraggio di Azure](../platform/data-platform.md) per comprendere le differenze fondamentali e altre considerazioni prima di configurare la raccolta di queste metriche aggiuntive e delle regole di avviso.  

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare le [cartelle di lavoro](vminsights-workbooks.md) incluse in monitoraggio di Azure per le macchine virtuali per analizzare ulteriormente le metriche relative a prestazioni e rete.  

- Per informazioni sulle dipendenze dell'applicazione individuate, vedere [visualizzare monitoraggio di Azure per le macchine virtuali mappa](vminsights-maps.md).
