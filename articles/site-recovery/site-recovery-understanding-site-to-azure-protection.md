<properties
	pageTitle="Replica Hyper-V con Azure Site Recovery | Microsoft Azure"
	description="Questo articolo descrive i concetti tecnici che permettono di installare, configurare e gestire Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="Rajani-Janaki-Ram"
	manager="mkjain"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="09/12/2016"
	ms.author="rajanaki"/>


# Replica Hyper-V con Azure Site Recovery

Questo articolo descrive i concetti tecnici che permettono di configurare e gestire la protezione da sito Hyper-V o da sito System Center Virtual Machine Manager (VMM) ad Azure usando Azure Site Recovery.

## Configurazione dell'ambiente di origine per la replica tra locale e Azure

Durante la configurazione del ripristino di emergenza tra locale e Azure, assicurarsi di scaricare e installare il provider di Azure Site Recovery nel server VMM. Installare l'agente di Servizi di ripristino di Azure in ognuno degli host Hyper-V.

![Distribuzione del sito VMM per la replica tra ambiente locale e Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

La configurazione dell'ambiente di origine in un'infrastruttura Hyper-V gestita è simile a quella che si esegue in un'infrastruttura VMM gestita. L'unica differenza è che il provider e l'agente sono installati nell'host Hyper-V stesso. Nell'ambiente VMM il provider è installato nel server VMM e l'agente è installato negli host Hyper-V, in caso di replica in Azure.

## Flussi di lavoro

### Abilitare la protezione
Dopo aver protetto una macchina virtuale dal portale di Azure o in locale, viene avviato un processo di Azure Site Recovery denominato **Abilita protezione**, che è possibile monitorare nella scheda **Processi**.

![Processo "Abilita protezione" nell'elenco dei processi](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

Il processo **Abilita protezione** verifica i prerequisiti prima di richiamare il metodo [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx). Questo metodo crea una replica in Azure usando input configurati durante la protezione.

Il processo **Abilita protezione** avvia la replica iniziale dall'ambiente locale richiamando il metodo [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), che invia ad Azure i dischi virtuali della macchina virtuale.

![Dettagli del processo "Abilita protezione"](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### Finalizzare la protezione nella macchina virtuale
Quando viene attivata la replica iniziale, viene acquisito uno [snapshot della VM Hyper-V](https://technet.microsoft.com/library/dd560637.aspx). I dischi rigidi virtuali vengono elaborati uno alla volta fino a quando tutti i dischi non sono stati caricati in Azure. Questa operazione richiede in genere un po' di tempo, a seconda delle dimensioni dei dischi e della larghezza di banda. Per informazioni su come ottimizzare l'utilizzo della rete, vedere [How to manage on-premises to Azure protection network bandwidth usage](https://support.microsoft.com/kb/3056159) (Come gestire l'utilizzo della larghezza di banda di rete per la protezione da ambiente locale ad Azure).

Al termine della replica iniziale, il processo **Finalizza la protezione nella macchina virtuale** configura le impostazioni di rete e post-replica. Mentre la replica iniziale è in corso:

- Vengono rilevate tutte le modifiche ai dischi.
- Viene utilizzato altro spazio di archiviazione su disco per i file di log della replica Hyper-V (HRL) e degli snapshot.

Al termine della replica iniziale, lo snapshot della macchina virtuale Hyper-V viene eliminato. L'eliminazione comporta l'unione delle modifiche ai dati dopo la replica iniziale nel disco padre.

![Processo "Finalizza la protezione nella macchina virtuale" nell'elenco dei processi](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### Replica dei dati
Lo strumento di monitoraggio della replica Hyper-V, che fa parte del motore di replica Hyper-V, tiene traccia delle modifiche apportate a un disco rigido virtuale in file di log della replica Hyper-V (HRL). I file HRL si trovano nella stessa directory dei dischi associati.

A ogni disco configurato per la replica è associato un file HRL. I log vengono inviati all'account di archiviazione del cliente al termine della replica iniziale. Quando un log è in transito verso Azure, le modifiche al database primario vengono registrate in un altro file di log nella stessa directory.

Durante la replica iniziale o la replica differenziale è possibile monitorare l'integrità della replica della VM nella visualizzazione VM, come illustrato in [Monitorare lo stato della replica per la macchina virtuale](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).

### Risincronizzazione
Una macchina virtuale viene contrassegnata per la risincronizzazione quando la replica differenziale ha esito negativo e la replica iniziale completa è dispendiosa in termini di tempo o di larghezza di banda di rete. Se, ad esempio, le dimensioni del file HRL raggiungono il 50% delle dimensioni totali del disco, la macchina virtuale viene contrassegnata per la risincronizzazione. La risincronizzazione riduce al minimo la quantità di dati inviati in rete, calcolando i checksum dei dischi delle macchine virtuali di origine e di destinazione e inviando solo il differenziale.

Al termine della risincronizzazione, dovrebbe riprendere la normale replica differenziale. Se si verifica un'interruzione di rete o un'altra interruzione, è possibile riprendere la risincronizzazione.

Per impostazione predefinita, la risincronizzazione automatica viene configurata con una pianificazione al di fuori dell'orario lavorativo. Se la macchina virtuale deve essere risincronizzata manualmente, selezionarla dal portale e fare clic su **Risincronizza**.

![Risincronizzazione manuale](media/site-recovery-understanding-site-to-azure-protection/image04.png)

La risincronizzazione usa un algoritmo di suddivisione in blocchi a blocchi fissi che suddivide appunto i file di origine e di destinazione in blocchi fissi. I checksum per ogni blocco vengono generati e messi a confronto per determinare quali blocchi dell'origine devono essere applicati alla destinazione.

### Logica di retry
Per gli errori di replica viene applicata una logica di ripetizione dei tentativi predefinita. Tale logica può essere classificata nelle due categorie seguenti:

| Categoria | Scenari |
|---------------------------|----------------------------------------------|
| Errore irreversibile | Non viene eseguito alcun nuovo tentativo. Lo stato della replica della macchina virtuale è **Critico** ed è necessario l'intervento di un amministratore. Ecco alcuni esempi: <ul><li>Catena VHD interrotta</li><li>Stato non valido della macchina virtuale di replica</li><li>Errore di autenticazione di rete</li><li>Errore di autorizzazione</li><li>Macchina virtuale non trovata nel caso di un server Hyper-V autonomo</li></ul>|
| Errore reversibile | Vengono eseguiti nuovi tentativi durante ogni intervallo di replica usando un backoff esponenziale che permette di aumentare l'intervallo tra i tentativi dall'inizio del primo tentativo (1, 2, 4, 8, 10 minuti). Se l'errore persiste, viene eseguito un nuovo tentativo ogni 30 minuti. Ecco alcuni esempi: <ul><li>Errore di rete</li><li>Spazio su disco insufficiente</li><li>Condizione di memoria insufficiente</li></ul>|

## Ciclo di vita di ripristino e protezione di macchine virtuali Hyper-V

![Ciclo di vita di ripristino e protezione di macchine virtuali Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## Altri riferimenti

- [Monitorare e risolvere i problemi di protezione per le macchine virtuali e i server fisici](./site-recovery-monitoring-and-troubleshooting.md)
- [Contattare il supporto Microsoft](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Errori più comuni di Azure Site Recovery e relative soluzioni](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!---HONumber=AcomDC_0928_2016-->