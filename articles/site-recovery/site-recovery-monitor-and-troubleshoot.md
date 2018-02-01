---
title: Monitorare e risolvere i problemi di Azure Site Recovery | Microsoft Docs
description: Monitorare e risolvere i problemi relativi alle repliche e alle operazioni di Azure Site Recovery usando il portale
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/06/2017
ms.author: bsiva
ms.openlocfilehash: 6dca032c8611ac4f7e66eb6f7e22e53f49209143
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Monitoraggio e risoluzione dei problemi di Azure Site Recovery

In questo articolo viene illustrato come usare le funzionalità di monitoraggio predefinite di Azure Site Recovery per il monitoraggio e la risoluzione dei problemi. È possibile passare agli argomenti seguenti:
> [!div class="checklist"]
> - Usare il dashboard di Azure Site Recovery (pagina di panoramica dell'insieme di credenziali)
> - Monitorare e risolvere i problemi di replica
> - Monitorare i processi o le operazioni di Azure Site Recovery
> - Sottoscrivere le notifiche tramite posta elettronica

## <a name="using-the-azure-site-recovery-dashboard"></a>Uso del dashboard di Azure Site Recovery

Il dashboard di Azure Site Recovery nella pagina di panoramica dell'insieme di credenziali accorpa tutte le informazioni di monitoraggio per l'insieme di credenziali in un'unica posizione. Esplorando i vari elementi del dashboard dell'insieme di credenziali è possibile eseguire un'analisi approfondita per ottenere informazioni più dettagliate. Gli elementi principali del dashboard di Azure Site Recovery sono i seguenti:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Passare dal dashboard di Backup di Azure a quello di Azure Site Recovery

L'opzione di attivazione/disattivazione nella parte superiore della pagina di panoramica consente di passare dalla pagina del dashboard di Site Recovery a quella di Backup. Questa selezione, una volta eseguita, viene memorizzata e impostata come predefinita per la prossima volta che si aprirà la pagina di panoramica dell'insieme di credenziali. Selezionare l'opzione Site Recovery per visualizzare il dashboard di Site Recovery. 

I diversi elementi della pagina del dashboard di Azure Site Recovery vengono aggiornati automaticamente ogni 10 minuti, in modo che il dashboard visualizzi le ultime informazioni disponibili.

![Funzionalità di monitoraggio nella pagina di panoramica di Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Elementi replicati

La sezione Elementi replicati del dashboard presenta una panoramica dell'integrità della replica dei server protetti nell'insieme di credenziali. 

<table>
<tr>
    <td>Healthy</td>
    <td>L'esecuzione della replica procede normalmente per questi server e non sono stati rilevati sintomi di errore o avviso.</td>
</tr>
<tr>
    <td>Avviso </td>
    <td>Per questi server è stato rilevato uno o più sintomi di avviso che potrebbero influire sulla replica o indicare che la replica non procede normalmente.</td>
</tr>
<tr>
    <td>Critico</td>
    <td>Per questi server è stato rilevato uno o più sintomi di errore di replica critici. Questi sintomi di errore in genere sono indicatori che la replica è bloccata o non sta procedendo con la stessa frequenza di modifica dei dati per questi server.</td>
</tr>
<tr>
    <td>Non applicabile</td>
    <td>Server per cui attualmente non è prevista la replica, ad esempio i server di cui è stato eseguito il failover.</td>
</tr>
</table>

Per visualizzare un elenco di server protetti filtrati in base all'integrità della replica, fare clic sulla descrizione dell'integrità della replica accanto al grafico ad anello. Il collegamento Visualizza tutto accanto al titolo della sezione consente di accedere alla pagina Elementi replicati per l'insieme di credenziali. Usare il collegamento Visualizza tutto per visualizzare l'elenco di tutti i server nell'insieme di credenziali.

### <a name="3-failover-test-success"></a>3. Failover di test riuscito

La sezione Failover di test riuscito del dashboard presenta una suddivisione delle macchine virtuali nell'insieme di credenziali in base allo stato del failover di test. 

<table>
<tr>
    <td>Test consigliato</td>
    <td>Macchine virtuali per cui il failover di test non è riuscito da quando hanno raggiunto uno stato protetto.</td>
</tr>
<tr>
    <td>Eseguito correttamente</td>
    <td>Macchine virtuali per cui è riuscito uno o più failover di test.</td>
</tr>
<tr>
    <td>Non applicabile</td>
    <td>Macchine virtuali attualmente non idonee per un failover di test, ad esempio server di cui è stato effettuato il failover, server per cui la replica iniziale è in corso, server per cui è in corso un failover, i server per cui un failover di test è già in corso.</td>
</tr>
</table>

Fare clic sullo stato del failover di test accanto al grafico ad anello per visualizzare l'elenco di server protetti in base allo stato del failover di test.
 
> [!IMPORTANT]
> Come procedura consigliata, è opportuno eseguire un failover di test sui server protetti almeno una volta ogni sei mesi. L'esecuzione di un failover di test è un modo per testare senza interruzioni il failover dei server e delle applicazioni in un ambiente isolato e consente di valutare la preparazione alla continuità aziendale.

 Un'operazione di failover di test in un server o in un piano di ripristino è considerata riuscita solo dopo che sia l'operazione di failover di test che l'operazione di failover di test di pulizia sono state completate correttamente.

### <a name="4-configuration-issues"></a>4. Problemi di configurazione

La sezione Problemi di configurazione visualizza un elenco di problemi che potrebbero compromettere la possibilità di effettuare correttamente il failover delle macchine virtuali. Le classi di problemi elencati in questa sezione sono:
 - **Configurazioni mancanti:** server protetti in cui mancano configurazioni necessarie, ad esempio una rete di ripristino o un gruppo di risorse di ripristino.
 - **Risorse mancanti:** risorse di destinazione/ripristino configurate non trovate o non disponibili nella sottoscrizione. Ad esempio, la risorsa è stata eliminata oppure ne è stata eseguita la migrazione a una sottoscrizione o a un gruppo di risorse diverso. Le configurazioni di destinazione/ripristino seguenti vengono monitorate in relazione alla disponibilità: gruppo di risorse di destinazione, rete virtuale e subnet di destinazione, account di archiviazione di log/destinazione, set di disponibilità di destinazione, indirizzo IP di destinazione.
 - **Quota sottoscrizione:** il totale delle quote di risorse della sottoscrizione disponibili viene confrontato con il totale necessario per poter effettuare il failover di tutte le macchine virtuali nell'insieme di credenziali. Se il totale disponibile risulta non sufficiente, viene segnalato che il totale delle quote non è sufficiente. Vengono monitorate le quote per le risorse di Azure seguenti: numero di core di macchine virtuali, numero di core di famiglie di macchine virtuali, numero di schede di interfaccia di rete.
 - **Aggiornamenti software:** disponibilità di nuovi aggiornamenti software e di versioni software in scadenza.

I problemi di configurazione (a eccezione della disponibilità di aggiornamenti software) vengono rilevati da un'operazione periodica del validator eseguita ogni 12 ore per impostazione predefinita. Per eseguire immediatamente l'operazione del validator, fare clic sull'icona Aggiorna accanto all'intestazione della sezione *Problemi di configurazione*.

Fare clic sui collegamenti per ottenere altri dettagli sui problemi elencati e sulle macchine virtuali interessate. Per i problemi che hanno effetto su macchine virtuali specifiche, è possibile ottenere altri dettagli facendo clic sul collegamento **richiede attenzione** nella colonna delle configurazioni di destinazione per la macchina virtuale. I dettagli includono raccomandazioni su come risolvere i problemi rilevati.

### <a name="5-error-summary"></a>5. Riepilogo errori

La sezione Riepilogo errori visualizza i sintomi di errore della replica attualmente attiva che potrebbero influire sulla replica dei server nell'insieme di credenziali, oltre al numero di entità interessate a causa di ogni errore.

I sintomi di errore di replica per i server in uno stato di integrità della replica critico o avviso possono essere visualizzati nella tabella Riepilogo errori. 

- Gli errori che hanno effetto sui componenti dell'infrastruttura locale, ad esempio il mancato recapito di un heartbeat dal provider di Azure Site Recovery in esecuzione nel server di configurazione locale, nel server VMM o nell'host Hyper-V, vengono elencati all'inizio della sezione Riepilogo errori
- I sintomi di errore di replica che hanno effetto sui server protetti sono elencato di seguito. Le voci della tabella Riepilogo errori sono elencate in ordine decrescente in base alla gravità dell'errore e quindi in ordine decrescente in base al conteggio dei server interessati.
 

> [!NOTE]
> 
>  È possibile osservare più sintomi di errore di replica in un unico server. Se sono presenti più sintomi di errore in un unico server, ogni sintomo ogni errore includerà tale server nell'elenco dei server interessati. Dopo la risoluzione del problema sottostante che restituisce un sintomo di errore, i parametri di replica risultano migliorati e l'errore viene cancellato dalla macchina virtuale.
>
> > [!TIP]
> Il conteggio dei server interessati è utile per capire se un singolo problema sottostante può influire su più server. Ad esempio, un problema di rete potrebbe influire su tutti i server che eseguono la replica da un sito locale ad Azure. Questa visualizzazione indica rapidamente che la risoluzione di quell'unico problema sottostante consentirà di correggere la replica per più server.
>

### <a name="6-infrastructure-view"></a>6. Visualizzazione dell'infrastruttura

La visualizzazione dell'infrastruttura offre una rappresentazione visiva a livello di scenario dei componenti infrastrutturali coinvolti nella replica. Offre anche una rappresentazione visiva dell'integrità della connettività tra i vari server e tra i server e i servizi di Azure coinvolti nella replica. 

Una linea verde indica che la connessione è integra, mentre una linea rossa con l'icona di errore sovrapposta indica l'esistenza di uno o più sintomi di errore che hanno effetto sulla connettività tra i componenti coinvolti. Passando il puntatore del mouse sopra l'icona di errore sulla riga, vengono visualizzati l'errore e il numero di entità interessate. 

Facendo clic sull'icona di errore, viene visualizzato un elenco filtrato delle entità interessate per gli errori.

![Visualizzazione dell'infrastruttura di Site Recovery (insieme di credenziali)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Assicurarsi che i componenti dell'infrastruttura locale (server di configurazione, server di elaborazione aggiuntivi, macchine virtuali VMware di replica, host Hyper-V, server VMM) eseguano la versione più recente del software di Azure Site Recovery. Per poter usare tutte le funzionalità della visualizzazione dell'infrastruttura, è necessario eseguire l'[aggiornamento cumulativo 22](https://support.microsoft.com/help/4072852) o versione successiva per Azure Site Recovery

Per usare la visualizzazione dell'infrastruttura, selezionare lo scenario di replica appropriato (macchine virtuali di Azure, server fisico/macchine virtuali VMware o Hyper-V) a seconda dell'ambiente di origine. La visualizzazione dell'infrastruttura presentata nella pagina di panoramica dell'insieme di credenziali è una visualizzazione aggregata per l'insieme di credenziali. È possibile eseguire il drill-down dei singoli componenti facendo clic sulle caselle.

Una visualizzazione dell'infrastruttura nell'ambito del contesto di un singolo computer di replica è disponibile nella pagina di panoramica dell'elemento replicato. Per passare alla pagina di panoramica di un server di replica, passare agli elementi replicati dal menu dell'insieme di credenziali e selezionare il server di cui visualizzare i dettagli.

### <a name="infrastructure-view---faq"></a>Visualizzazione dell'infrastruttura - Domande frequenti

**D.** Perché non è disponibile la visualizzazione dell'infrastruttura per la macchina virtuale? </br>
**R.** La funzionalità di visualizzazione dell'infrastruttura è disponibile solo per le macchine virtuali che eseguono la replica in Azure. La funzionalità non è attualmente disponibile per le macchine virtuali che eseguono la replica tra siti locali.

**D.** Perché il conteggio delle macchine virtuali nella visualizzazione dell'infrastruttura dell'insieme di credenziali è diverso dal conteggio totale visualizzato nel grafico ad anello degli elementi replicati?</br>
**R.** L'ambito della visualizzazione dell'infrastruttura nell'insieme di credenziali viene definito dagli scenari di replica. Solo le macchine virtuali che fanno parte dello scenario di replica attualmente selezionato sono incluse nel conteggio delle macchine virtuali presenti nella visualizzazione dell'infrastruttura. Inoltre, per lo scenario selezionato, solo le macchine virtuali attualmente configurate per la replica in Azure vengono incluse nel conteggio delle macchine virtuali presenti nella visualizzazione dell'infrastruttura. Ad esempio, le macchine virtuali di cui è stato effettuato il failover e le macchine virtuali che eseguono la replica in un sito locale non vengono incluse nella visualizzazione dell'infrastruttura.

**D.** Perché il conteggio degli elementi replicati visualizzato nel menu Informazioni di base nella pagina di panoramica è diverso dal conteggio totale degli elementi replicati visualizzato nel grafico ad anello nel dashboard?</br>
**R.** Solo le macchine virtuali per cui la replica iniziale è stata completata vengono incluse nel conteggio visualizzato nel menu Informazioni di base. Il totale del grafico ad anello degli elementi replicati include tutte le macchine virtuali nell'insieme di credenziali, inclusi i server per cui la replica iniziale è attualmente in corso.

**D.** Per quali scenari di replica è disponibile la visualizzazione dell'infrastruttura? </br>
**R.**
>[!div class="mx-tdBreakAll"]
>|Scenario di replica  | Stato VM  | Visualizzazione dell'infrastruttura disponibile  |
>|---------|---------|---------|
>|Macchine virtuali che eseguono la replica tra due siti locali     | -        | No       |
>|Tutti     | Failover eseguito         |  No        |
>|Macchine virtuali che eseguono la replica tra due aree di Azure     | Replica iniziale in corso o Protetta         | Sì         |
>|Macchine virtuali VMware che eseguono la replica in Azure     | Replica iniziale in corso o Protetta        | Sì        |
>|Macchine virtuali VMware che eseguono la replica in Azure     | Le macchine virtuali di cui è stato effettuato il failover vengono replicate in un sito VMware locale         | No         |
>|Macchine virtuali Hyper-V che eseguono la replica in Azure     | Replica iniziale in corso o Protetta        | Sì       |
>|Macchine virtuali Hyper-V che eseguono la replica in Azure     | Failover eseguito/Failback in corso        |  No        |


### <a name="7-recovery-plans"></a>7. Piani di ripristino

La sezione Piani di ripristino visualizza il conteggio dei piani di ripristino nell'insieme di credenziali. Fare clic sul numero per visualizzare l'elenco di piani di ripristino, creare nuovi piani di ripristino o modificare quelli esistenti. 

### <a name="8-jobs"></a>8. Processi

I processi di Azure Site Recovery tengono traccia dello stato delle operazioni di Azure Site Recovery. La maggior parte delle operazioni in Azure Site Recovery viene eseguita in modo asincrono, con un processo di verifica usato per tenere traccia dello stato dell'operazione.  Per informazioni su come monitorare lo stato dell'operazione, vedere la sezione [Monitorare i processi o le operazioni di Azure Site Recovery](#monitor-azure-site-recovery-jobsoperations).

La sezione Processi del dashboard offre le informazioni seguenti:

<table>
<tr>
    <td>Operazione non riuscita</td>
    <td>Processo di Azure Site Recovery non riusciti nelle ultime 24 ore</td>
</tr>
<tr>
    <td>In corso</td>
    <td>Processi di Azure Site Recovery attualmente in corso</td>
</tr>
<tr>
    <td>In attesa di input</td>
    <td>Processi di Azure Site Recovery attualmente in pausa in attesa di un input dall'utente.</td>
</tr>
</table>

Il collegamento Visualizza tutto accanto all'intestazione della sezione consente di accedere alla pagina dell'elenco di processi.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Monitorare e risolvere i problemi di replica

Oltre alle informazioni disponibili nella pagina del dashboard dell'insieme di credenziali, è possibile ottenere altri dettagli e informazioni sulla risoluzione dei problemi nella pagina dell'elenco di macchine virtuali e nella pagina dei dettagli della macchina virtuale. Per visualizzare l'elenco di macchine virtuali protette nell'insieme di credenziali, scegliere l'opzione **Elementi replicati** dal menu dell'insieme di credenziali. In alternativa, è possibile ottenere un elenco filtrato degli elementi protetti facendo clic su uno dei collegamenti con ambito disponibili nella pagina del dashboard dell'insieme di credenziali.

![Visualizzazione dell'elenco Elementi replicati di Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

L'opzione Filtro nella pagina dell'elenco Elementi replicati consente di applicare filtri diversi, ad esempio l'integrità della replica e i criteri di replica. 

L'opzione Selettore di colonna consente di specificare colonne aggiuntive da visualizzare, ad esempio quelle relative a RPO, problemi di configurazione di destinazione ed errori di replica. È possibile avviare le operazioni in una macchina virtuale o visualizzare gli errori che hanno effetto sulla macchina virtuale facendo clic con il pulsante destro del mouse su una particolare riga dell'elenco di computer.

Per eseguire ulteriormente il drill-down, selezionare una macchina virtuale facendo clic su di essa. Verrà aperta la pagina dei dettagli della macchina virtuale. La pagina di panoramica nei dettagli della macchina virtuale contiene un dashboard in cui sono disponibili informazioni aggiuntive relative al computer. 

Nella pagina di panoramica per il computer di replica, sarà possibile trovare:
- RPO (obiettivo del punto di ripristino): RPO corrente per la macchina virtuale e ora dell'ultimo calcolo dell'RPO.
- Punti di ripristino disponibili più recenti per il computer
- Eventuali problemi di configurazione che potrebbero influire sulla conformità del failover del computer. Fare clic sul collegamento per ottenere altri dettagli.
- Dettagli dell'errore: elenco di sintomi di errore di replica attualmente osservati nel computer con le possibili cause e le correzioni consigliate
- Eventi: elenco cronologico di eventi recenti con conseguenze sul computer. Mentre i dettagli dell'errore indicano i sintomi di errore attualmente osservabili nel computer, gli eventi sono costituiti da un record cronologico di diversi eventi che potrebbero avere avuto effetto sul computer, inclusi i sintomi di errore che potrebbero essere stati già notati per il computer.
- Visualizzazione dell'infrastruttura per i computer che eseguono la replica in Azure

![Dettagli/Panoramica degli elementi replicati di Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

Il menu di azione nella parte superiore della pagina include le opzioni per eseguire diverse operazioni, ad esempio il failover di test nella macchina virtuale. Il pulsante Dettagli errore nel menu di azione consente di visualizzare tutti gli errori attualmente attivi, inclusi gli errori di replica, i problemi di configurazione e gli avvisi basati sulle procedure consigliate di configurazione per la macchina virtuale.

> [!TIP]
> L'obiettivo del punto di ripristino (RPO) è diverso dall'ultimo punto di ripristino disponibile.
> 
>Azure Site Recovery usa un processo asincrono in più passaggi per replicare le macchine virtuali in Azure. Nel penultimo passaggio della replica le modifiche recenti apportate nella macchina virtuale e i metadati vengono copiati in un account di archiviazione di log/cache. Dopo che queste modifiche e il tag per identificare un punto di ripristino disponibile sono stati scritti nell'account di archiviazione nell'area di destinazione, Azure Site Recovery ha le informazioni necessarie per generare un punto di ripristino disponibile per la macchina virtuale. A questo punto, è stato soddisfatto l'RPO per le modifiche caricate finora nell'account di archiviazione. In altre parole, l'RPO per la macchina virtuale espresso in questo punto in unità di tempo è uguale all'intervallo di tempo trascorso dal timestamp corrispondente al punto di ripristino disponibile.
>
>Il servizio Azure Site Recovery, che opera in background, seleziona i dati caricati dall'account di archiviazione e li applica ai dischi di replica creati per la macchina virtuale. Genera quindi un punto di ripristino e lo rende disponibile per il ripristino in fase di failover. Il punto di ripristino disponibile più recente indica il timestamp corrispondente al punto di ripristino più recente già elaborato e applicato ai dischi di replica.
>> [!WARNING]
> Un orologio non sincronizzato o un'ora di sistema non corretta nel computer di origine di replica o nei server dell'infrastruttura locale sfaserà il valore RPO calcolato. Per assicurarsi che vengano riportati valori RPO corretti, verificare che l'orologio di sistema sui server coinvolti nella replica sia preciso. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Monitorare i processi o le operazioni di Azure Site Recovery

Azure Site Recovery esegue le operazioni specificate in modo asincrono. Esempi di operazioni che è possibile eseguire sono l'abilitazione della replica, la creazione del piano di ripristino, il failover di test, l'aggiornamento delle impostazioni di replica e così via. A ogni operazione di questo tipo corrisponde un processo creato per tenere traccia dell'operazione e controllarla. L'oggetto processo contiene tutte le informazioni necessarie per tenere traccia dello stato e dell'avanzamento dell'operazione. È possibile tenere traccia dello stato delle varie operazioni di Site Recovery per l'insieme di credenziali dalla pagina Processi. 

Per visualizzare l'elenco di processi di Site Recovery per l'insieme di credenziali, andare alla sezione **Monitoraggio e report** del menu dell'insieme di credenziali e selezionare Processi > Processi di Site Recovery. Selezionare un processo dall'elenco dei processi nella pagina facendo clic su di esso per ottenere altri dettagli sul processo specificato. Se un processo non è stato completato correttamente o presenta errori, è possibile visualizzare altre informazioni sull'errore e sulla possibile correzione facendo clic sul pulsante Dettagli errore nella parte superiore della pagina dei dettagli del processo (accessibile anche dalla pagina dell'elenco dei processi facendo clic con il pulsante destro del mouse sul processo non riuscito). È possibile usare l'opzione Filtro dal menu di azione nella parte superiore della pagina dell'elenco dei processi per filtrare l'elenco in base a criteri specifici e usare il pulsante di esportazione per esportare in Excel i dettagli dei processi selezionati. È anche possibile accedere all'elenco di processi dal collegamento disponibile nella pagina del dashboard di Site Recovery. 

 Per le operazioni eseguite dal portale di Azure, è anche possibile tenere traccia del processo creato e dello stato corrente dalla sezione delle notifiche (icona a forma di campana in alto a destra) del portale di Azure.

## <a name="subscribe-to-email-notifications"></a>Sottoscrivere le notifiche tramite posta elettronica

La funzionalità di notifica tramite posta elettronica predefinita consente di sottoscrivere l'invio di notifiche tramite posta elettronica per gli eventi critici. Se si effettua la sottoscrizione, vengono inviate notifiche tramite posta elettronica per gli eventi seguenti:
- Riduzione al livello critico dell'integrità della replica di un computer di replica.
- Nessuna connettività tra i componenti dell'infrastruttura locale e il servizio Azure Site Recovery. La connettività al servizio Site Recovery dai componenti dell'infrastruttura locale, ad esempio il server di configurazione (VMware) o System Center Virtual Machine Manager (Hyper-V) registrato nell'insieme di credenziali, viene rilevata usando un meccanismo di heartbeat.
- Eventuali errori nelle operazioni di failover.

Per sottoscrivere l'invio di notifiche tramite posta elettronica per Azure Site Recovery, andare alla sezione **Monitoraggio e report** del menu dell'insieme di credenziali e:
1. Selezionare Avvisi ed eventi > Site Recovery Events (Eventi di Site Recovery).
2. Scegliere "Notifiche di posta elettronica" dal menu nella parte superiore della pagina degli eventi che viene aperta.
3. Usare la procedura guidata per attivare o disattivare le notifiche tramite posta elettronica e per selezionare i destinatari delle notifiche. È possibile specificare che le notifiche vengano inviate a tutti gli amministratori della sottoscrizione e/o fornire un elenco di indirizzi di posta elettronica a cui inviare notifiche. 