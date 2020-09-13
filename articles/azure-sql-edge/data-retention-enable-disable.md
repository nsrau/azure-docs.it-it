---
title: Abilitare e disabilitare i criteri di conservazione dei dati-Azure SQL Edge (anteprima)
description: Informazioni su come abilitare e disabilitare i criteri di conservazione dei dati in Azure SQL Edge (anteprima)
keywords: SQL Edge, conservazione dei dati
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 9787f2cfa87a16d9e7dd1753e4389977c6753b81
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89550664"
---
# <a name="enable-and-disable-data-retention-policies"></a>Abilitare e disabilitare i criteri di conservazione dei dati

In questo argomento viene descritto come abilitare e disabilitare i criteri di conservazione dei dati per un database e una tabella. 

## <a name="enable-data-retention-for-a-database"></a>Abilitare la conservazione dei dati per un database

Nell'esempio seguente viene illustrato come abilitare la conservazione dei dati utilizzando [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options).

> [!NOTE]
> Per abilitare la funzionalità di conservazione dei dati in Azure SQL Edge (anteprima), abilitare TF 12825 come opzione di avvio oppure usare il comando DBCC TRACEON. Per altre informazioni su come abilitare i flag di traccia usando un file MSSQL. conf, vedere [configurare usando un file MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file). 

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Controllare se la conservazione dei dati è abilitata per un database

Il comando seguente può essere usato per verificare se la conservazione dei dati è abilitata per un database
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Abilitare la conservazione dei dati per una tabella

La conservazione dei dati deve essere abilitata per ogni tabella per cui si desidera che i dati vengano eliminati automaticamente. Quando la conservazione dei dati è abilitata per il database e la tabella, un'attività di sistema in background analizzerà periodicamente la tabella per identificare ed eliminare eventuali righe obsolete (obsolete). La conservazione dei dati può essere abilitata in una tabella durante la creazione della tabella tramite [Create Table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) o [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

Nell'esempio seguente viene illustrato come abilitare la conservazione dei dati per una tabella tramite [Create Table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql). 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

La `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` parte del comando Create Table consente di impostare la conservazione dei dati nella tabella. Il comando usa i parametri obbligatori seguenti 

- DATA_DELETION: indica se la conservazione dei dati è attiva o disattivata.
- Nome FILTER_COLUMN nella colonna della tabella, che verrà utilizzata per verificare se le righe sono obsolete o meno. La colonna filtro può essere solo una colonna con i tipi di dati seguenti 
    - Data
    - SmallDateTime
    - Datetime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD: valore integer seguito da un descrittore di unità. Le unità consentite sono giorno, settimana, mese e anno.

Nell'esempio seguente viene illustrato come abilitare la conservazione dei dati per la tabella utilizzando [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Controllare se la conservazione dei dati è abilitata per una tabella

Il comando seguente può essere usato per controllare le tabelle per cui è abilitata la conservazione dei dati

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Il valore data_retention_period =-1 e data_retention_period_unit come infinito indica che la conservazione dei dati non è impostata nella tabella.

È possibile utilizzare la query seguente per identificare la colonna utilizzata come filter_column per la conservazione dei dati. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="corelating-db-and-table-data-retention-settings"></a>Correlazione delle impostazioni di conservazione dei dati di database e tabelle

Le impostazioni di conservazione dei dati nel database e nella tabella vengono utilizzate insieme per determinare se la pulizia automatica per le righe obsolete verrà eseguita sulle tabelle. 

|Opzione di database | Opzione table | Comportamento |
|----------------|--------------|----------|
| OFF | OFF | I criteri di conservazione dei dati sono disabilitati e la pulizia automatica e manuale dei record obsoleti è disabilitata.|
| OFF | ON  | I criteri di conservazione dei dati sono abilitati per la tabella, tuttavia la pulizia automatica e manuale dei record obsoleti è disabilitata. |
| ON | OFF | I criteri di conservazione dei dati sono abilitati a livello di database. Tuttavia, poiché l'opzione è disabilitata a livello di tabella, non esiste alcuna pulizia basata sulla conservazione delle righe obsolete.|
| ON | ON | I criteri di conservazione dei dati sono abilitati per il database e le tabelle. Pulizia automatica/manuale dei record obsoleti abilitata |

## <a name="disable-data-retention-on-a-table"></a>Disabilitare la conservazione dei dati in una tabella 

La conservazione dei dati può essere disabilitata in una tabella tramite [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql). Il comando seguente può essere usato per disabilitare la conservazione dei dati in una tabella.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Disabilitare la conservazione dei dati in un database

La conservazione dei dati può essere disabilitata in una tabella tramite [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options). Il comando seguente può essere usato per disabilitare la conservazione dei dati in un database.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Passaggi successivi
- [Conservazione dei dati e eliminazione automatica dei dati](data-retention-overview.md)
- [Gestire i dati cronologici con i criteri di conservazione](data-retention-cleanup.md)
