<properties
   pageTitle="Continuità aziendale cloud - Backup predefinito - Database SQL | Microsoft Azure"
   description="Informazioni sui backup predefiniti del database SQL che consentono di ripristinare un database SQL di Azure a un momento precedente o di copiare un database in uno nuovo in un'area geografica (fino a 35 giorni)."
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Backup automatici del database SQL

Il servizio di database SQL di Azure protegge tutti i database con un backup automatizzato che viene conservato per 7 giorni per l'opzione Basic, 35 giorni per l’opzione Standard e 35 giorni per l'opzione Premium. Vedere l'articolo [Opzioni e prestazioni disponibili in ogni livello di servizio del database SQL](sql-database-service-tiers.md) per maggiori informazioni sulle funzioni disponibili per ogni livello di servizio.

I backup del database vengono eseguiti automaticamente senza alcuna necessità di fornire il consenso esplicito e senza costi aggiuntivi. Le funzionalità automatiche di backup e ripristino temporizzato offrono un modo per proteggere i database da un danneggiamento o un'eliminazione accidentale a costo zero e senza l'intervento dell'amministratore, qualunque sia la causa. È possibile usare questi backup automatizzati per eseguire ripristini temporizzati e per ripristinare un database eliminato dopo un danneggiamento o un'eliminazione accidentale dei dati.

## Costi dei backup automatici

Il database SQL di Microsoft Azure offre fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning per la risorsa di archiviazione di backup senza costi aggiuntivi. Ad esempio, se si usa un'istanza di database Standard con una dimensione di database con provisioning pari a 250 GB, saranno disponibili 500 GB di archiviazione di backup senza costi aggiuntivi. Se il database supera lo spazio di archiviazione di backup fornito, è possibile scegliere di ridurre il periodo di conservazione contattando il supporto tecnico di Azure oppure di pagare lo spazio di archiviazione di backup aggiuntivo, che viene addebitato in base alla tariffa standard per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS, Read-Access Geographically Redundant Storage).

## Dettagli dei backup automatici

Tutti i database Basic, Standard e Premium sono protetti da backup automatici. I backup completi vengono eseguiti ogni settimana, i backup differenziali ogni giorno e i backup del log ogni 5 minuti. Il primo backup completo viene pianificato subito dopo la creazione di un database. In genere questo viene completato entro 30 minuti, ma può richiedere più tempo. Se un database è già di grandi dimensioni, ad esempio se viene creato come risultato della copia o del ripristino di un database da un database di grandi dimensioni, il primo backup completo potrebbe richiedere più tempo. Dopo il primo backup completo l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. I tempi esatti dei backup completi e differenziali sono determinati dal sistema per bilanciare il carico complessivo.

## Ridondanza geografica

I file di backup vengono archiviati in un account di archiviazione con ridondanza geografica con accesso in lettura (RA-GRS) per assicurare la disponibilità a scopo di ripristino di emergenza. Di seguito viene mostrata una replica geografica di backup settimanali e mensili archiviati in un account di archiviazione con ridondanza geografica con accesso in lettura (RA-GRS) per assicurare la disponibilità a scopo di ripristino di emergenza.

![ripristino geografico](./media/sql-database-geo-restore/geo-restore-1.png)

## Uso di backup automatizzati

È possibile [ripristinare un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md) durante il relativo [periodo di conservazione](sql-database-service-tiers.md) in:

- Un nuovo database nello stesso server logico ripristinato in una temporizzazione specificata entro il periodo di conservazione.
- Un database nello stesso server logico ripristinato all'ora di eliminazione per un database eliminato.
- Un nuovo database in qualsiasi server logico in qualsiasi area ripristinato ai backup giornalieri più recenti nell'archiviazione BLOB con replica geografica (RA-GRS).

È inoltre possibile usare [backup automatici del database SQL](sql-database-automated-backups.md) per creare una [copia del database](sql-database-copy.md) in qualsiasi server logico in qualsiasi area coerente da un punto di vista transazionale con il database SQL corrente. È possibile usare la copia del database ed [esportare in un file BACPAC](sql-database-export.md) per archiviare una copia di un database coerente da un punto di vista transazionale per l'archiviazione a lungo termine oltre il periodo di conservazione o trasferire una copia del database in un'istanza locale o un'istanza VM di Azure di SQL Server.

## Cosa accade al periodo di conservazione del punto di ripristino in caso di downgrade o aggiornamento in base al livello di servizio

Dopo il downgrade a un livello di prestazioni inferiore, il periodo di conservazione del punto di ripristino viene limitato immediatamente al periodo di conservazione del livello di prestazioni del database corrente. Se invece il livello di servizio viene aggiornato, il periodo di conservazione inizia a estendersi solo dopo l'aggiornamento del database. Se ad esempio si esegue il downgrade del database da P1 a S3, il periodo di conservazione cambierà immediatamente da 35 a 35 giorni e tutti i punti di ripristino precedenti al nuovo periodo di 35 giorni non saranno più disponibili. Successivamente, se il database viene di nuovo aggiornato a P1, il periodo di conservazione inizierà da 35 giorni e comincerà ad estendersi fino a 35 giorni.

## Quanto dura il periodo di conservazione di un database rimosso? 
Il periodo di conservazione è determinato dal livello di servizio associato al database prima della rimozione o dal numero di giorni in cui il database esiste ancora (viene usato il valore più basso).

> [AZURE.IMPORTANT] Se si elimina un'istanza del server di database SQL di Azure, vengono eliminati anche tutti i relativi database e non possono essere recuperati. Non è attualmente disponibile alcun supporto per il ripristino di un server eliminato.

## Passaggi successivi

- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](sql-database-recovery-using-backups.md)
- Per altre informazioni sulle opzioni di ripristino più veloci, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](sql-database-geo-replication-overview.md)
- Per altre informazioni sull'uso dei backup automatici per l'archiviazione, vedere [Copiare un database SQL di Azure](sql-database-copy.md)
- Per un quadro generale, vedere la [panoramica sulla continuità aziendale](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0706_2016-->