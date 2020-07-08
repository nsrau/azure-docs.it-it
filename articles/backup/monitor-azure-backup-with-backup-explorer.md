---
title: Monitorare i backup con Explorer di Backup
description: Questo articolo descrive come usare Explorer di Backup per eseguire il monitoraggio in tempo reale dei backup in insiemi di credenziali, sottoscrizioni, aree e tenant.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e891ee1ccfbe929aaa8ac35518b40f5514da714f
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715206"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorare i backup con Explorer di Backup

Poiché le organizzazioni eseguono il backup di un numero sempre più elevato di macchine virtuali nel cloud, diventa molto importante monitorare i backup in modo efficiente. Per iniziare è consigliabile usare una posizione centralizzata per visualizzare le informazioni operative in una struttura di grandi dimensioni.

Explorer di Backup è una cartella di lavoro predefinita di Monitoraggio di Azure che i clienti di Backup di Azure possono usare come unica posizione centralizzata. Explorer di Backup consente di monitorare le attività operative nell'intera struttura di backup in Azure, che include tenant, percorsi, sottoscrizioni, gruppi di risorse e insiemi di credenziali. In generale Explorer di Backup offre le funzionalità seguenti:

* **Prospettiva su vasta scala**: consente di avere una visualizzazione aggregata di elementi di backup, processi, avvisi, criteri e risorse che non sono ancora configurati per il backup nell'intera struttura.
* **Analisi di drill-down**: visualizza informazioni dettagliate su ogni processo, avviso, criterio ed elemento di backup, tutto in un'unica posizione.
* **Interfacce interattive**: dopo aver identificato un problema, è possibile risolverlo passando semplicemente all'elemento di backup pertinente o alla risorsa di Azure.

Si tratta di funzionalità predefinite nell'integrazione nativa con le cartelle di lavoro di Azure Resource Graph e Monitoraggio di Azure.

> [!NOTE]
>
> * Explorer di Backup è attualmente disponibile solo per i dati delle macchine virtuali di Azure.
> * Explorer di Backup è concepito per essere un dashboard operativo per visualizzare le informazioni sui backup negli ultimi 7 giorni (tempo massimo).
> * Explorer di Backup non è attualmente supportato nei cloud nazionali.
> * La personalizzazione del modello di Explorer di Backup non è attualmente supportata.
> * Non è consigliabile scrivere automazioni personalizzate nei dati di Azure Resource Graph.
> * Attualmente Explorer di Backup consente di monitorare i backup in un massimo di 1000 sottoscrizioni (nei tenant).

## <a name="get-started"></a>Introduzione

Per accedere a Explorer di Backup, passare a uno degli insiemi di credenziali di Servizi di ripristino e selezionare il collegamento **Esplora backup** nel riquadro **Panoramica**.

![Collegamento rapido dall'insieme di credenziali](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Dopo aver selezionato il collegamento, si aprirà Explorer di Backup che offre una visualizzazione aggregata su tutti gli insiemi di credenziali e le sottoscrizioni a cui si ha accesso. Se si usa un account Azure Lighthouse, è possibile visualizzare i dati in tutti i tenant a cui si ha accesso. Per altre informazioni, vedere la sezione "Visualizzazioni tra tenant" alla fine di questo articolo.

![Pagina di destinazione di Explorer di Backup](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casi d'uso di Explorer di Backup

In Explorer di Backup vengono visualizzate più schede, ognuna delle quali contiene informazioni dettagliate su un artefatto di backup specifico, ad esempio un elemento di backup, un processo o un criterio. La sezione seguente offre una breve panoramica di ogni scheda. I video illustrano casi d'uso di esempio per ogni artefatto di backup, oltre a descrizioni dei controlli disponibili.

### <a name="the-summary-tab"></a>Scheda Riepilogo

La scheda **Riepilogo** offre una rapida panoramica della condizione complessiva della struttura di backup. È ad esempio possibile visualizzare il numero di elementi protetti, il numero di elementi per cui non è stata abilitata la protezione o il numero di processi completati nelle ultime 24 ore.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Scheda Elementi di backup

È possibile filtrare e visualizzare tutti gli elementi di backup in base alla sottoscrizione, all'insieme di credenziali e ad altre caratteristiche. Selezionare il nome di un elemento di backup per aprire il riquadro di Azure corrispondente a tale elemento. Dalla tabella è ad esempio possibile osservare che l'ultimo backup non è riuscito per l'elemento *X*. Selezionando *X*, è possibile aprire il riquadro **Backup** dell'elemento e attivare un'operazione di backup su richiesta.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Scheda Processi

Selezionare la scheda **Processi** per visualizzare i dettagli di tutti i processi attivati negli ultimi 7 giorni. Qui è possibile filtrare in base a *Operazioni processo*, *Stato processo* e *Codice errore* (per i processi non riusciti).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Scheda Avvisi

Selezionare la scheda **Avvisi** per visualizzare i dettagli di tutti gli avvisi generati negli insiemi di credenziali negli ultimi 7 giorni. È possibile filtrare gli avvisi in base al tipo (*Errore di backup* o *Errore di ripristino*, allo stato corrente (*Attivo* o *Risolto*) e alla gravità (*Critica*, *Avvertenza* o *Informazioni*. È anche possibile selezionare un collegamento per passare alla macchina virtuale di Azure ed eseguire le azioni necessarie.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Scheda Criteri

È possibile selezionare la scheda **Criteri** per visualizzare le informazioni principali su tutti i criteri di backup creati per la struttura di backup. È possibile visualizzare il numero di elementi associati a ogni criterio, il periodo di conservazione e la frequenza di backup specificati dal criterio.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Scheda Backup Not Enabled (Backup non abilitato)

Il backup deve essere abilitato per tutti i computer che devono essere protetti. Con Explorer di Backup, gli amministratori di backup possono identificare rapidamente quali computer di un'organizzazione non sono ancora protetti dal backup. Per visualizzare queste informazioni, selezionare la scheda **Backup Not Enabled** (Backup non abilitato).

Il riquadro **Backup Not Enabled** (Backup non abilitato) visualizza una tabella con un elenco di computer non protetti. L'organizzazione può assegnare tag diversi ai computer di produzione e ai computer di test o ai computer che svolgono funzioni diverse. Poiché ogni classe di computer richiede un criterio di backup distinto, l'applicazione di filtri in base ai tag consente di visualizzare informazioni specifiche su ogni computer. Se si seleziona il nome di un computer, si viene reindirizzati al riquadro **Configura backup** del computer, in cui è possibile scegliere di applicare un criterio di backup appropriato.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Eseguire l'esportazione in Excel

È possibile esportare i contenuti di qualsiasi tabella o grafico come foglio di calcolo di Excel. I contenuti vengono esportati così come sono, con i filtri esistenti applicati. Per esportare righe di tabella aggiuntive, è possibile aumentare il numero di righe da visualizzare nella pagina usando l'elenco a discesa **Righe per pagina** nella parte superiore di ogni scheda.

## <a name="pin-to-the-dashboard"></a>Aggiungere al dashboard

È possibile selezionare l'icona a forma di puntina nella parte superiore di ogni tabella o grafico per aggiungere un'informazione al dashboard del portale di Azure. L'aggiunta di queste informazioni consente di creare un dashboard personalizzato in cui vengono visualizzate le informazioni più importanti.

## <a name="cross-tenant-views"></a>Visualizzazioni tra tenant

Gli utenti di Azure Lighthouse con accesso delegato alle sottoscrizioni in più ambienti tenant possono usare il filtro della sottoscrizione predefinito. Per visualizzare le sottoscrizioni di cui si vogliono vedere i dati, selezionare l'icona "Filtro" nella parte superiore destra del portale di Azure. Quando si usa questa funzionalità, Explorer di Backup aggrega le informazioni relative a tutti gli insiemi di credenziali nelle sottoscrizioni selezionate. Per altre informazioni, vedere [Informazioni su Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come usare Monitoraggio di Azure per ottenere informazioni dettagliate sui dati di backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
