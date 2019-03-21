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
ms.date: 02/22/2019
ms.author: magoedte
ms.openlocfilehash: 7032fabd022b55bc8946a48568bbd799d4a0a5e9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731379"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Come creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali (anteprima)
Monitoraggio di Azure per le macchine virtuali include un set di grafici delle prestazioni che rappresentano diversi indicatori di prestazioni chiave (KPI) per stabilire l'efficacia delle prestazioni di una macchina virtuale. I grafici mostrano l'utilizzo delle risorse in un periodo di tempo per consentire di identificare colli di bottiglia e anomalie o passare a una prospettiva che elenchi ogni macchina per visualizzare l'utilizzo delle risorse in base alla metrica selezionata. Sebbene esistano numerosi elementi da considerare quando si lavora con le prestazioni, monitoraggio di Azure per gli indicatori di prestazioni chiave del sistema operativo di macchine virtuali monitoraggi correlati al processore, memoria, scheda di rete e utilizzo del disco. Prestazioni si integra alla funzionalità di monitoraggio dell'integrità e consente di esporre i problemi che indicano un possibile errore dei componenti di sistema, agevolare l'ottimizzazione per aumentare l'efficienza o supportare la pianificazione della capacità.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Prospettiva di più macchine virtuali da Monitoraggio di Azure
Monitoraggio di Azure, la funzionalità delle prestazioni offre una visualizzazione di tutte le macchine virtuali monitorate distribuiti in gruppi di lavoro nelle sottoscrizioni o nell'ambiente in uso. Per accedere da Monitoraggio di Azure, eseguire la procedura seguente. 

1. Nel portale di Azure selezionare **Monitoraggio**. 
2. Scegliere **Macchine virtuali (anteprima)** nella sezione **Soluzioni**.
3. Selezionare la scheda **Prestazioni**.

![Visualizzazione elenco Primi N in Prestazioni di Informazioni dettagliate macchina virtuale](./media/vminsights-performance/vminsights-performance-aggview-01.png)

Nella scheda **Primi N grafici**, se si dispone di più aree di lavoro di Log Analytics, scegliere quella abilitata con la soluzione dal selettore **Area di lavoro** nella parte superiore della pagina. Il selettore **Gruppo** restituirà le sottoscrizioni, i gruppi di risorse, i [gruppi di computer](../platform/computer-groups.md) e i set di scalabilità delle macchine virtuali dei computer correlati all'area di lavoro selezionata, che potranno essere usati per filtrare ulteriormente i risultati presentati nei grafici di questa e di altre pagine. La selezione è valida solo per la funzionalità Prestazioni e non si applica alla funzionalità Integrità o Mappa.  

Per impostazione predefinita, i grafici mostrano le ultime 24 ore. Usando il selettore **Intervallo di tempo**, è possibile eseguire una query per gli intervalli di tempo cronologici di un massimo di 30 giorni per esaminare le prestazioni di un periodo precedente.   

I cinque grafici dell'utilizzo della capacità visualizzati nella pagina sono:

* % utilizzo CPU: mostra i primi cinque computer con il valore medio più alto di utilizzo del processore 
* Memoria disponibile: mostra i primi cinque computer con il valore medio più basso di quantità di memoria disponibile 
* % spazio su disco logico utilizzato: mostra i primi cinque computer con il valore medio più alto di percentuale di spazio su disco in tutti i volumi del disco 
* Frequenza byte inviati: mostra i primi cinque computer con la media più alta di byte inviati 
* Frequenza byte ricevuti: mostra i primi cinque computer con la media più alta di byte ricevuti 

Facendo clic sull'icona della puntina nell'angolo in alto a destra di uno dei cinque grafici verrà aggiunto il grafico selezionato per l'ultimo dashboard di Azure, che l'ultima visualizzazione.  Dal dashboard, è possibile ridimensionare e riposizionare il grafico. Selezionando il grafico nel dashboard verrà reindirizzati al monitoraggio di Azure per le macchine virtuali e caricare l'ambito corretto e la vista.  

Facendo clic sull'icona a sinistra dell'icona del pin su uno qualsiasi dei cinque grafici si apre la **Top N elenco** visualizzazione.  Qui si vede l'utilizzo delle risorse per tale metrica delle prestazioni per singola macchina virtuale in una visualizzazione elenco e quale macchina ha la tendenza più alta.  

![Visualizzazione Elenco primi N per una metrica delle prestazioni selezionata](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando si fa clic sulla macchina virtuale, il riquadro **Proprietà** si espande a destra per visualizzare le proprietà dell'elemento selezionato, ad esempio le informazioni di sistema segnalate dal sistema operativo, le proprietà della macchina virtuale di Azure e così via. Facendo clic su una delle opzioni nella sezione **Collegamenti rapidi** si viene direttamente reindirizzati a tale funzionalità dalla macchina virtuale selezionata.  

![Riquadro Proprietà macchina virtuale](./media/vminsights-performance/vminsights-properties-pane-01.png)

Passare alla scheda **Grafici aggregati** per visualizzare le metriche delle prestazioni filtrate per media o percentile.  

![Visualizzazione aggregata delle prestazioni di Informazioni dettagliate macchina virtuale](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Vengono forniti i grafici di utilizzo della capacità seguenti:

* % utilizzo CPU: per impostazione predefinita mostra la media e il 95° percentile 
* Memoria disponibile - impostazioni predefinite che mostra il percentile-5 e 10, medio e superiore 
* % spazio su disco logico utilizzato: per impostazione predefinita mostra la media e il 95° percentile 
* Frequenza byte inviati: per impostazione predefinita mostra il numero medio di byte inviati 
* Frequenza byte ricevuti: per impostazione predefinita mostra il numero medio di byte ricevuti

È anche possibile modificare la granularità dei grafici entro l'intervallo di tempo selezionando **Media**, **Min**, **Max**, **50°**, **90°** e **95°** nel selettore del percentile.   

Per visualizzare l'utilizzo delle risorse per singole macchine virtuali e vedere quale macchina ha la tendenza di utilizzo più alta, selezionare la scheda **Elenco primi N**.  La pagina **Elenco primi N** mostra le prime 20 macchine ordinate a partire dalla più utilizzata per il 95° percentile per la metrica *% utilizzo CPU*.  È possibile vedere più macchine selezionando **Carica altro**; i risultati si espandono per mostrare le prime 500 macchine. 

>[!NOTE]
>L'elenco non può visualizzare più di 500 macchine alla volta.  
>

![Esempio di pagina Elenco primi N](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Per filtrare i risultati in una macchina virtuale specifica nell'elenco, immettere il relativo nome di computer nella casella di testo **Cerca per nome**.  

Per visualizzare l'utilizzo in base a una diversa metrica delle prestazioni, dall'elenco a discesa **Metrica** selezionare **Memoria disponibile**, **% spazio su disco logico utilizzato**, **Byte di rete ricevuti/sec** oppure **Byte di rete inviati/sec** e l'elenco viene aggiornato per mostrare l'utilizzo nell'ambito di tale metrica.  

Selezionando una macchina virtuale dall'elenco si apre il riquadro **Proprietà** sul lato destro della pagina dal quale è possibile selezionare **Dettagli prestazioni**.  Si apre la pagina **Dettagli macchina virtuale** il cui ambito è la macchina virtuale in questione. L'esperienza è analoga all'accesso a Prestazioni di Informazioni dettagliate macchina virtuale direttamente dalla macchina virtuale di Azure.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Visualizzare le prestazioni direttamente da una macchina virtuale di Azure
Per accedere direttamente da una macchina virtuale, eseguire la procedura seguente.

1. Nel portale di Azure selezionare **Macchine virtuali**. 
2. Nell'elenco scegliere una macchina virtuale e nella sezione **Monitoraggio** scegliere **Informazioni dettagliate (anteprima)**.  
3. Selezionare la scheda **Prestazioni**. 

Questa pagina non solo include i grafici di utilizzo delle prestazioni, ma anche una tabella che mostra capacità, utilizzo e media totale di ogni disco logico individuato per ogni misura.  

Vengono forniti i grafici di utilizzo della capacità seguenti:

* % utilizzo CPU: per impostazione predefinita mostra la media e il 95° percentile 
* Memoria disponibile - impostazioni predefinite che mostra il percentile-5 e 10, medio e superiore 
* % spazio su disco logico utilizzato: per impostazione predefinita mostra la media e il 95° percentile 
* Operazioni di I/O al secondo disco logico: per impostazione predefinita mostra la media e il 95° percentile
* MB/sec disco logico: per impostazione predefinita mostra la media e il 95° percentile
* % utilizzo massimo disco logico: per impostazione predefinita mostra la media e il 95° percentile
* Frequenza byte inviati: per impostazione predefinita mostra il numero medio di byte inviati 
* Frequenza byte ricevuti: per impostazione predefinita mostra il numero medio di byte ricevuti

Facendo clic sull'icona della puntina nell'angolo in alto a destra di uno qualsiasi dei perni grafici il grafico selezionato per l'ultimo dashboard di Azure è visualizzato. Dal dashboard, è possibile ridimensionare e riposizionare il grafico. Selezionando il grafico nel dashboard, si viene reindirizzati a monitoraggio di Azure per le macchine virtuali e carica la visualizzazione Dettagli prestazioni per la macchina virtuale.  

![Vista di Prestazioni di Informazioni dettagliate macchina virtuale direttamente dalla macchina virtuale](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="alerts"></a>Avvisi  
Le metriche delle prestazioni abilitate nell'ambito di Monitoraggio di Azure per macchine virtuali non includono regole di avviso preconfigurate. Esistono [gli avvisi sull'integrità](vminsights-health.md#alerts) corrispondente a problemi di prestazioni rilevati nella VM di Azure, ad esempio un utilizzo elevato della CPU, spazio su disco insufficiente, disponibili di memoria insufficiente, e così via.  Tuttavia, questi avvisi di integrità si applicano solo a tutte le macchine virtuali abilitate per monitoraggio di Azure per le macchine virtuali. 

È possibile tuttavia raccogliere e archiviare solo un subset delle metriche delle prestazioni necessarie nell'area di lavoro Log Analytics. Se la strategia di monitoraggio richiede analisi o avvisi con altre metriche delle prestazioni per valutare in modo efficace la capacità o l'integrità della macchina virtuale o è necessaria la flessibilità di specificare dei criteri o una logica per gli avvisi personalizzata, è possibile configurare la [raccolta dei contatori delle prestazioni](../platform/data-sources-performance-counters.md) in Log Analytics e definire gli [avvisi dei log](../platform/alerts-log.md). Mentre Log Analytics consente di eseguire un'analisi complessa con altri tipi di dati e di offrire un periodo di conservazione più lungo per l'analisi delle tendenze, le metriche sono leggere e in grado di supportare scenari quasi in tempo reale. Vengono raccolte dall'[agente Diagnostica di Azure](../../virtual-machines/windows/monitor.md) e memorizzate nell'archivio metriche di Monitoraggio di Azure permettendo di creare avvisi con minor latenza e costi inferiori.

Rivedere la panoramica della [raccolta di metriche e log con Monitoraggio di Azure](../platform/data-collection.md) per comprendere le differenze fondamentali e altre considerazioni prima di configurare la raccolta di queste metriche aggiuntive e delle regole di avviso.  

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come usare la funzionalità di integrità, vedere [Visualizzare l'integrità di Monitoraggio di Azure per le macchine virtuali](vminsights-health.md) oppure per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). 