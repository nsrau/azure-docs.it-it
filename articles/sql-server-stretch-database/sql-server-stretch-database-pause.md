---
title: Sospendere e riprendere la migrazione dei dati (Estensione database) | Documentazione Microsoft
description: Informazioni su come sospendere o riprendere la migrazione dei dati in Azure.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>Sospendere e riprendere la migrazione dei dati (Database Estensione)
Per sospendere o riprendere la migrazione dei dati in Azure, selezionare **Estensione** per una tabella in SQL Server Management Studio e quindi selezionare **Sospendi** per sospendere la migrazione dei dati o **Riprendi** per riprendere la migrazione. Per sospendere o riprendere la migrazione dei dati è anche possibile usare Transact\-SQL.

Sospendere la migrazione dei dati nelle singole tabelle per risolvere i problemi nel server locale o per ottimizzare la larghezza di banda di rete disponibile.

## <a name="pause-data-migration"></a>Sospendere la migrazione dei dati
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Usare SQL Server Management Studio per sospendere la migrazione dei dati
1. In SQL Server Management Studio, in Esplora oggetti, selezionare la tabella abilitata per l'estensione per la quale si vuole sospendere la migrazione dei dati.
2. Fare clic con il pulsante destro del mouse e scegliere **Estensione**, quindi **Sospendi**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Usare Transact\-SQL per sospendere la migrazione dei dati
Eseguire il comando indicato di seguito.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Riprendere la migrazione dei dati
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Usare SQL Server Management Studio per riprendere la migrazione dei dati
1. In SQL Server Management Studio, in Esplora oggetti, selezionare la tabella abilitata per l'estensione per la quale si vuole riprendere la migrazione dei dati.
2. Fare clic con il pulsante destro del mouse e scegliere **Estensione**, quindi **Riprendi**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Usare Transact\-SQL per riprendere la migrazione dei dati
Eseguire il comando indicato di seguito.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Controllare se la migrazione è attiva o in sospeso
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Utilizzare SQL Server Management Studio per controllare se la migrazione è attiva o in sospeso
In SQL Server Management Studio aprire **Monitoraggio estensione database** e controllare il valore della colonna **Stato della migrazione**. Per altre informazioni, vedere l'articolo relativo al [monitoraggio e risoluzione dei problemi di migrazione dei dati](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Usare Transact-SQL per controllare se la migrazione è attiva o in sospeso
Eseguire una query sulla vista del catalogo **sys.remote_data_archive_tables** e controllare il valore della colonna **is_migration_paused**. Per altre informazioni, vedere [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Vedere anche
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Monitorare e risolvere i problemi di migrazione dei dati](sql-server-stretch-database-monitor.md)




<!--HONumber=Nov16_HO3-->


