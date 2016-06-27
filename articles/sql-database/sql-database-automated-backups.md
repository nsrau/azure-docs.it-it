<properties
   pageTitle="Continuità aziendale cloud - Backup predefinito - Database SQL | Microsoft Azure"
   description="Informazioni sui backup predefiniti del database SQL che consentono di ripristinare un database SQL di Azure a un momento precedente o di copiare un database in uno nuovo in un'area geografica (fino a 35 giorni)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-features"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Panoramica: Backup automatici del database SQL

Il servizio di database SQL di Azure protegge tutti i database con un backup automatizzato che viene conservato per 7 giorni per l'opzione Basic, 14 giorni per l'opzione Standard e 35 giorni per l'opzione Premium. È possibile usare questi backup automatizzati per eseguire ripristini temporizzati e per ripristinare un database eliminato dopo un danneggiamento o un'eliminazione accidentale dei dati.

I backup del database vengono eseguiti automaticamente senza alcuna necessità di fornire il consenso esplicito e senza costi aggiuntivi. Le funzionalità automatiche di backup e ripristino temporizzato offrono un modo per proteggere i database da un danneggiamento o un'eliminazione accidentale a costo zero e senza l'intervento dell'amministratore, qualunque sia la causa.

## Costi dei backup automatici

Il database SQL di Microsoft Azure offre fino al 200% delle risorse di archiviazione massime del database sottoposto a provisioning per la risorsa di archiviazione di backup senza costi aggiuntivi. Ad esempio, se si usa un'istanza di database Standard con una dimensione di database con provisioning pari a 250 GB, saranno disponibili 500 GB di archiviazione di backup senza costi aggiuntivi. Se il database supera lo spazio di archiviazione di backup fornito, è possibile scegliere di ridurre il periodo di conservazione contattando il supporto tecnico di Azure oppure di pagare lo spazio di archiviazione di backup aggiuntivo, che viene addebitato in base alla tariffa standard per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS, Read-Access Geographically Redundant Storage).

## Dettagli dei backup automatici

Tutti i database Basic, Standard e Premium sono protetti da backup automatici. I backup completi vengono eseguiti ogni settimana, i backup differenziali ogni giorno e i backup del log ogni 5 minuti. Il primo backup completo viene pianificato subito dopo la creazione di un database. In genere questo viene completato entro 30 minuti, ma può richiedere più tempo. Se un database è già di grandi dimensioni, ad esempio se viene creato come risultato della copia o del ripristino di un database da un database di grandi dimensioni, il primo backup completo potrebbe richiedere più tempo. Dopo il primo backup completo l'esecuzione di tutti i successivi backup è pianificata e gestita automaticamente in background. I tempi esatti dei backup completi e differenziali sono determinati dal sistema per bilanciare il carico complessivo. I file di backup vengono archiviati in un account di archiviazione con ridondanza geografica con accesso in lettura (RA-GRS) per assicurare la disponibilità a scopo di ripristino di emergenza.

## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Ripristino geografico](sql-database-geo-restore.md)
- [Replica geografica attiva](sql-database-geo-replication-overview.md)
- [Progettare un'applicazione per il ripristino di emergenza cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->