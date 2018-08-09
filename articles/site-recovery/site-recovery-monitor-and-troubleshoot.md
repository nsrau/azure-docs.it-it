---
title: Monitorare Azure Site Recovery | Microsoft Docs
description: Monitorare e risolvere i problemi relativi alle repliche e alle operazioni di Azure Site Recovery usando il portale
services: site-recovery
author: bsiva
manager: abhemra
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 07/19/2018
ms.author: bsiva
ms.openlocfilehash: 96f4ecb433277c747ddb1d1dc977bd609f93d5ff
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578586"
---
# <a name="monitor-and-troubleshoot-site-recovery"></a>Monitorare e risolvere i problemi di Azure Site Recovery

In questo articolo viene illustrato come usare le funzionalità di monitoraggio predefinite di Azure Site Recovery per il monitoraggio e la risoluzione dei problemi. 

## <a name="use-the-dashboard"></a>Usare il dashboard

1. Nell'insieme di credenziali fare clic su **Panoramica** per aprire il dashboard di Site Recovery. Sono disponibili pagine del dashboard sia per Site Recovery che per Backup ed è possibile da una all'altra.

    ![Dashboard di Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  Il dashboard consolida tutte le informazioni di monitoraggio per l'insieme di credenziali in una singola posizione. Dal dashboard è possibile eseguire il drill-down in diverse aree. 

    ![Dashboard di Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. In **Elementi replicati** fare clic su **Visualizza tutto** per visualizzare tutti i server nell'insieme di credenziali.
4. Eseguire il drill-down facendo clic sui dettagli sullo stato in ogni sezione. In **Visualizzazione dell'infrastruttura** è possibile ordinare le informazioni di monitoraggio in base al tipo di macchine di cui è necessario eseguire la replica.

## <a name="monitor-replicated-items"></a>Monitorare gli elementi replicati

La sezione Elementi replicati mostra l'integrità di tutte le macchine con la replica abilitata nell'insieme di credenziali.

**State** | **Dettagli**
--- | ---
Healthy | La replica sta procedendo normalmente. Non sono stati rilevati sintomi di errore o avviso.
Avviso | Sono stati rilevati uno o più sintomi di avviso che potrebbero influire sulla replica.
Critico | Sono stati rilevati uno o più sintomi di errore di replica critici.<br/><br/> Questi sintomi di errore in genere sono indicatori del fatto che la replica è bloccata o non sta procedendo con la stessa frequenza di modifica dei dati.
Non applicabile | Server di cui attualmente non è prevista la replica. Potrebbe includere le macchine di cui è stato eseguito il failover.

## <a name="monitor-test-failovers"></a>Monitorare i failover di test

È possibile visualizzare lo stato dei failover di test per le macchine nell'insieme di credenziali.

- È consigliabile eseguire un failover di test nelle macchine replicate almeno una volta ogni sei mesi. È un modo per verificare che il failover funzioni come previsto senza interruzioni per l'ambiente di produzione. 
- Un failover di test è considerato riuscito solo una volta che il failover e la pulizia dopo il failover sono stati completati correttamente.

**State** | **Dettagli**
--- | ---
Test consigliato | Macchine in cui non è stato eseguito un failover di test dal momento in cui è stata abilitata la protezione.
Eseguito correttamente | Macchine con uno o più failover di test riusciti.
Non applicabile | Macchine attualmente non idonee per un failover di test. Ad esempio, macchine di cui è stato eseguito il failover o con replica iniziale/failover di test/failover in corso.

## <a name="monitor-configuration-issues"></a>Monitorare i problemi di configurazione

La sezione **Problemi di configurazione** visualizza un elenco di problemi che potrebbero compromettere la possibilità di effettuare correttamente il failover.

- I problemi di configurazione (ad eccezione della disponibilità di aggiornamenti software) vengono rilevati da un'operazione periodica del validator eseguita ogni 12 ore per impostazione predefinita. Per eseguire immediatamente l'operazione del validator, fare clic sull'icona Aggiorna accanto all'intestazione della sezione **Problemi di configurazione**.
- Fare clic sui collegamenti per ottenere altri dettagli. Per problemi che hanno effetto su computer specifici, fare clic su **richiede attenzione** nella colonna **Configurazioni di destinazione**. I dettagli includono raccomandazioni sulla risoluzione dei problemi.

**State** | **Dettagli**
--- | ---
Configurazioni mancanti | Un'impostazione necessaria non è presente, ad esempio una rete di ripristino o un gruppo di risorse.
Risorse mancanti | Una risorsa specificata non è stata trovata o non è disponibile nella sottoscrizione. Ad esempio, la risorsa è stata eliminata o ne è stata eseguita la migrazione. Le risorse monitorate includono gruppo di risorse di destinazione, rete virtuale e subnet di destinazione, account di archiviazione di log/destinazione, set di disponibilità di destinazione, indirizzo IP di destinazione.
Quota sottoscrizione |  Il totale delle quote di risorse della sottoscrizione disponibili viene confrontato con il totale necessario per effettuare il failover delle macchine nell'insieme di credenziali.<br/><br/> Se le risorse sono insufficienti, viene segnalato che il totale delle quote non è sufficiente.<br/><br/> Le quote monitorano il conteggio dei core delle macchine virtuali, il conteggio dei core delle famiglie di macchine virtuali e il conteggio delle schede di interfaccia di rete (NIC).
Aggiornamenti software | Disponibilità di nuovi aggiornamenti software e informazioni sulle versioni software in scadenza.


## <a name="monitoring-errors"></a>Errori di monitoraggio 
La sezione **Riepilogo errori** visualizza i sintomi di errore attualmente attivi che potrebbero influire sulla replica dei server nell'insieme di credenziali e il numero di macchine interessate.

- All'inizio della sezione, vengono visualizzati gli errori che riguardano i componenti dell'infrastruttura locale. Ad esempio, il mancato recapito di un heartbeat dal provider di Azure Site Recovery in esecuzione nel server di configurazione locale, nel server VMM o nell'host Hyper-V.
- Vengono quindi elencati i sintomi di errore di replica che hanno effetto sui server replicati.
- Le voci della tabella sono elencate in ordine decrescente in base alla gravità dell'errore e quindi in ordine decrescente in base al conteggio delle macchine interessate.
- Il conteggio dei server interessati è utile per capire se un singolo problema sottostante può influire su più macchine. Ad esempio, un problema di rete può avere conseguenze su tutte le macchine che eseguono la replica in Azure. 
- Possono verificarsi più errori di replica in un singolo server. In questo caso, il server viene conteggiato nell'elenco dei server interessati per ogni sintomo di errore. Dopo aver risolto il problema, i parametri di replica migliorano e l'errore viene cancellato dalla macchina.

## <a name="monitor-the-infrastructure"></a>Monitorare l'infrastruttura.

In **Visualizzazione dell'infrastruttura** vengono mostrati i componenti dell'infrastruttura coinvolti nella replica e l'integrità della connettività tra i server e i servizi di Azure.

- Una linea verde indica che la connessione è integra.
- Una linea rossa con un'icona di errore sovrapposta indica la presenza di uno o più sintomi di errore che influiscono sulla connettività.
-  Passare il puntatore del mouse sopra l'icona di errore per visualizzare l'errore e il numero di entità interessate. Fare clic sull'icona per visualizzare un elenco filtrato delle entità interessate.

    ![Visualizzazione dell'infrastruttura di Site Recovery (insieme di credenziali)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Suggerimenti per il monitoraggio dell'infrastruttura

- Assicurarsi che i componenti dell'infrastruttura locale (server di configurazione, server di elaborazione, server VMM, host Hyper-V, macchine VMware) eseguano le versioni più recenti del provider di Site Recovery e/o degli agenti.
- Per usare tutte le funzionalità nella visualizzazione dell'infrastruttura, è necessario eseguire l'[aggiornamento cumulativo 22](https://support.microsoft.com/help/4072852) per questi componenti.
- Per usare la visualizzazione dell'infrastruttura, selezionare lo scenario di replica appropriato per lo specifico ambiente. È possibile eseguire il drill-down nella visualizzazione per altri dettagli. La tabella seguente illustra gli scenari rappresentati.

    **Scenario** | **State**  | **Visualizzazione disponibile?**
    --- |--- | ---
    **Replica tra siti locali** | Tutti gli stati | No  
    **Replica di VM di Azure tra aree di Azure**  | Replica abilitata/replica iniziale in corso | Yes
    **Replica di VM di Azure tra aree di Azure** | Failover/failback | No    
    **Replica VMware in Azure** | Replica abilitata/replica iniziale in corso | Yes     
    **Replica VMware in Azure** | Failover/failback | No       
    **Replica Hyper-V in Azure** | Failover/failback | No 

- Per visualizzare la visualizzazione dell'infrastruttura per una singola macchina di replica, nel menu dell'insieme di credenziali fare clic su **Elementi replicati** e selezionare un server.  

### <a name="common-questions"></a>Domande frequenti


**Perché il conteggio delle macchine virtuali nella visualizzazione dell'infrastruttura dell'insieme di credenziali è diverso dal conteggio totale visualizzato negli elementi replicati?**

L'ambito della visualizzazione dell'infrastruttura nell'insieme di credenziali viene definito dagli scenari di replica. Solo le macchine nello scenario di replica attualmente selezionato sono incluse nel conteggio per la visualizzazione. Inoltre, vengono conteggiate solo le macchine virtuali che sono configurate per la replica in Azure. Le macchine sottoposte a failover o le macchine che eseguono la replica in un sito locale non vengono conteggiate nella visualizzazione.

**Perché il conteggio degli elementi replicati visualizzato nel menu Informazioni di base è diverso dal conteggio totale degli elementi replicati nel dashboard?**

Solo le macchine per cui la replica iniziale è stata completata vengono incluse nel conteggio visualizzato nel menu Informazioni di base. Il totale degli elementi replicati include tutte le macchine nell'insieme di credenziali, incluse quelle per cui la replica iniziale è attualmente in corso.


## <a name="monitor-recovery-plans"></a>Monitorare i piani di ripristino

Nella sezione **Piani di ripristino** è possibile esaminare il numero di piani, creare nuovi piani e modificare quelli esistenti.  

## <a name="monitor-jobs"></a>Monitorare i processi

La sezione **Processi** riflette lo stato delle operazioni di Site Recovery.

- La maggior parte delle operazioni in Azure Site Recovery viene eseguita in modo asincrono, con un processo di verifica creato e usato per tenere traccia dello stato dell'operazione. 
- L'oggetto processo contiene tutte le informazioni necessarie per tenere traccia dello stato e dell'avanzamento dell'operazione. 

Monitorare i processi come indicato di seguito:

1. Nella sezione **Processi** del dashboard è possibile visualizzare un riepilogo dei processi completati, in corso o in attesa di input nelle ultime 24 ore. È possibile fare clic su qualsiasi stato per ottenere altre informazioni sui processi interessati.
2. Fare clic su **Visualizza tutto** per visualizzare tutti i processi nelle ultime 24 ore.

    > [!NOTE]
    > È anche possibile accedere alle informazioni sui processi dal menu dell'insieme di credenziali > **Processi di Site Recovery**. 

2. Nell'elenco **Processi di Site Recovery** viene visualizzato un elenco dei processi. Nel menu superiore è possibile ottenere i dettagli degli errori per un processo specifico, filtrare l'elenco dei processi in base a criteri specifici ed esportare i dettagli dei processi selezionati in Excel.
3. È possibile eseguire il drill-down in un processo facendo clic su di esso. 

## <a name="monitor-virtual-machines"></a>Monitorare le macchine virtuali

È possibile monitorare le macchine nella pagina Macchine virtuali del dashboard. 

1. Nell'insieme di credenziali fare clic su **Elementi replicati** per ottenere un elenco delle macchine replicate.  In alternativa, è possibile ottenere un elenco filtrato degli elementi protetti facendo clic su uno dei collegamenti con ambito nella pagina del dashboard.

    ![Visualizzazione dell'elenco Elementi replicati di Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Nella pagina **Elementi replicati** è possibile visualizzare e filtrare le informazioni. Dal menu Azione nella parte superiore è possibile eseguire azioni per una determinata macchina, ad esempio l'esecuzione un failover di test o la visualizzazione di errori specifici.
3.  Fare clic su **Colonne** per visualizzare colonne aggiuntive, ad esempio per l'obiettivo RPO, i problemi di configurazione della destinazione e gli errori di replica.
4. Fare clic su **Filtro** per visualizzare le informazioni in base a parametri specifici, ad esempio l'integrità della replica o criteri di replica specifici.
5. Fare clic con il pulsante destro del mouse su una macchina per avviare operazioni come il failover di test o per visualizzare i dettagli degli specifici errori.
6. Fare clic su una macchina per visualizzare maggiori dettagli su di essa. I dettagli includono: **Informazioni replica**: stato corrente e integrità della macchina.
        - **RPO** (obiettivo del punto di ripristino): RPO corrente per la macchina virtuale e ora dell'ultimo calcolo dell'RPO.
        - **Punti di ripristino**: punti di ripristino più recenti disponibili per la macchina.
        - **Conformità failover**: indica se è stato eseguito un failover di test per la macchina, la versione dell'agente in esecuzione nella macchina (per le macchine che eseguono il servizio Mobility) ed eventuali problemi di configurazione.
        - **Errori**: elenco di sintomi di errore di replica attualmente osservati nella macchina, con le possibili cause e azioni.
        - **Eventi**: elenco cronologico di eventi recenti con conseguenze sulla macchina. I dettagli dell'errore indicano i sintomi di errore attualmente osservabili, mentre gli eventi sono costituiti da una registrazione cronologica dei problemi che potrebbero avere avuto effetto sulla macchina.
        - **Visualizzazione dell'infrastruttura**: mostra lo stato dell'infrastruttura per lo scenario durante la replica delle macchine in Azure.

    ![Dettagli/Panoramica degli elementi replicati di Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Domande frequenti

**Qual è la differenza tra l'RPO e l'ultimo punto di ripristino disponibile?**


- Site Recovery usa un processo asincrono in più passaggi per replicare le macchine in Azure.
- Nel penultimo passaggio della replica le modifiche recenti apportate nella macchina e i metadati vengono copiati in un account di archiviazione di log/cache.
- Queste modifiche e il tag per identificare un punto di ripristino disponibile vengono scritti nell'account di archiviazione nell'area di destinazione.
-  Site Recovery ora può generare un punto di ripristino disponibile per la macchina virtuale.
- A questo punto, è stato soddisfatto l'RPO per le modifiche caricate finora nell'account di archiviazione. In altre parole, l'RPO per la macchina in questo punto è uguale all'intervallo di tempo trascorso dal timestamp corrispondente al punto di ripristino disponibile.
- A questo punto, Site Recovery seleziona i dati caricati dall'account di archiviazione e li applica ai dischi di replica creati per la macchina.
- Site Recovery genera quindi un punto di ripristino e lo rende disponibile per il ripristino in fase di failover. Il punto di ripristino disponibile più recente indica quindi il timestamp corrispondente al punto di ripristino più recente già elaborato e applicato ai dischi di replica.

> [!NOTE]
> Un'ora di sistema non corretta nella macchina di origine di replica o nei server dell'infrastruttura locale sfaserà il valore RPO calcolato. Per la segnalazione accurata degli RPO, assicurarsi che l'orologio di sistema sia preciso in tutti i server e le macchine. 

## <a name="subscribe-to-email-notifications"></a>Sottoscrivere le notifiche tramite posta elettronica

È possibile eseguire la sottoscrizione per ricevere notifiche di posta elettronica per gli eventi critici seguenti:
 
- Stato critico per la macchina replicata.
- Nessuna connettività tra i componenti dell'infrastruttura locale e il servizio Site Recovery. La connettività tra Site Recovery e i server locali registrati in un insieme di credenziali viene rilevata tramite un meccanismo di heartbeat.
- Errori di failover.

Eseguire la sottoscrizione come indicato di seguito:

Nell'insieme di credenziali > **Monitoraggio e report** fare clic su **Eventi di Site Recovery**.
2. Fare clic su **Notifiche tramite posta elettronica**.
3. In **Notifiche tramite posta elettronica** attivare le notifiche e specificare i destinatari. È possibile inviare le notifiche a tutti gli amministratori della sottoscrizione e facoltativamente a specifici indirizzi di posta elettronica.

    ![Notifiche tramite posta elettronica](./media/site-recovery-monitor-and-troubleshoot/email.png)
