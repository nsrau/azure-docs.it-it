---
title: Monitorare Azure Site Recovery | Microsoft Docs
description: Monitorare e risolvere i problemi relativi alle repliche e alle operazioni di Azure Site Recovery usando il portale
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717359"
---
# <a name="monitor-site-recovery"></a>Site Recovery di monitoraggio

Questo articolo illustra come monitorare [Site Recovery](site-recovery-overview.md)di Azure, usando Site Recovery il monitoraggio incorporato.  È possibile monitorare:

- L'integrità e lo stato dei computer replicati da Site Recovery
- Verificare lo stato di failover dei computer.
- Problemi ed errori che interessano la configurazione e la replica.
- Componenti dell'infrastruttura, ad esempio server locali.


## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, è consigliabile esaminare le [domande di monitoraggio più comuni](monitoring-common-questions.md) .

## <a name="monitor-in-the-dashboard"></a>Monitorare nel dashboard

1. Nell'insieme di credenziali fare clic su **Panoramica**. Il dashboard dei servizi di ripristino consolida tutte le informazioni di monitoraggio per l'insieme di credenziali in un'unica posizione. Sono disponibili pagine per Site Recovery e per il servizio backup di Azure ed è possibile passare da una all'altra.

    ![Dashboard di Site Recovery](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Dal dashboard eseguire il drill-down in aree diverse. 

    ![Dashboard di Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. In **elementi replicati**fare clic su **Visualizza tutto** per visualizzare tutti i server nell'insieme di credenziali.
4. Fare clic su Dettagli stato in ogni sezione per eseguire il drill-down.
5. In **visualizzazione infrastruttura**, ordinare le informazioni di monitoraggio per il tipo di computer che si sta replicando.

## <a name="monitor-replicated-items"></a>Monitorare gli elementi replicati

In **elementi replicati**monitorare lo stato di tutti i computer nell'insieme di credenziali in cui è abilitata la replica.

**Stato** | **Dettagli**
--- | ---
Integra | La replica sta procedendo normalmente. Non sono stati rilevati sintomi di errore o avviso.
Avviso | Sono stati rilevati uno o più sintomi di avviso che potrebbero influire sulla replica.
Critico | Sono stati rilevati uno o più sintomi di errore di replica critici.<br/><br/> Questi sintomi di errore in genere sono indicatori del fatto che la replica è bloccata o non sta procedendo con la stessa frequenza di modifica dei dati.
Non applicabile | Server di cui attualmente non è prevista la replica. Potrebbe includere le macchine di cui è stato eseguito il failover.

## <a name="monitor-test-failovers"></a>Monitorare i failover di test

In **esito positivo test di failover**monitorare lo stato di failover per i computer nell'insieme di credenziali.

- È consigliabile eseguire un failover di test nelle macchine replicate almeno una volta ogni sei mesi. Si tratta di un modo per verificare che il failover funzioni come previsto, senza compromettere l'ambiente di produzione. 
- Un failover di test è considerato riuscito solo una volta che il failover e la pulizia dopo il failover sono stati completati correttamente.

**Stato** | **Dettagli**
--- | ---
Test consigliato | Macchine in cui non è stato eseguito un failover di test dal momento in cui è stata abilitata la protezione.
Eseguito correttamente | Macchine con uno o più failover di test riusciti.
Non applicabile | Macchine attualmente non idonee per un failover di test. Ad esempio, macchine di cui è stato eseguito il failover o con replica iniziale/failover di test/failover in corso.

## <a name="monitor-configuration-issues"></a>Monitorare i problemi di configurazione

In **problemi di configurazione**, monitorare eventuali problemi che potrebbero invalidare la possibilità di eseguire il failover.

- I problemi di configurazione (ad eccezione della disponibilità di aggiornamenti software) vengono rilevati da un'operazione periodica del validator eseguita ogni 12 ore per impostazione predefinita. Per eseguire immediatamente l'operazione del validator, fare clic sull'icona Aggiorna accanto all'intestazione della sezione **Problemi di configurazione**.
- Fare clic sui collegamenti per ottenere altri dettagli. Per i problemi che influiscano su computer specifici, fare clic su **richiesta attenzione** nella colonna **configurazioni di destinazione** . I dettagli includono consigli per la correzione.

**Stato** | **Dettagli**
--- | ---
Configurazioni mancanti | Un'impostazione necessaria non è presente, ad esempio una rete di ripristino o un gruppo di risorse.
Risorse mancanti | Una risorsa specificata non è stata trovata o non è disponibile nella sottoscrizione. Ad esempio, la risorsa è stata eliminata o ne è stata eseguita la migrazione. Le risorse monitorate includono gruppo di risorse di destinazione, rete virtuale e subnet di destinazione, account di archiviazione di log/destinazione, set di disponibilità di destinazione, indirizzo IP di destinazione.
Quota sottoscrizione |  Il totale delle quote di risorse della sottoscrizione disponibili viene confrontato con il totale necessario per effettuare il failover delle macchine nell'insieme di credenziali.<br/><br/> Se le risorse sono insufficienti, viene segnalato che il totale delle quote non è sufficiente.<br/><br/> Le quote monitorano il conteggio dei core delle macchine virtuali, il conteggio dei core delle famiglie di macchine virtuali e il conteggio delle schede di interfaccia di rete (NIC).
Aggiornamenti software | Disponibilità di nuovi aggiornamenti software e informazioni sulle versioni software in scadenza.


## <a name="monitor-errors"></a>Monitoraggio degli errori

In **Riepilogo errori**, monitorare i sintomi di errore attualmente attivi che potrebbero influisca sulla replica dei server nell'insieme di credenziali e monitorare il numero di computer interessati.

- Gli errori che influiscano sui componenti dell'infrastruttura locale vengono visualizzati all'inizio della sezione. Ad esempio, la mancata ricezione di un heartbeat dal provider Azure Site Recovery nel server di configurazione locale o nell'host Hyper-V.
- Vengono quindi elencati i sintomi di errore di replica che hanno effetto sui server replicati.
- Le voci della tabella sono elencate in ordine decrescente in base alla gravità dell'errore e quindi in ordine decrescente in base al conteggio delle macchine interessate.
- Il numero di server interessati è un modo utile per capire se un singolo problema sottostante potrebbe influisca su più computer. Ad esempio, un problema di rete potrebbe potenzialmente influire su tutti i computer che vengono replicati in Azure. 
- Possono verificarsi più errori di replica in un singolo server. In questo caso, il server viene conteggiato nell'elenco dei server interessati per ogni sintomo di errore. Dopo aver risolto il problema, i parametri di replica migliorano e l'errore viene cancellato dalla macchina.

## <a name="monitor-the-infrastructure"></a>Monitorare l'infrastruttura.

In **visualizzazione infrastruttura**monitorare i componenti dell'infrastruttura necessari per la replica e l'integrità della connettività tra i server e i servizi di Azure.

- Una linea verde indica che la connessione è integra.
- Una linea rossa con l'icona di errore sovrapposta indica l'esistenza di uno o più sintomi di errore che influiscano sulla connettività.
-  Passare il puntatore del mouse sopra l'icona di errore per visualizzare l'errore e il numero di entità interessate. Fare clic sull'icona per visualizzare un elenco filtrato delle entità interessate.

    ![Visualizzazione dell'infrastruttura di Site Recovery (insieme di credenziali)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Suggerimenti per il monitoraggio dell'infrastruttura

- Assicurarsi che i componenti dell'infrastruttura locale (server di configurazione, server di elaborazione, server VMM, host Hyper-V, macchine VMware) eseguano le versioni più recenti del provider di Site Recovery e/o degli agenti.
- Per usare tutte le funzionalità nella visualizzazione dell'infrastruttura, è necessario eseguire l'[aggiornamento cumulativo 22](https://support.microsoft.com/help/4072852) per questi componenti.
- Per usare la visualizzazione dell'infrastruttura, selezionare lo scenario di replica appropriato per lo specifico ambiente. È possibile eseguire il drill-down nella visualizzazione per altri dettagli. La tabella seguente illustra gli scenari rappresentati.

    **Scenario** | **Stato**  | **Visualizzazione disponibile?**
    --- |--- | ---
    **Replica tra siti locali** | Tutti gli stati | No 
    **Replica di VM di Azure tra aree di Azure**  | Replica abilitata/replica iniziale in corso | Yes
    **Replica di VM di Azure tra aree di Azure** | Failover/failback | No   
    **Replica VMware in Azure** | Replica abilitata/replica iniziale in corso | Sì     
    **Replica VMware in Azure** | Failover/failback | No      
    **Replica Hyper-V in Azure** | Failover/failback | No

- Per visualizzare la visualizzazione dell'infrastruttura per una singola macchina di replica, nel menu dell'insieme di credenziali fare clic su **Elementi replicati** e selezionare un server.  




## <a name="monitor-recovery-plans"></a>Monitorare i piani di ripristino

Nei **piani di ripristino**monitorare il numero di piani, creare nuovi piani e modificare quelli esistenti.  

## <a name="monitor-jobs"></a>Monitorare i processi

In **processi**monitorare lo stato delle operazioni di Site Recovery.

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

In **elementi replicati**ottenere un elenco di computer replicati. 
    ![Site Recovery visualizzazione elenco elementi replicati](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. È possibile visualizzare e filtrare le informazioni. Dal menu Azione nella parte superiore è possibile eseguire azioni per una determinata macchina, ad esempio l'esecuzione un failover di test o la visualizzazione di errori specifici.
3. Fare clic su **Colonne** per visualizzare colonne aggiuntive, ad esempio l'obiettivo RPO, i problemi di configurazione della destinazione e gli errori di replica.
4. Fare clic su **Filtro** per visualizzare le informazioni in base a parametri specifici, ad esempio l'integrità della replica o criteri di replica specifici.
5. Fare clic con il pulsante destro del mouse su una macchina per avviare operazioni come il failover di test o per visualizzare i dettagli degli specifici errori.
6. Fare clic su una macchina per visualizzare maggiori dettagli su di essa. I dettagli includono:
   - **Informazioni replica**: stato corrente e integrità della macchina.
   - **RPO** (obiettivo del punto di ripristino): RPO corrente per la macchina virtuale e ora dell'ultimo calcolo dell'RPO.
   - **Punti di ripristino**: punti di ripristino disponibili più recenti per la macchina.
   - **Conformità failover**: indica se è stato eseguito un failover di test per la macchina, la versione dell'agente in esecuzione nella macchina (per le macchine che eseguono il servizio Mobility) ed eventuali problemi di configurazione.
   - **Errori**: elenco di sintomi di errore di replica attualmente osservati nella macchina, con le possibili cause e azioni.
   - **Eventi**: elenco cronologico di eventi recenti con conseguenze sulla macchina. I dettagli dell'errore indicano i sintomi di errore attualmente osservabili, mentre gli eventi sono costituiti da una registrazione cronologica dei problemi che potrebbero avere avuto effetto sulla macchina.
   - **Visualizzazione dell'infrastruttura**: mostra lo stato dell'infrastruttura per lo scenario durante la replica delle macchine in Azure.

     ![Dettagli/Panoramica degli elementi replicati di Site Recovery](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Sottoscrivere le notifiche tramite posta elettronica

È possibile eseguire la sottoscrizione per ricevere notifiche di posta elettronica per gli eventi critici seguenti:
 
- Stato critico per la macchina replicata.
- Nessuna connettività tra i componenti dell'infrastruttura locale e il servizio Site Recovery. La connettività tra Site Recovery e i server locali registrati in un insieme di credenziali viene rilevata tramite un meccanismo di heartbeat.
- Errori di failover.

Eseguire la sottoscrizione come indicato di seguito:

Nella sezione **monitoraggio** > dell'insieme di credenziali fare clic su **Site Recovery eventi**.
1. Fare clic su **Notifiche tramite posta elettronica**.
1. In **Notifiche tramite posta elettronica** attivare le notifiche e specificare i destinatari. È possibile inviare le notifiche a tutti gli amministratori della sottoscrizione e facoltativamente a specifici indirizzi di posta elettronica.

    ![Notifiche tramite posta elettronica](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sul](monitor-log-analytics.md) monitoraggio Site Recovery con monitoraggio di Azure.
