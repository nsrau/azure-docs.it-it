<properties
   pageTitle="Backup del database SQL | Microsoft Azure"
   description="Informazioni sui backup dei database predefiniti del database SQL che consentono di ripristinare un database SQL di Azure a un momento precedente o di copiare un database in uno nuovo in un'area geografica (fino a 35 giorni)."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="carlrab"/>

# Backup del database SQL

Il database SQL crea automaticamente backup dei database come parte dell'offerta del servizio, senza che sia necessario acconsentire esplicitamente e senza costi aggiuntivi. Usare i backup dei database per ripristinare lo stato di un momento specifico precedente per un database. Questo articolo illustra i concetti specifici della funzionalità di backup dei database nel database SQL.

## Informazioni sul backup del database  

Un backup del database è un file che archivia le informazioni relative allo stato del database in un momento specifico. Il database SQL crea backup [completi](https://msdn.microsoft.com/library/ms186289.aspx), [differenziali](https://msdn.microsoft.com/library/ms175526.aspx) e con [log delle transazioni](https://msdn.microsoft.com/library/ms191429.aspx). Quando si ripristina un database rispetto a un momento specifico, il servizio individua i backup da ripristinare.

I backup dei database sono una parte essenziale di qualsiasi strategia di continuità aziendale e ripristino di emergenza, perché proteggono i dati dal danneggiamento o dall'eliminazione accidentale. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md).

## Archiviazione con ridondanza geografica

Il servizio database SQL archivia i file di backup del database in un account di archiviazione con ridondanza geografica con accesso in lettura (RA-GRS). La funzionalità Archiviazione con ridondanza geografica e accesso in lettura di Archiviazione di Azure replica i file di backup in un [data center associato](../best-practices-availability-paired-regions.md). La replica geografica assicura che sia possibile ripristinare un database nel caso in cui non si possa accedere al backup del database dall'area del database primario. Nell'esempio seguente il database SQL crea backup di database nell'area Stati Uniti orientali e li archivia in un account di Archiviazione con ridondanza geografica e accesso in lettura. Archiviazione di Azure esegue quindi la replica geografica dei backup in un data center associato nell'area Stati Uniti occidentali.

![ripristino geografico](./media/sql-database-geo-restore/geo-restore-1.png)

>[AZURE.NOTE] In Archiviazione di Azure il termine *replica* fa riferimento alla copia dei file da una località a un'altra. La *replica di database* di SQL fa riferimento a più database secondari sincronizzati con un database primario.

Per altre informazioni su:
- Archiviazione con ridondanza geografica, vedere [Replica di Archiviazione di Azure](../storage/storage-redundancy.md).
- Archiviazione con ridondanza geografica e accesso in lettura, vedere [Archiviazione con ridondanza geografica e accesso in lettura](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## Costi del backup del database

Il database SQL di Microsoft Azure offre fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning come risorsa di archiviazione di backup senza costi aggiuntivi. Se si usa ad esempio un'istanza di database Standard con una dimensione di database con provisioning pari a 250 GB, sono disponibili 500 GB di archiviazione di backup senza costi aggiuntivi. Se il database supera le risorse di archiviazione di backup fornite, è possibile scegliere di ridurre il periodo di conservazione contattando il supporto tecnico di Azure. È anche possibile pagare per ottenere risorse di archiviazione di backup aggiuntive, fatturate in base alla tariffa Standard per l'Archiviazione con ridondanza geografica e accesso in lettura.

## Pianificazione dei backup dei database

Tutti i database Basic, Standard e Premium sono protetti da backup automatici. Il backup completo, il backup differenziale e il backup del log delle transazioni vengono eseguiti rispettivamente ogni settimana, ogni ora e ogni cinque minuti. Il primo backup completo viene pianificato subito dopo la creazione di un database. Il completamento richiede in genere 30 minuti, ma potrebbe richiedere più tempo se le dimensioni del database sono elevate. Il backup iniziale, ad esempio, può richiedere più tempo in un database ripristinato o in una copia del database. Dopo il primo backup completo, l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. Il momento esatto per l'esecuzione dei backup di database completi e [differenziali](https://msdn.microsoft.com/library/ms175526.aspx) viene determinato in modo da bilanciare il carico di lavoro di sistema complessivo.

## Periodo di conservazione dei backup dei database

Ogni backup del database SQL viene conservato per 7 giorni per il livello Basic, 35 giorni per il livello Standard e 35 giorni per il livello Premium. Per altre informazioni sulle funzionalità disponibili in ogni livello di servizio, vedere [Livelli di servizio](sql-database-service-tiers.md).

### Cosa accade al periodo di conservazione del punto di ripristino in caso di downgrade o aggiornamento in base al livello di servizio

Dopo il downgrade a un livello di prestazioni inferiore, il periodo di conservazione del punto di ripristino viene limitato immediatamente al periodo di conservazione del livello di prestazioni del database corrente. Se invece il livello di servizio viene aggiornato, il periodo di conservazione inizia a estendersi solo dopo l'aggiornamento del database. Se, ad esempio, viene eseguito il downgrade del database al livello Basic, il periodo di conservazione cambia da 35 giorni a 7 giorni. Tutti i punti di ripristino precedenti a sette giorni risultano immediatamente non disponibili. Quando si aggiorna un database al livello Standard o Premium, il periodo di conservazione inizia da 7 giorni e viene incrementato fino a raggiungere i 35 giorni.

### Quanto dura il periodo di conservazione di un database rimosso? 

Il periodo di conservazione è determinato dal livello di servizio associato al database prima della rimozione o dal numero di giorni in cui il database esiste ancora (viene usato il valore più basso).

> [AZURE.IMPORTANT] Se si elimina un'istanza del server del database SQL di Azure, vengono eliminati anche tutti i database appartenenti all'istanza e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.


## Usi comuni per i backup dei database

L'uso principale per i backup dei database consiste nel ripristinare un database a un punto specifico nel periodo di conservazione. Con un backup del database è possibile ripristinare un database a un punto specifico del tempo, ripristinare un database eliminato al momento in cui è stato eliminato oppure ripristinare un database in un'altra area geografica.

- Per informazioni sul ripristino dei database, vedere [Ripristinare un database dai backup automatici](sql-database-recovery-using-backups.md).

È possibile usare un backup del database per copiare un database in un server SQL logico in qualsiasi area geografica. La copia è coerente a livello transazionale con il database SQL corrente.

- Per informazioni su come copiare un database, vedere [Copiare un database SQL di Azure](sql-database-copy.md).

È anche possibile archiviare i backup automatici oltre il periodo di conservazione creando una copia di database da [esportare in un file BACPAC](sql-database-export.md). Dopo avere creato il file BACPAC, è possibile archiviarlo nelle risorse di archiviazione a lungo termine, oltre il periodo di conservazione. In alternativa, usare il file BACPAC per trasferire una copia del database in SQL Server, in locale o in una macchina virtuale di Azure.

- Per informazioni sull'archiviazione di un backup del database, vedere [Copiare un database SQL di Azure](sql-database-copy.md)


## Argomenti correlati

### Scenari

- Per un quadro generale, vedere la [panoramica sulla continuità aziendale](sql-database-business-continuity.md)

### Funzionalità

Per informazioni su:

- Ripristino di un backup del database, vedere [Ripristinare un database SQL di Azure mediante i backup automatici del database](sql-database-recovery-using-backups.md).
- Archiviazione di un database, vedere [Copiare un database SQL di Azure](sql-database-copy.md).
- Opzioni di ripristino più veloci, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md).

<!-- ### Tasks -->

<!-- ### Tutorials -->

<!---HONumber=AcomDC_0928_2016-->