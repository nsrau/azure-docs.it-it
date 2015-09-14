<properties
	pageTitle="Informazioni sulla protezione da sito ad Azure"
	description="Questo articolo descrive i concetti tecnici che aiutano a installare, configurare e gestire Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="anbacker"
	manager="mkjain"
	editor=""/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/01/2015"
	ms.author="anbacker"/>


# Informazioni sulla protezione da sito Hyper-V o VMM ad Azure

Questo articolo descrive i concetti tecnici che aiutano a configurare e gestire la protezione da sito Hyper-V o VMM ad Azure con Azure Site Recovery.

## Informazioni sui componenti

### Distribuzione del sito Hyper-V o VMM per la replica tra ambiente locale e Azure.

Nell'ambito dell'impostazione di ripristino di emergenza tra due siti locali e Azure; il provider di Azure Site Recovery deve essere scaricato e installato nel server VMM insieme all'agent di Azure Recovery Services che deve essere installato in ciascun host Hyper-V.

![Distribuzione del sito VMM per la replica tra siti locali e Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

La distribuzione di un sito Hyper-V è uguale a quella di VMM, con l'unica differenza legata al fatto che provider e agente vengono installati nell'host Hyper-V stesso.

## Informazioni sui flussi di lavoro

### Abilitare la protezione
Una volta protetta una macchina virtuale dal portale o in locale, viene avviato un processo di Ripristino automatico di sistema (ASR) denominato *Abilita protezione*, che può essere monitorato nella scheda PROCESSI.

![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-understanding-site-to-azure-protection/image01.png)

Il processo *Abilita protezione* controlla i prerequisiti prima di richiamare [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), che crea una replica in Azure usando gli input configurati durante la protezione. Il processo *Abilita protezione* avvia la replica iniziale dall'ambiente locale richiamando [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), che invia i dischi virtuali della macchina virtuale in Azure.

![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-understanding-site-to-azure-protection/image02.png)

### Finalizzare la protezione
Quando viene attivata la replica iniziale, viene acquisito uno [snapshot delle VM Hyper-V](https://technet.microsoft.com/library/dd560637.aspx). I dischi rigidi virtuali vengono elaborati uno alla volta fino a quando tutti i dischi non sono stati caricati in Azure. Questa operazione richiede in genere un po' di tempo, a seconda delle dimensioni dei dischi e della larghezza di banda. Per informazioni su come ottimizzare l'utilizzo della rete, vedere [Come gestire la larghezza di banda di rete per la protezione da ambiente locale ad Azure](https://support.microsoft.com/kb/3056159). Una volta completata la replica iniziale, il processo *Finalizza la protezione nella macchina virtuale* configura le impostazioni di rete e post-replica. Mentre la replica iniziale è in corso, viene tenuta traccia di tutte le modifiche ai dischi, come indicato nella sezione Replica dei dati, più avanti. Mentre la replica iniziale è in corso, verrà utilizzato spazio di archiviazione su disco aggiuntivo per lo snapshot e i file HRL. Una volta completata una replica iniziale, lo snapshot delle VM Hyper-V verrà eliminato con la conseguente unione delle modifiche ai dati post-replica iniziale nel disco padre.

![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### Replica dei dati
Lo strumento di monitoraggio della replica Hyper-V, che fa parte del motore di replica Hyper-V, tiene traccia delle modifiche a un disco rigido virtuale in log di replica Hyper-V (*.hrl). I file HRL si troveranno nella stessa directory dei dischi associati. Ogni disco configurato per la replica avrà un file HRL associato. I log vengono inviati all'account di archiviazione del cliente dopo il completamento della replica iniziale. Quando un log è in transito verso Azure, le modifiche al database primario vengono registrate in un altro file di log nella stessa directory.

L'integrità della replica della VM durante la replica iniziale o la replica differenziale può essere monitorata nella visualizzazione VM come illustrato in [Monitorare lo stato di integrità della replica per la macchina virtuale](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).

### Risincronizzazione 
Una macchina virtuale viene contrassegnata per la risincronizzazione quando la replica differenziale ha esito negativo e la replica iniziale completa è dispendiosa in termini di larghezza di banda di rete o di tempo necessario per il completamento. Se, ad esempio le dimensioni dei file HRL raggiungono il 50% delle dimensioni totali del disco, la macchina virtuale viene contrassegnata per la risincronizzazione. La risincronizzazione riduce al minimo la quantità di dati inviati in rete, calcolando checksum dei dischi delle macchine virtuali di origine e di destinazione e inviando solo il differenziale.

Al termine della risincronizzazione, dovrebbe venire ripresa la normale replica differenziale. In caso di interruzione (ad esempio, interruzione della rete, arresto anomalo del servizio Virtual Machine Management e così via), è possibile riprendere la risincronizzazione.

Per impostazione predefinita, la *risincronizzazione automatica* è configurata durante le ore non lavorative. Se la macchina virtuale deve essere risincronizzata manualmente, selezionarla nel portale e fare clic su RISINCRONIZZA. ![Risoluzione dei problemi di Hyper-V a livello locale](media/site-recovery-understanding-site-to-azure-protection/image04.png)

La risincronizzazione usa un algoritmo di suddivisione in blocchi fissi in cui i file di origine e di destinazione vengono divisi in blocchi fissi, per ogni blocco viene generato un checksum e quindi questi ultimi vengono confrontati per determinare quale blocchi dell'origine devono essere applicati alla destinazione.

### Logica di retry
Quando si verificano errori di replica, viene applicata una logica di retry predefinita. Tale logica può essere classificata nelle due categorie indicate di seguito.

| Categoria | Scenari |
|---------------------------|----------------------------------------------|
| Errore irreversibile | Non vengono eseguiti nuovi tentativi. Lo stato della replica della macchina virtuale viene visualizzato come Critico ed è necessario l'intervento di un amministratore. Ecco alcuni esempi: <ul><li>Catena VHD interrotta</li><li>Stato non valido della macchina virtuale di replica</li><li>Errore di autenticazione di rete</li><li>Errore di autorizzazione</li><li>Macchina virtuale non trovata nel caso di un server Hyper-V autonomo</li></ul>|
| Errore reversibile | Vengono eseguiti nuovi tentativi durante ogni intervallo di replica usando un backoff in modo esponenziale, così da aumentare l'intervallo tra tentativi dall'inizio del primo tentativo (1, 2, 4, 8, 10 minuti). Se l'errore persiste, viene eseguito un nuovo tentativo ogni 30 minuti. Ecco alcuni esempi: <ul><li>Errore di rete</li><li>Spazio su disco insufficiente</li><li>Condizione di memoria insufficiente</li></ul>|

## Informazioni sul ciclo di vita di protezione e ripristino di una macchina virtuale Hyper-V

![Comprendere la protezione della macchina virtuale Hyper-V & il ciclo di vita del ripristino](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## Altri riferimenti

- [Monitorare e risolvere i problemi di protezione per VMware, VMM, Hyper-V e siti fisici](./site-recovery-monitoring-and-troubleshooting.md)
- [Contattare il supporto Microsoft](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Errori comuni del ripristino automatico di sistema e relative soluzioni](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!---HONumber=September15_HO1-->