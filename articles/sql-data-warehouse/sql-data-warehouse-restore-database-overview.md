<properties
   pageTitle="Ripristinare un database in Azure SQL Data Warehouse (Panoramica) | Microsoft Azure"
   description="Panoramica delle opzioni di ripristino del database per ripristinare un database in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Ripristinare un database in Azure SQL Data Warehouse (Panoramica)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-restore-database-overview.md)
- [Portale](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST](sql-data-warehouse-manage-restore-database-rest-api.md)

Questo argomento descrive le opzioni per il ripristino di un database in Azure SQL Data Warehouse. Sono inclusi il ripristino di un data warehouse in tempo reale e di un data warehouse eliminato. I data warehouse in tempo reale ed eliminati vengono ripristinati dagli snapshot automatici creati da tutti i data warehouse.

## Scenari di ripristino

**Ripristino da errori dell'infrastruttura:** questo scenario si riferisce al ripristino da problemi dell'infrastruttura, ad esempio errori del disco e così via. Un cliente vuole garantire la continuità aziendale con un'infrastruttura con tolleranza di errore e disponibilità elevata.

**Ripristino da errori dell'utente:** questo scenario si riferisce al ripristino da danneggiamento o eliminazione di dati non intenzionale o accidentale. Nel caso in cui un utente modifichi o elimini dati inavvertitamente o accidentalmente, è possibile che un cliente voglia garantire la continuità aziendale ripristinando il database da un punto precedente nel tempo.

## Criteri dello snapshot

[AZURE.INCLUDE [Criteri di conservazione dei backup di SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Funzionalità di ripristino del database

Si osservi come SQL Data Warehouse migliora l'affidabilità del database e consente la recuperabilità e il funzionamento continuo negli scenari descritti in precedenza.


### Ridondanza dei dati

SQL Data Warehouse archivia tutti i dati nell’archiviazione Premium di Azure [con ridondanza locale](../storage/storage-redundancy.md), conservando 3 copie dei dati.

### Ripristino del database

La funzionalità di ripristino del database è stata progettata per ripristinare il database da un punto precedente nel tempo. Il servizio Azure SQL Data Warehouse protegge tutti i database eseguendo snapshot di archiviazione automatica almeno ogni 8 ore e li conserva per 7 giorni in modo da fornire un set discreto di punti di ripristino. Le funzionalità di snapshot e ripristino automatici consentono di proteggere i database da un danneggiamento o un'eliminazione accidentale a costo zero. Per altre informazioni sul ripristino di database, vedere l’articolo [Attività di ripristino del database][].

## Passaggi successivi
Per altre importanti attività di gestione, vedere [Panoramica sulla gestione][].

<!--Image references-->

<!--Article references-->
[Azure storage redundancy options]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[Panoramica sulla gestione]: sql-data-warehouse-overview-management.md
[Attività di ripristino del database]: sql-data-warehouse-manage-database-restore-portal.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->