---
title: Monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)
description: Panoramica della funzionalità di integrità di Monitoraggio di Azure per le macchine virtuali, incluso il modo in cui è possibile visualizzare l'integrità delle macchine virtuali e ricevere avvisi quando una macchina virtuale diventa non integra.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 96bed9f3b04e54e2e9a5234d78f9a2660126742e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687063"
---
# <a name="azure-monitor-for-vms-guest-health-preview"></a>Monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)
Monitoraggio di Azure per le macchine virtuali integrità Guest consente di visualizzare l'integrità delle macchine virtuali in base a un set di misurazioni delle prestazioni campionate a intervalli regolari dal sistema operativo guest. È possibile controllare rapidamente l'integrità di tutte le macchine virtuali in una sottoscrizione o in un gruppo di risorse, eseguire il drill-down sull'integrità dettagliata di una determinata macchina virtuale o ricevere notifiche proattive quando una macchina virtuale diventa non integra. 

## <a name="enable-virtual-machine-health"></a>Abilita integrità macchina virtuale
Per informazioni dettagliate sull'abilitazione della funzionalità di integrità Guest e sull'onboarding delle macchine virtuali, vedere [Enable monitoraggio di Azure per le macchine virtuali Health Guest (anteprima)](vminsights-health-enable.md) .

## <a name="pricing"></a>Prezzi
Non esiste alcun costo diretto per la funzionalità di integrità Guest, ma è previsto un costo per l'inserimento e l'archiviazione dei dati sullo stato di integrità nell'area di lavoro Log Analytics. Tutti i dati vengono archiviati nella tabella *HealthStateChangeEvent* . Per informazioni dettagliate sui modelli di prezzi e sui costi, vedere [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](../platform/manage-cost-storage.md) .

## <a name="view-virtual-machine-health"></a>Visualizza integrità macchina virtuale
La colonna **stato VM guest** nella pagina attività **iniziali** consente di visualizzare rapidamente l'integrità di ogni macchina virtuale in una sottoscrizione o in un gruppo di risorse specifico. Lo stato di integrità corrente di ogni macchina virtuale viene visualizzato mentre le icone di ogni gruppo indicano il numero di macchine virtuali attualmente in ogni stato del gruppo.

[![Pagina iniziale con integrità VM Guest](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Monitoraggi
Fare clic sullo stato di integrità di una macchina virtuale per visualizzare lo stato dettagliato per ognuno dei monitoraggi. Ogni monitoraggio misura lo stato di un particolare componente. L'integrità complessiva della macchina virtuale è determinata dall'integrità dei singoli monitoraggi. 

![Esempio di monitoraggi](media/vminsights-health-overview/monitors.png)

Nella tabella seguente sono elencati i monitoraggi aggregati e di unità attualmente disponibili per ogni macchina virtuale. 

| Monitoraggio | Tipo | Descrizione |
|:---|:---|:---|
| Uso della CPU | Unità | Percentuale di utilizzo del processore. |
| File system | Aggregate | Stato aggregato di tutti i file System nella VM Linux. |
| File system  | Aggregate | Integrità di ogni singola file system nella VM Linux. Il nome del monitoraggio è il nome della file system. |
| Spazio libero | Unità | Percentuale di spazio disponibile nel file system. |
| Dischi logici | Aggregate | Stato aggregato di tutti i dischi logici nella macchina virtuale Windows. |
| Disco logico  | Aggregate | Integrità di ogni singolo disco logico nella macchina virtuale Windows. Il nome del monitoraggio è il nome del disco. |
| Memoria | Aggregate | Stato aggregato della memoria nella macchina virtuale. |
| Memoria disponibile | Unità | Megabyte disponibili nella macchina virtuale. |

## <a name="health-states"></a>Stati di integrità
Ogni monitoraggio esegue il campionamento dei valori nell'agente ogni minuto e confronta i valori campionati con i criteri per ogni stato di integrità. Se i criteri per lo stato specifico sono true, il monitoraggio è impostato su tale stato. Se nessuno dei criteri è true, il monitoraggio è impostato su uno stato integro. I dati vengono inviati dall'agente a monitoraggio di Azure ogni tre minuti o immediatamente in caso di modifica dello stato.

Ogni monitoraggio ha una finestra lookback e analizza tutti i campioni raccolti in tale intervallo di tempo. Ad esempio, un monitoraggio può avere una finestra lookback di 240 secondi per cercare il valore massimo tra almeno 2 valori campionati, ma non più degli ultimi 3. Mentre i valori vengono campionati a intervalli regolari, il numero campionato in una particolare finestra può variare leggermente se si verificano problemi nell'operazione dell'agente.

I monitoraggi presentano tutti i possibili stati di integrità nella tabella seguente e saranno in uno solo in un determinato momento. Quando un monitoraggio viene inizializzato, viene avviato in uno stato integro.

| Stato di integrità | Descrizione |
|:---|:---|
| Healthy  | Il monitoraggio non supera attualmente la soglia di avviso o critica. |
| Avviso  | Il monitoraggio ha superato la soglia di avviso (se definito). |
| Critico | Il monitoraggio ha superato la soglia critica (se definito). |
| Sconosciuto  | Non sono stati raccolti dati sufficienti per determinare lo stato di integrità. |
| Disabled | Il monitoraggio è attualmente disabilitato. |
| Nessuno     | Il monitoraggio è appena avviato e non è ancora stato valutato o l'oggetto monitorato non esiste più. |



Esistono due tipi di monitoraggi, come illustrato nella tabella seguente.

| Monitoraggio | Descrizione |
|:---|:---|
| Monitoraggio unità | Misura alcuni aspetti di una risorsa o di un'applicazione. Questo potrebbe controllare un contatore delle prestazioni per determinare le prestazioni della risorsa o la relativa disponibilità. |
| Monitoraggio aggregato | Raggruppa più monitoraggi per fornire un singolo stato di integrità aggregato. Un monitoraggio aggregato può contenere uno o più monitoraggi unità e altri monitoraggi aggregati. |


  
### <a name="health-rollup-policy"></a>Criteri di rollup stato
Lo stato di integrità di una macchina virtuale è determinato dal rollup dello stato da ogni unità e monitoraggi aggregati. Ogni monitoraggio aggregato utilizza un criterio di rollup dello stato peggiore in cui lo stato del monitoraggio aggregato corrisponde allo stato del monitoraggio figlio con lo stato di integrità peggiore.  

Nell'esempio seguente il monitoraggio **dello spazio libero** si trova in uno stato critico, ovvero i ruoli fino alle aggregazioni per la relativa istanza e poi nei **file System**. Anche se l' **utilizzo della CPU** si trova in uno stato di avviso, la macchina virtuale viene impostata su critico perché si tratta dello stato peggiore sottostante. Se lo spazio disponibile era quello di tornare a uno stato integro, la macchina virtuale passa a uno stato di avviso poiché l'utilizzo della CPU diventa il monitoraggio con lo stato peggiore.

![Esempio di rollup dell'integrità](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>Dettagli del monitoraggio
Selezionare un monitoraggio per visualizzarne i dettagli, che include le schede seguenti.

**Cenni preliminari** fornisce una descrizione del monitoraggio, l'ultima volta che è stata valutata e i valori campionati per determinare lo stato di integrità corrente. Il numero di campioni può variare in base alla definizione del monitoraggio e alla volatilità dei valori.

[![Panoramica dei dettagli del monitoraggio](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**Cronologia** elenca la cronologia delle modifiche di stato per il monitoraggio. È possibile espandere qualsiasi modifica di stato per visualizzare i valori valutati per determinare lo stato di integrità. Fare clic su **Visualizza configurazione applicata** per visualizzare la configurazione del monitoraggio nel momento in cui lo stato di integrità è stato modificato.



[![Cronologia dei dettagli del monitoraggio](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Configurazione
Visualizzare e modificare la configurazione del monitoraggio per la macchina virtuale selezionata. Per informazioni dettagliate, vedere [configurare il monitoraggio in monitoraggio di Azure per le macchine virtuali integrità Guest (anteprima)](vminsights-health-enable.md) .

[![Configurazione dei dettagli del monitoraggio](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Passaggi successivi

- [Abilitare l'integrità Guest in Monitoraggio di Azure per le macchine virtuali e caricare gli agenti.](vminsights-health-enable.md)
- [Configurare i monitoraggi utilizzando il portale di Azure.](vminsights-health-configure.md)
- [Configurare i monitoraggi con le regole di raccolta dati.](vminsights-health-configure-dcr.md)