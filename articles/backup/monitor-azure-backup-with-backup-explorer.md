---
title: Monitorare i backup con Esplora backup
description: Un articolo che descrive come usare Backup Explorer per eseguire il monitoraggio in tempo reale dei backup tra insiemi di credenziali, sottoscrizioni, aree e tenant
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992183"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Monitorare i backup con Esplora backup

Poiché le organizzazioni eseguono il backup di più macchine virtuali nel cloud, è importante disporre di una posizione centralizzata per visualizzare le informazioni operative sui backup in una grande area, per monitorare in modo efficiente i backup.

La cartella di lavoro di backup Explorer è una cartella di lavoro predefinita di monitoraggio di Azure che consente ai clienti di backup di avere un unico riquadro di vetro per l'esecuzione di attività di monitoraggio operative correlate al backup nell'intero patrimonio di backup in Azure (spanning di tenant, posizioni, sottoscrizioni, gruppi di risorse e insiemi di credenziali, da una posizione centrale. In generale, fornisce le funzionalità seguenti:

* **Nel punto di vista della scalabilità** : una visualizzazione aggregata di elementi di backup, processi, avvisi, criteri e risorse non configurati per il backup nell'intero patrimonio di backup. 
* **Analisi di drill-down** : è possibile scegliere di ottenere informazioni dettagliate su ogni entità, ovvero processi, avvisi, criteri ed elementi di backup, da un'unica posizione.
* **Interfacce** di utilità pratica: una volta identificato un problema, è possibile scegliere di eseguire le azioni passando facilmente all'elemento di backup o alla risorsa di Azure.

Le funzionalità sopra elencate sono fornite dall'integrazione nativa con Azure Resource Graph e le cartelle di lavoro di monitoraggio di Azure.

> [!NOTE]
> * Esplora backup è attualmente disponibile solo per i dati delle macchine virtuali di Azure.
> * Esplora backup è destinato a essere un dashboard operativo per la visualizzazione delle informazioni sui backup negli ultimi 7 giorni (massimo).
> * Attualmente, la personalizzazione del modello di Esplora backup non è supportata. Inoltre, attualmente non è consigliabile scrivere automazione personalizzate nei dati del grafo delle risorse di Azure.

## <a name="getting-started"></a>Inizia ora

Per accedere a Esplora backup, passare a uno degli insiemi di credenziali di servizi di ripristino e fare clic sul collegamento **Esplora backup** nella pagina **Panoramica** .

![Collegamento rapido insieme di credenziali](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Facendo clic sul collegamento si apre Esplora backup che fornisce una visualizzazione aggregata in tutti gli insiemi di credenziali e le sottoscrizioni a cui si ha accesso. Se si usa un account Azure Lighthouse, è possibile visualizzare i dati in tutti i tenant a cui si ha accesso. vedere più avanti nella sezione relativa alle viste tra tenant.

![Pagina di destinazione di Esplora risorse](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Casi d'uso di Esplora backup

Esplora backup è costituito da più schede, ciascuna scheda che fornisce informazioni dettagliate su un tipo specifico di artefatto di backup, ad esempio elementi di backup, processi, criteri e così via. In questa sezione viene fornita una breve panoramica di ogni scheda. I video forniscono casi d'uso di esempio per ciascuna scheda, insieme a una descrizione dei vari controlli disponibili per l'utente.

### <a name="summary"></a>Riepilogo

La scheda Riepilogo consente di avere una rapida occhiata alla condizione complessiva della proprietà di backup. È possibile visualizzare informazioni quali il numero di elementi protetti, il numero di elementi per cui non è stata abilitata la protezione, il numero di processi completati nelle ultime 24 ore e così via. 

Ognuna delle altre schede rappresenta un'entità DISTINCT, ad esempio gli elementi di backup, i processi di backup, gli avvisi di backup e i criteri di backup. È inoltre possibile visualizzare informazioni sui computer per i quali non è stato configurato il backup. Se si fa clic su una di queste schede, vengono visualizzate informazioni specifiche di tale entità.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Elementi di backup

È possibile visualizzare ogni elemento di backup filtrato in base alla sottoscrizione, all'insieme di credenziali e ad altri parametri. Facendo clic sul nome di un elemento di backup è possibile aprire il pannello Azure per l'elemento di backup. Ad esempio, dalla tabella, è possibile osservare che l'ultimo backup non è riuscito per l'elemento "X". Facendo clic su "X" si apre il pannello backup per questo elemento, in cui è possibile attivare un'operazione di backup su richiesta.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Processi

È possibile visualizzare i dettagli di tutti i processi attivati negli ultimi sette giorni passando alla scheda processi. Qui è possibile filtrare in base all'operazione del processo, allo stato del processo e al codice di errore, nel caso di processi non riusciti.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Avvisi

È possibile visualizzare i dettagli di tutti gli avvisi generati negli insiemi di credenziali negli ultimi sette giorni, facendo clic sulla scheda avvisi. Qui è possibile filtrare i record in base al tipo di avviso, ad esempio errore di backup, errore di ripristino, lo stato corrente dell'avviso (ad esempio, attivo o risolto) e la gravità dell'avviso (ad esempio, critico, avviso, informazioni). È anche possibile passare alla macchina virtuale di Azure facendo clic sul collegamento alla macchina virtuale e intraprendere l'azione necessaria.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Criteri

Facendo clic sulla scheda criteri è possibile visualizzare le informazioni chiave su tutti i criteri di backup creati nell'area di backup. È possibile visualizzare il numero di elementi associati a ogni criterio, insieme al periodo di mantenimento dati e alla frequenza di backup specificati da ogni criterio.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>Backup non abilitato

È importante che l'amministratore di backup di un'organizzazione possa identificare rapidamente i computer dell'organizzazione per i quali non è ancora stato abilitato il backup, in modo che sia possibile abilitare il backup per tutti i computer che necessitano di protezione. Fare clic sulla scheda **backup non abilitato** consente di eseguire questa attività.

In questa scheda verrà visualizzata una tabella contenente l'elenco di elementi non protetti. Se l'organizzazione segue la pratica di assegnare tag diversi ai computer di produzione e ai computer di test o ai computer che dispongono di funzioni diverse, per ognuna delle quali potrebbe essere necessario un criterio di backup separato, l'applicazione di filtri in base ai tag consente di visualizzare informazioni specifiche di un una determinata classe di computer. Facendo clic sul nome di un elemento viene reindirizzato il pannello **Configura backup** per l'elemento, in cui è possibile scegliere di eseguire il backup di tale elemento con un criterio di backup appropriato.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Esportazione in Excel

Facendo clic sul pulsante freccia giù nella parte superiore destra di qualsiasi widget (tabella/grafico), il contenuto del widget viene esportato come foglio di Excel, così come è con i filtri esistenti applicati. Per esportare più righe di una tabella in Excel, è possibile aumentare il numero di righe visualizzate nella pagina utilizzando l'elenco a discesa **righe per pagina** nella parte superiore di ogni scheda.

## <a name="pinning-to-dashboard"></a>Aggiunta al dashboard

È possibile fare clic sull'icona Aggiungi nella parte superiore di ogni widget per aggiungere tale widget al dashboard portale di Azure. Questo consente di creare dashboard personalizzati personalizzati per visualizzare le informazioni più importanti necessarie.

## <a name="cross-tenant-views"></a>Viste tra tenant

Se si è un utente di Azure Lighthouse con accesso delegato alle sottoscrizioni in più ambienti tenant, è possibile usare il filtro della sottoscrizione predefinito (facendo clic sull'icona del filtro in alto a destra della portale di Azure) per selezionare tutte le sottoscrizioni desiderate vedere i dati per. Questa operazione consentirà a backup Explorer di aggregare le informazioni su tutti gli insiemi di credenziali in tutte le sottoscrizioni. Per altre informazioni su Azure Lighthouse, vedere [qui](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Fasi successive

[Informazioni su come usare monitoraggio di Azure per ottenere informazioni dettagliate sui dati di backup](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)