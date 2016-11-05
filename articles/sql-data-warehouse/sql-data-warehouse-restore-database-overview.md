---
title: Ripristinare un'istanza di Azure SQL Data Warehouse (panoramica) | Microsoft Docs
description: Panoramica delle opzioni di ripristino del database per ripristinare un database in Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/28/2016
ms.author: lakshmir;barbkess;sonyama

---
# Ripristinare un'istanza di Azure SQL Data Warehouse (panoramica)
> [!div class="op_single_selector"]
> * [Panoramica][Panoramica]
> * [Portale][Portale]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Azure SQL Data Warehouse protegge i dati tramite l'archiviazione con ridondanza locale e i backup automatici. I backup automatici consentono di proteggere i database dal danneggiamento o dall'eliminazione accidentale senza l'intervento dell'amministratore. Nel caso in cui un utente modifichi o elimini dati inavvertitamente o accidentalmente, è possibile garantire la continuità aziendale ripristinando il database da un momento precedente nel tempo. SQL Data Warehouse usa snapshot di Archiviazione di Azure per eseguire facilmente il backup del database senza richiedere tempi di inattività.

## Backup automatizzati
Il backup del database **attivo** verrà eseguito automaticamente con un intervallo minimo di ogni 8 ore e conservato per 7 giorni. Ciò consente di ripristinare il database attivo a uno dei diversi punti di ripristino degli ultimi 7 giorni.

Quando un database viene sospeso, i nuovi backup vengono arrestati e quelli precedenti vengono mantenuti fino alla scadenza di 7 giorni. Se un database viene sospeso per più di 7 giorni, verrà salvato il backup più recente per garantire di avere sempre almeno un backup.

Quando un database viene eliminato, l'ultimo backup viene conservato per 7 giorni.

Eseguire la query sull'istanza di SQL Data Warehouse attiva per vedere quando è stato eseguito l'ultimo backup:

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Se è necessario conservare una copia di backup per più di 7 giorni, è possibile ripristinare semplicemente uno dei punti di ripristino in un nuovo database e quindi sospendere facoltativamente il database, in modo da pagare solo per lo spazio di archiviazione di quel backup.

## Ridondanza dei dati
Oltre ai backup, SQL Data Warehouse protegge i dati anche con l'archiviazione Premium di Azure [con ridondanza locale][con ridondanza locale]. Nel data center locale vengono mantenute più copie sincrone dei dati per garantire una protezione trasparente degli stessi in caso di problemi localizzati. La ridondanza dei dati garantisce che i dati non subiscano l'impatto di problemi dell'infrastruttura, ad esempio errori del disco e così via. La ridondanza dei dati assicura la continuità aziendale con un'infrastruttura a tolleranza di errore e disponibilità elevata.

## Per ripristinare un database
Il ripristino di SQL Data Warehouse è un'operazione semplice che può essere eseguita nel portale di Azure o automatizzata tramite PowerShell o le API REST.

## Passaggi successivi
Per altre informazioni sulle funzionalità di continuità aziendale delle edizioni del database SQL di Azure, vedere [Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL][Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL].

<!--Image references-->

<!--Article references-->
[Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL]: ./sql-database-business-continuity.md
[con ridondanza locale]: ../storage/storage-redundancy.md
[Panoramica]: ./sql-data-warehouse-restore-database-overview.md
[Portale]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0824_2016-->