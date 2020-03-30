---
title: Monitorare i backup con Esplora backup
description: In questo articolo viene descritto come utilizzare Esplora backup per eseguire il monitoraggio in tempo reale dei backup tra insiemi di credenziali, sottoscrizioni, aree e tenant.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131800"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorare i backup con Esplora backup

Man mano che le organizzazioni esegue il backup di un numero sempre maggiore di computer nel cloud, diventa sempre più importante monitorare questi backup in modo efficiente. Il modo migliore per iniziare è quello di utilizzare una posizione centrale per la visualizzazione delle informazioni operative in una grande tenuta.

Esplora backup è una cartella di lavoro predefinita di Monitoraggio di Azure che offre ai clienti di Backup di Azure questa singola posizione centrale. Esplora backup consente di monitorare le attività operative nell'intera proprietà Backup in Azure, che comprende tenant, posizioni, sottoscrizioni, gruppi di risorse e insiemi di credenziali. In generale, Esplora backup offre le seguenti funzionalità:

* **Prospettiva a livello di scalabilità:** consente di ottenere una visualizzazione aggregata degli elementi di backup, dei processi, degli avvisi, dei criteri e delle risorse non ancora configurati per il backup nell'intera proprietà. 
* **Analisi drill-down:** consente di visualizzare informazioni dettagliate su ogni processo, avviso, criteri ed elementi di backup, in un'unica posizione.
* **Interfacce utilizzabili:** dopo aver identificato un problema, è possibile risolverlo passando senza problemi all'elemento di backup o alla risorsa di Azure pertinente.

Queste funzionalità vengono fornite out-of-box dall'integrazione nativa con le cartelle di lavoro di Azure Resource Graph e Azure Monitor.These capabilities are provided out-of-box by native integration with Azure Resource Graph and Azure Monitor workbooks.

> [!NOTE]
> * Esplora backup è attualmente disponibile solo per i dati delle macchine virtuali di Azure.Backup Explorer is currently available only for Azure virtual machines (VMs) data.
> * Esplora backup è progettato per essere un dashboard operativo per la visualizzazione delle informazioni sui backup negli ultimi 7 giorni (massimo).
> * Esplora backup non è attualmente supportato nei cloud nazionali.
> * Attualmente, la personalizzazione del modello Esplora backup non è supportata. 
> * Non è consigliabile scrivere automazioni personalizzate nei dati di Azure Resource Graph.We do not recommend writing custom automations on Azure Resource Graph data.

## <a name="get-started"></a>Introduzione

È possibile accedere a Esplora backup accedendo a qualsiasi insieme di credenziali di Servizi di ripristino e selezionando il collegamento **Esplora backup** nel riquadro **Panoramica.**

![Collegamento rapido Vault](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Selezionando il collegamento si apre Esplora backup, che offre una vista aggregata in tutti gli insiemi di credenziali e le sottoscrizioni a cui si ha accesso. Se si usa un account Lighthouse di Azure, è possibile visualizzare i dati in tutti i tenant a cui si ha accesso. Per ulteriori informazioni, vedere la sezione "Visualizzazioni tra tenant" alla fine di questo articolo.

![Pagina di destinazione di Esplora backup](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casi d'uso di Esplora backup

In Esplora backup vengono visualizzate più schede, ognuna delle quali fornisce informazioni dettagliate su un elemento di backup specifico, ad esempio un elemento di backup, un processo o un criterio. In questa sezione viene fornita una breve panoramica di ciascuna delle schede. I video forniscono esempi di esempi di casi d'uso per ogni elemento di backup, insieme alle descrizioni dei controlli disponibili.

### <a name="the-summary-tab"></a>Scheda Riepilogo

La scheda **Riepilogo** fornisce una rapida occhiata alle condizioni generali della proprietà di backup. Ad esempio, è possibile visualizzare il numero di elementi protetti, il numero di elementi per i quali la protezione non è stata abilitata o il numero di processi riusciti nelle ultime 24 ore.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>Scheda Elementi di backup

È possibile filtrare e visualizzare ciascuno degli elementi di backup in base alla sottoscrizione, all'insieme di credenziali e ad altre caratteristiche. Selezionando il nome di un elemento di backup, è possibile aprire il riquadro Di Azure per tale elemento. Ad esempio, dalla tabella, è possibile osservare che l'ultimo backup non è riuscito per l'elemento *X*. Selezionando *X*, è possibile aprire il riquadro **Backup** dell'elemento, in cui è possibile attivare un'operazione di backup su richiesta.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>Scheda Processi

Selezionare la scheda **Processi** per visualizzare i dettagli di tutti i processi attivati negli ultimi 7 giorni. Qui è possibile filtrare in base *a Operazione processo,* *Stato processo*e Codice *di errore* (per i processi non riusciti).


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>Scheda Avvisi

Selezionare la scheda **Avvisi** per visualizzare i dettagli di tutti gli avvisi generati nei vault negli ultimi 7 giorni. È possibile filtrare gli avvisi in base al tipo (*Errore di backup* o Ripristina non *riusciti*), allo stato corrente (*Attivo* o *Risolto*) e alla gravità (*Critico*, *Avviso*o *Informazioni*). È anche possibile selezionare un collegamento per passare alla macchina virtuale di Azure ed eseguire le azioni necessarie.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>Scheda Criteri

È possibile selezionare la scheda **Criteri** per visualizzare le informazioni chiave su tutti i criteri di backup creati nel patrimonio di backup. È possibile visualizzare il numero di elementi associati a ogni criterio, insieme all'intervallo di conservazione e alla frequenza di backup specificati dal criterio.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>Scheda Backup non abilitato

Il backup deve essere abilitato per tutti i computer che richiedono protezione. Con Esplora backup, gli amministratori di backup possono identificare rapidamente quali computer in un'organizzazione non sono ancora protetti dal backup. Per visualizzare queste informazioni, selezionare la scheda **Backup non abilitato.**

Nel riquadro **Backup non abilitato** viene visualizzata una tabella con un elenco di computer non protetti. L'organizzazione potrebbe assegnare tag diversi ai computer di produzione e ai computer di test o ai computer che svolgono una varietà di funzioni. Poiché ogni classe di computer necessita di criteri di backup separati, il filtro in base ai tag consente di visualizzare informazioni specifiche per ognuno di essi. Selezionando il nome di qualsiasi computer si reindirizza al riquadro **Configura backup** del computer, in cui è possibile scegliere di applicare un criterio di backup appropriato.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Eseguire l'esportazione in Excel

È possibile esportare il contenuto di qualsiasi tabella o grafico come foglio di calcolo di Excel. I contenuti vengono esportati così come sono, con i filtri esistenti applicati. Per esportare altre righe di tabella, è possibile aumentare il numero di righe da visualizzare nella pagina utilizzando l'elenco a discesa **Righe per pagina** nella parte superiore di ogni scheda.

## <a name="pin-to-the-dashboard"></a>Aggiungere al dashboard

È possibile selezionare l'icona "aggiungi" nella parte superiore di ogni tabella o grafico per aggiungerla al dashboard del portale di Azure.You can select the "pin" icon in top of each table or chart to pin it to your Azure portal dashboard. L'aggiunta di queste informazioni consente di creare un dashboard personalizzato personalizzato per visualizzare le informazioni più importanti per l'utente.

## <a name="cross-tenant-views"></a>Visualizzazioni tra tenant

Se si è un utente di Azure Lighthouse con accesso delegato alle sottoscrizioni in più ambienti tenant, è possibile usare il filtro di sottoscrizione predefinito. Per visualizzare le sottoscrizioni per cui si vogliono visualizzare i dati, selezionare l'icona "filtro" nella parte superiore destra del portale di Azure. Quando si utilizza questa funzionalità, Esplora backup aggrega le informazioni su tutti gli insiemi di credenziali tra le sottoscrizioni selezionate. Per altre informazioni, vedere [Che cos'è Azure Lighthouse?](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come usare Monitoraggio di Azure per ottenere informazioni dettagliate sui dati di backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
