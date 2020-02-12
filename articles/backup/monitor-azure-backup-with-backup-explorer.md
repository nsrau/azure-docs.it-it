---
title: Monitorare i backup con Esplora backup
description: Questo articolo descrive come usare Esplora backup per eseguire il monitoraggio in tempo reale dei backup tra insiemi di credenziali, sottoscrizioni, aree e tenant.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: b65f68e33b53dff341ee72f6b9e9f42e344c49b1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149576"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorare i backup con Esplora backup

Poiché le organizzazioni eseguono il backup di più macchine virtuali nel cloud, diventa sempre più importante monitorare i backup in modo efficiente. Il modo migliore per iniziare consiste nell'usare una posizione centralizzata per la visualizzazione delle informazioni operative in una grande azienda.

Esplora backup è una cartella di lavoro predefinita di monitoraggio di Azure che offre ai clienti di backup di Azure questa singola posizione centralizzata. Esplora backup consente di monitorare le attività operative nell'intero patrimonio di backup in Azure, con spanning di tenant, località, sottoscrizioni, gruppi di risorse e insiemi di credenziali. In generale, in Esplora backup sono disponibili le funzionalità seguenti:

* **Prospettiva su larga scala**: ottenere una visualizzazione aggregata di elementi di backup, processi, avvisi, criteri e risorse che non sono ancora configurati per il backup nell'intera proprietà. 
* **Analisi di drill-down**: Visualizza informazioni dettagliate su ogni processo, avviso, criterio ed elementi di backup in un'unica posizione.
* **Interfacce**utilizzabili: dopo l'identificazione di un problema, è possibile risolverlo facilmente nell'elemento di backup pertinente o nella risorsa di Azure.

Queste funzionalità sono fornite dall'integrazione nativa con il grafico delle risorse di Azure e le cartelle di lavoro di monitoraggio di Azure.

> [!NOTE]
> * Esplora backup è attualmente disponibile solo per i dati delle macchine virtuali (VM) di Azure.
> * Backup Explorer deve essere un dashboard operativo per la visualizzazione delle informazioni sui backup negli ultimi 7 giorni (massimo).
> * Attualmente, la personalizzazione del modello di Esplora backup non è supportata. 
> * Non è consigliabile scrivere automazione personalizzate sui dati del grafico delle risorse di Azure.

## <a name="get-started"></a>Attività iniziali

Per accedere a backup Explorer, passare a uno degli insiemi di credenziali di servizi di ripristino e selezionare il collegamento **Esplora backup** nel riquadro **Panoramica** .

![Collegamento rapido insieme di credenziali](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Selezionando il collegamento si apre backup Explorer, che fornisce una visualizzazione aggregata in tutti gli insiemi di credenziali e le sottoscrizioni a cui si ha accesso. Se si usa un account Azure Lighthouse, è possibile visualizzare i dati in tutti i tenant a cui si ha accesso. Per altre informazioni, vedere la sezione "viste tra tenant" alla fine di questo articolo.

![Pagina di destinazione di Esplora backup](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casi d'uso di Esplora backup

In Esplora backup vengono visualizzate più schede, ognuna delle quali fornisce informazioni dettagliate su un elemento di backup specifico, ad esempio un elemento di backup, un processo o un criterio. In questa sezione viene fornita una breve panoramica di ogni scheda. I video forniscono casi d'uso di esempio per ogni artefatto di backup, oltre a descrizioni dei controlli disponibili.

### <a name="the-summary-tab"></a>Scheda Riepilogo

La scheda **Summary (riepilogo** ) fornisce una rapida panoramica della condizione complessiva della proprietà backup. Ad esempio, è possibile visualizzare il numero di elementi protetti, il numero di elementi per cui non è stata abilitata la protezione o il numero di processi completati nelle ultime 24 ore.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Scheda elementi di backup

È possibile filtrare e visualizzare ciascuno degli elementi di backup in base alla sottoscrizione, all'insieme di credenziali e ad altre caratteristiche. Selezionando il nome di un elemento di backup, è possibile aprire il riquadro Azure per l'elemento. Ad esempio, dalla tabella, è possibile osservare che l'ultimo backup non è riuscito per l'elemento *X*. Selezionando *X*, è possibile aprire il riquadro di **backup** dell'elemento, in cui è possibile attivare un'operazione di backup su richiesta.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Scheda processi

Selezionare la scheda **processi** per visualizzare i dettagli di tutti i processi attivati negli ultimi 7 giorni. Qui è possibile filtrare in base all' *operazione del processo*, *allo stato del processo*e al codice di *errore* (per i processi non riusciti).


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Scheda avvisi

Selezionare la scheda **avvisi** per visualizzare i dettagli di tutti gli avvisi generati negli insiemi di credenziali negli ultimi 7 giorni. È possibile filtrare gli avvisi per tipo *(errore di backup* o *ripristino*), stato corrente (*attivo* o *risolto*) e gravità (*critico*, *avviso*o *informazioni*). È anche possibile selezionare un collegamento per passare alla macchina virtuale di Azure ed eseguire le azioni necessarie.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Scheda criteri

È possibile selezionare la scheda **criteri** per visualizzare le informazioni chiave su tutti i criteri di backup creati nell'area di backup. È possibile visualizzare il numero di elementi associati a ogni criterio, insieme al periodo di mantenimento dati e alla frequenza di backup specificati dal criterio.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Scheda backup non abilitato

Il backup deve essere abilitato per tutti i computer che richiedono la protezione. Con Esplora backup, gli amministratori di backup possono identificare rapidamente quali computer di un'organizzazione non sono ancora protetti dal backup. Per visualizzare le informazioni, selezionare la scheda **backup non abilitato** .

Il riquadro **backup non abilitato** Visualizza una tabella con un elenco di computer non protetti. L'organizzazione potrebbe assegnare tag diversi ai computer di produzione e ai computer di test o ai computer che svolgono una serie di funzioni. Poiché ogni classe di computer necessita di un criterio di backup separato, l'applicazione di filtri in base ai tag consente di visualizzare informazioni specifiche. Se si seleziona il nome di un computer, viene reindirizzato al riquadro **Configura backup** di tale computer, in cui è possibile scegliere di applicare un criterio di backup appropriato.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Esportare in Excel

È possibile esportare il contenuto di qualsiasi tabella o grafico come foglio di calcolo di Excel. Il contenuto viene esportato così com'è, con i filtri esistenti applicati. Per esportare righe di tabella aggiuntive, è possibile aumentare il numero di righe da visualizzare nella pagina utilizzando l'elenco a discesa **righe per pagina** nella parte superiore di ogni scheda.

## <a name="pin-to-the-dashboard"></a>Aggiungi al dashboard

È possibile selezionare l'icona "Aggiungi" nella parte superiore di ogni tabella o grafico per aggiungerla al dashboard portale di Azure. L'aggiunta di queste informazioni consente di creare un dashboard personalizzato personalizzato per visualizzare le informazioni più importanti.

## <a name="cross-tenant-views"></a>Viste tra tenant

Se si è un utente di Azure Lighthouse con accesso delegato alle sottoscrizioni in più ambienti tenant, è possibile usare il filtro della sottoscrizione predefinito. Per visualizzare le sottoscrizioni per cui si desidera visualizzare i dati, selezionare l'icona "filtro" nella parte superiore destra della portale di Azure. Quando si usa questa funzionalità, Esplora backup aggrega le informazioni relative a tutti gli insiemi di credenziali nelle sottoscrizioni selezionate. Per altre informazioni, vedere [che cos'è Azure Lighthouse?](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come usare monitoraggio di Azure per ottenere informazioni dettagliate sui dati di backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
