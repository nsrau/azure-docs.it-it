---
title: Esportare un database SQL di Azure in un file BACPAC | Documentazione Microsoft
description: Esportare un database SQL di Azure in un file BACPAC usando il portale di Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2461f706f8fc1150e69312098640c0676206a531
ms.openlocfilehash: 4edd728fe4798450106a4991b353b9dac9de0d88
ms.lasthandoff: 02/17/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>Esportare un database SQL di Azure o un database SQL Server in un file BACPAC

Questo articolo illustra l'esportazione del database SQL di Azure o di un database SQL Server in un file BACPAC. 

> [!IMPORTANT]
> La funzionalità di esportazione automatizzata di database SQL di Azure, ora disponibile in anteprima, verrà ritirata il 1° marzo 2017. A partire dal 1° dicembre 2016, non è più possibile configurare l'esportazione automatizzata per database SQL. Tutti i processi di esportazione automatizzata esistenti continueranno a funzionare fino al 1° marzo 2017. Dal 1° dicembre 2016, è possibile usare la [conservazione dei backup a lungo termine](sql-database-long-term-retention.md) o [Automazione di Azure](../automation/automation-intro.md) per archiviare periodicamente i database SQL con PowerShell in base alla pianificazione desiderata. È possibile scaricare uno [script di esempio da GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 
>

## <a name="overview"></a>Panoramica

Quando è necessario esportare un database per l'archiviazione o lo spostamento in un'altra piattaforma, è possibile esportare lo schema di database e i dati in un file BACPAC. Un file BACPAC è semplicemente un file zip con estensione bacpac. Un file BACPAC in un secondo momento può essere archiviato nell'archiviazione BLOB di Azure o in un percorso locale e successivamente reimportato nel database SQL di Azure o in un'installazione locale di SQL Server. 

* È possibile esportare il database SQL di Azure usando il [portale di Azure](sql-database-export-portal.md), [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) o [SQL Server Management Studio](sql-database-export-ssms.md).
* È possibile esportare un database SQL Server usando [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) o [SQL Server Management Studio](sql-database-export-ssms.md).

> [!IMPORTANT]
> Se si sta eseguendo l'esportazione da SQL Server come preparazione alla migrazione nel database SQL di Azure, vedere [Migrare un database SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).
> 

## <a name="considerations"></a>Considerazioni

* Perché un'esportazione sia coerente dal punto di vista transazionale, è necessario assicurarsi che non si verifichino attività di scrittura durante l'esportazione o che l'esportazione sia eseguita da una [copia coerente dal punto di vista transazionale](sql-database-copy.md) del database SQL di Azure.
* Se si sta eseguendo l'esportazione nell'archiviazione BLOB, la dimensione massima di un file BACPAC è 200 GB. Per archiviare un file BACPAC di dimensioni maggiori, eseguire l'esportazione in una risorsa di archiviazione locale.
* L'esportazione di un file BACPAC in Archiviazione Premium di Azure usando i metodi descritti in questo articolo non è supportata.
* Se l'operazione di esportazione da un database SQL di Azure dura oltre 20 ore, potrebbe essere annullata. Per migliorare le prestazioni durante l'esportazione è possibile:
  * Aumentare temporaneamente il livello di servizio.
  * Interrompere tutte le attività di lettura e scrittura durante l'esportazione.
  * Utilizzare un [indice cluster](https://msdn.microsoft.com/library/ms190457.aspx) con valori non null in tutte le tabelle di grandi dimensioni. Senza indici cluster, l'esportazione potrebbe non riuscire se dovesse durare più di 6 - 12 ore. Questo perché i servizi di esportazione devono completare la scansione della tabella prima di provare a esportarla per intero. Un modo valido di determinare se le tabelle sono ottimizzate per l'esportazione consiste nell'eseguire **DBCC SHOW_STATISTICS** e verificare che il parametro *RANGE_HI_KEY* non sia null e il relativo valore abbia distribuzione valida. Per i dettagli, vedere [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> I BACPAC non sono destinati a essere utilizzati per il backup e le operazioni di ripristino. Il database SQL di Azure crea automaticamente i backup per ogni database dell’utente. Per altre informazioni, vedere [Panoramica sulla continuità aziendale](sql-database-business-continuity.md) e [Backup del database SQL](sql-database-automated-backups.md).  
> 


## <a name="next-steps"></a>Passaggi successivi

* Per una descrizione dell'intero processo di migrazione del database del database SQL Server, vedere [Migrare un database di SQL Server nel database SQL di Azure](sql-database-cloud-migrate.md).
* Per una panoramica su come copiare un database in Azure, vedere [Copiare un database SQL di Azure](sql-database-copy.md).
* È possibile copiare il database SQL di Azure nell'ambiente Azure usando il [portale di Azure](sql-database-copy-portal.md), [PowerShell](sql-database-copy-powershell.md) o [Transact-SQL](sql-database-copy-transact-sql.md). 

