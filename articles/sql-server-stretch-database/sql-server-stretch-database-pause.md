<properties
	pageTitle="Sospendere e riprendere la migrazione dei dati (Database Estensione) | Microsoft Azure"
	description="Informazioni su come sospendere o riprendere la migrazione dei dati in Azure."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Sospendere e riprendere la migrazione dei dati (Database Estensione)

Per sospendere o riprendere la migrazione dei dati in Azure, selezionare **Estensione** per una tabella in SQL Server Management Studio e quindi selezionare **Sospendi** per sospendere la migrazione dei dati o **Riprendi** per riprendere la migrazione. Per sospendere o riprendere la migrazione dei dati è anche possibile usare Transact-SQL.

Sospendere la migrazione dei dati nelle singole tabelle per risolvere i problemi nel server locale o per ottimizzare la larghezza di banda di rete disponibile.

## Sospendere la migrazione dei dati

### Usare SQL Server Management Studio per sospendere la migrazione dei dati

1.  In SQL Server Management Studio in Esplora oggetti selezionare la tabella abilitata per l'estensione la quale si desidera sospendere la migrazione dei dati.

2.  Fare clic con il pulsante destro del mouse e selezionare **Estensione**, quindi **Sospendi**.

### Usare Transact-SQL per sospendere la migrazione dei dati
Eseguire il comando indicato di seguito.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## Riprendere la migrazione dei dati

### Usare SQL Server Management Studio per riprendere la migrazione dei dati

1.  In SQL Server Management Studio in Esplora oggetti selezionare la tabella abilitata per l'estensione la quale si desidera riprendere la migrazione dei dati.

2.  Fare clic con il pulsante destro del mouse e selezionare **Estensione**, quindi **Riprendi**.

### Usare Transact-SQL per riprendere la migrazione dei dati
Eseguire il comando indicato di seguito.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## Controllare se la migrazione è attiva o in sospeso

### Utilizzare SQL Server Management Studio per controllare se la migrazione è attiva o in sospeso
In SQL Server Management Studio aprire **Monitoraggio estensione database** e controllare il valore della colonna **Stato della migrazione**. Per altre informazioni, vedere l'articolo relativo al [monitoraggio e risoluzione dei problemi di migrazione dei dati](sql-server-stretch-database-monitor.md).

### Usare Transact-SQL per controllare se la migrazione è attiva o in sospeso
Eseguire una query sulla vista del catalogo **sys.remote\_data\_archive\_tables** e controllare il valore della colonna **is\_migration\_paused**. Per altre informazioni, vedere [sys.remote\_data\_archive\_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## Vedere anche

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx) [Monitoraggio e risoluzione dei problemi di migrazione dei dati](sql-server-stretch-database-monitor.md)

<!---HONumber=AcomDC_0622_2016-->