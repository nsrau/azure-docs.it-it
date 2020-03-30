---
title: Creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali
description: Prestazioni è una funzionalità di Monitoraggio di Azure per le macchine virtuali che rileva automaticamente i componenti delle applicazioni nei sistemi Windows e Linux e mappa la comunicazione tra i servizi. Questo articolo contiene informazioni dettagliate su come usare la funzionalità in un'ampia gamma di scenari.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283719"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Creare un grafico delle prestazioni con Monitoraggio di Azure per le macchine virtuali

Monitoraggio di Azure per le macchine virtuali include un set di grafici delle prestazioni che rappresentano diversi indicatori di prestazioni chiave (KPI) per stabilire l'efficacia delle prestazioni di una macchina virtuale. I grafici mostrano l'utilizzo delle risorse in un periodo di tempo per consentire di identificare colli di bottiglia e anomalie o passare a una prospettiva che elenchi ogni macchina per visualizzare l'utilizzo delle risorse in base alla metrica selezionata. Sebbene esistano numerosi elementi da considerare quando si gestiscono le prestazioni, Monitoraggio di Azure per macchine virtuali monitora gli indicatori chiave delle prestazioni del sistema operativo relativi all'utilizzo di processori, memoria, scheda di rete e disco. Prestazioni si integra alla funzionalità di monitoraggio dell'integrità e consente di esporre i problemi che indicano un possibile errore dei componenti di sistema, agevolare l'ottimizzazione per aumentare l'efficienza o supportare la pianificazione della capacità.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Prospettiva di più macchine virtuali da Monitoraggio di Azure

Da Monitoraggio di Azure, la funzionalità Prestazioni offre una visualizzazione di tutte le macchine virtuali monitorate distribuite tra gruppi di lavoro nelle sottoscrizioni o nell'ambiente. Per accedere da Monitoraggio di Azure, eseguire la procedura seguente. 

1. Nel portale di Azure selezionare **Monitor .** 
2. Scegliere Macchine virtuali nella sezione **Soluzioni.Choose** **Virtual Machines** in the Solutions section.
3. Selezionare la scheda **Prestazioni**.

![Visualizzazione elenco Primi N in Prestazioni di Informazioni dettagliate macchina virtuale](media/vminsights-performance/vminsights-performance-aggview-01.png)

Nella scheda **Primi N grafici**, se si dispone di più aree di lavoro Log Analytics, scegliere quella abilitata con la soluzione dal selettore **Area di lavoro** nella parte superiore della pagina. Il selettore **Gruppo** restituirà le sottoscrizioni, i gruppi di risorse, i [gruppi di computer](../platform/computer-groups.md) e i set di scalabilità delle macchine virtuali dei computer correlati all'area di lavoro selezionata, che potranno essere usati per filtrare ulteriormente i risultati presentati nei grafici di questa e di altre pagine. La selezione è valida solo per la funzionalità Prestazioni e non si applica alla funzionalità Integrità o Mappa.  

Per impostazione predefinita, i grafici mostrano le ultime 24 ore. Usando il selettore **Intervallo di tempo**, è possibile eseguire una query per gli intervalli di tempo cronologici di un massimo di 30 giorni per esaminare le prestazioni di un periodo precedente.

I cinque grafici dell'utilizzo della capacità visualizzati nella pagina sono:

* % utilizzo CPU: mostra i primi cinque computer con il valore medio più alto di utilizzo del processore 
* Memoria disponibile: mostra i primi cinque computer con il valore medio più basso di quantità di memoria disponibile 
* % spazio su disco logico utilizzato: mostra i primi cinque computer con il valore medio più alto di percentuale di spazio su disco in tutti i volumi del disco 
* Frequenza byte inviati: mostra i primi cinque computer con la media più alta di byte inviati 
* Frequenza di ricezione byte - mostra i primi cinque computer con la media più alta di byte ricevuti 

Facendo clic sull'icona a forma di puntina nell'angolo superiore destro di uno qualsiasi dei cinque grafici, il grafico selezionato verrà acposto all'ultimo dashboard di Azure visualizzato per l'ultima volta.  Dal dashboard, è possibile ridimensionare e riposizionare il grafico. Se si seleziona il grafico dal dashboard, si reindirizzerà ad Azure Monitor per le macchine virtuali e si caricherà l'ambito e la visualizzazione corretti.  

Facendo clic sull'icona a sinistra dell'icona a forma di puntina su uno qualsiasi dei cinque grafici si apre la visualizzazione **Top N List.**  Qui si vede l'utilizzo delle risorse per tale metrica delle prestazioni per singola macchina virtuale in una visualizzazione elenco e quale macchina ha la tendenza più alta.  

![Visualizzazione Elenco primi N per una metrica delle prestazioni selezionata](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Quando si fa clic sulla macchina virtuale, il riquadro **Proprietà** viene espanso a destra per visualizzare le proprietà dell'elemento selezionato, ad esempio le informazioni di sistema segnalate dal sistema operativo, le proprietà della macchina virtuale di Azure e così via. Facendo clic su una delle opzioni nella sezione **Collegamenti rapidi** verrà reindirizzata a tale funzionalità direttamente dalla macchina virtuale selezionata.  

![Riquadro Proprietà macchina virtuale](./media/vminsights-performance/vminsights-properties-pane-01.png)

Passare alla scheda **Grafici aggregati** per visualizzare le metriche delle prestazioni filtrate per media o percentile.  

![Visualizzazione aggregata delle prestazioni di Informazioni dettagliate macchina virtuale](./media/vminsights-performance/vminsights-performance-aggview-02.png)

Vengono forniti i grafici di utilizzo della capacità seguenti:

* % utilizzo CPU: per impostazione predefinita mostra la media e il 95° percentile 
* Memoria disponibile: impostazione predefinita che mostra la media, il quinto e il decimo percentile 
* % spazio su disco logico utilizzato: per impostazione predefinita mostra la media e il 95° percentile 
* Frequenza byte inviati: per impostazione predefinita mostra il numero medio di byte inviati 
* Frequenza byte ricevuti: per impostazione predefinita mostra il numero medio di byte ricevuti

È anche possibile modificare la granularità dei grafici entro l'intervallo di tempo selezionando **Media**, **Min**, **Max**, **50°**, **90°** e **95°** nel selettore del percentile.

Per visualizzare l'utilizzo delle risorse per singola macchina virtuale in una visualizzazione elenco e vedere quale computer è di tendenza con l'utilizzo più elevato, selezionare la scheda **Elenco N principali.**  La pagina **Top N List** mostra le prime 20 macchine ordinate per le più utilizzate dal 95esimo percentile per la metrica di utilizzo della CPU *%*.  È possibile vedere più macchine selezionando **Carica altro**; i risultati si espandono per mostrare le prime 500 macchine. 

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
2. Nell'elenco scegliere una macchina virtuale e nella sezione **Monitoraggio** scegliere **Insights**.  
3. Selezionare la scheda **Prestazioni**. 

Questa pagina non solo include i grafici di utilizzo delle prestazioni, ma anche una tabella che mostra capacità, utilizzo e media totale di ogni disco logico individuato per ogni misura.  

Vengono forniti i grafici di utilizzo della capacità seguenti:

* % utilizzo CPU: per impostazione predefinita mostra la media e il 95° percentile 
* Memoria disponibile: impostazione predefinita che mostra la media, il quinto e il decimo percentile 
* % spazio su disco logico utilizzato: per impostazione predefinita mostra la media e il 95° percentile 
* Operazioni di I/O al secondo disco logico: per impostazione predefinita mostra la media e il 95° percentile
* MB/sec disco logico: per impostazione predefinita mostra la media e il 95° percentile
* % utilizzo massimo disco logico: per impostazione predefinita mostra la media e il 95° percentile
* Frequenza byte inviati: per impostazione predefinita mostra il numero medio di byte inviati 
* Frequenza byte ricevuti: per impostazione predefinita mostra il numero medio di byte ricevuti

Facendo clic sull'icona a forma di puntina nell'angolo superiore destro di uno qualsiasi dei grafici, il grafico selezionato viene segnaposto all'ultimo dashboard di Azure visualizzato. Dal dashboard, è possibile ridimensionare e riposizionare il grafico. Se si seleziona il grafico dal dashboard, si reindirizza ad Monitoraggio di Azure per le macchine virtuali e viene caricata la visualizzazione dei dettagli delle prestazioni per la macchina virtuale.  

![Vista di Prestazioni di Informazioni dettagliate macchina virtuale direttamente dalla macchina virtuale](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Visualizzare le prestazioni direttamente da un set di scalabilità di macchine virtuali di AzureView performance directly from an Azure virtual machine scale set

Per accedere direttamente da un set di scalabilità di macchine virtuali di Azure, eseguire i passaggi seguenti.

1. Nel portale di Azure selezionare Set di **scalabilità macchina virtuale**.
2. Nell'elenco scegliere una macchina virtuale e nella sezione Monitoraggio scegliere Insights per visualizzare la scheda Prestazioni.From the **list,** choose a VM and in the **Monitoring** section choose **Insights** to view the Performance tab.

Questa pagina carica la visualizzazione delle prestazioni di Monitoraggio di Azure, con ambito al set di scalabilità selezionato. In questo modo è possibile visualizzare le istanze Top N nel set di scalabilità nel set di metriche monitorate, visualizzare le prestazioni aggregate nel set di scalabilità e visualizzare le tendenze per le metriche selezionate nelle singole istanze del set di scalabilità. La selezione di un'istanza dalla visualizzazione elenco consente di caricare la mappa o di spostarsi in una visualizzazione dettagliata delle prestazioni per tale istanza.

Facendo clic sull'icona a forma di puntina nell'angolo superiore destro di uno qualsiasi dei grafici, il grafico selezionato viene segnaposto all'ultimo dashboard di Azure visualizzato. Dal dashboard, è possibile ridimensionare e riposizionare il grafico. Se si seleziona il grafico dal dashboard, si reindirizza ad Monitoraggio di Azure per le macchine virtuali e viene caricata la visualizzazione dei dettagli delle prestazioni per la macchina virtuale.  

![Informazioni sulle macchine virtuali Prestazioni direttamente dalla visualizzazione del set di scalabilità delle macchine virtualiVM insights Performance directly from virtual machine scale set view](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>È inoltre possibile accedere a una visualizzazione dettagliata delle prestazioni per un'istanza specifica dalla visualizzazione Istanze per il set di scalabilità. Passare a **Istanze** nella sezione **Impostazioni** e quindi scegliere **Insights**.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare [le cartelle di lavoro](vminsights-workbooks.md) incluse in Monitoraggio di Azure per le macchine virtuali per analizzare ulteriormente le metriche di prestazioni e di rete.  

- Per altre informazioni sulle dipendenze delle applicazioni individuate, vedere [Visualizzare Azure Monitor per le macchine virtuali Mappare](vminsights-maps.md).
