<properties
	pageTitle="Disabilitare Database Estensione e recuperare i dati remoti | Microsoft Azure"
	description="Scoprire come disabilitare Database Estensione per una tabella e scegliere di recuperare i dati remoti."
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
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Disabilitare Database Estensione e recuperare i dati remoti

Per disabilitare Database Estensione per una tabella, selezionare **Estensione** per una tabella in SQL Server Management Studio. Selezionare quindi una delle opzioni seguenti.

-   **Disabilita | Recupera i dati da Azure**. Copiare i dati remoti per la tabella da Azure a SQL Server, quindi disabilitare Database Estensione per la tabella. Questa operazione comporta costi di trasferimento dati e non può essere annullata.

-   **Disabilita | Lascia i dati in Azure**. Disabilitare Database Estensione per la tabella. Abbandonare i dati remoti per la tabella in Azure.

Dopo aver disabilitato Database Estensione per una tabella, la migrazione di dati viene interrotta e i risultati della query non includono più i risultati della tabella remota.

È possibile usare anche Transact-SQL per disabilitare Database Estensione per una tabella o per un database.

Se si desidera sospendere la migrazione dei dati, vedere l'articolo su come [sospendere e riprendere Database Estensione](sql-server-stretch-database-pause.md).

**Nota:** la disabilitazione dell'estensione non rimuove gli oggetti remoti. Per eliminare la tabella o il database remoto è necessario rimuoverli usando il portale di gestione di Azure.

## Disabilitare Database Estensione per una tabella

### Con SQL Server Management Studio disabilitare Database Estensione per una tabella

1.  In SQL Server Management Studio, in Esplora oggetti, selezionare la tabella per cui si desidera disabilitare Database Estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Stretch**, quindi selezionare una delle opzioni seguenti.

    -   **Disabilita | Ripristina dati da Azure**. Copiare i dati remoti per la tabella da Azure a SQL Server, quindi disabilitare Database Estensione per la tabella. Questo comando non può essere annullato.

        Questa operazione comporta costi di trasferimento dati. Per altre informazioni, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

        Dopo aver copiato tutti i dati remoti da Azure a SQL Server, l'estensione per la tabella risulta disabilitata.

    -   **Disabilita | Lascia dati in Azure**. Disabilitare Database Estensione per la tabella. Abbandonare i dati remoti per la tabella in Azure.

    La disabilitazione dell'estensione non rimuove la tabella remota. Per eliminare la tabella remota è necessario rimuoverla usando il portale di gestione di Azure.

### Con Transact-SQL disabilitare Database Estensione per una tabella

-   Per disabilitare l'estensione per una tabella e copiare i dati remoti per la tabella da Azure a SQL Server, eseguire il comando seguente. Questo comando non può essere annullato.

    ```tsql
    ALTER TABLE <table name>
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    ```
    Questa operazione comporta costi di trasferimento dati. Per altre informazioni, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

    Dopo aver copiato tutti i dati remoti da Azure a SQL Server, l'estensione per la tabella risulta disabilitata.

-   Per disabilitare l'estensione per una tabella e abbandonare i dati remoti, eseguire il comando seguente.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```
La disabilitazione dell'estensione non rimuove la tabella remota. Per eliminare la tabella remota è necessario rimuoverla usando il portale di gestione di Azure.

## Disabilitare Database Estensione per un database
Prima di disabilitare Database Estensione per un database, è necessario disabilitare Database Estensione su singole tabelle con estensione abilitata nel database.

La disabilitazione dell'estensione non rimuove il database remoto. Per eliminare il database remoto è necessario rimuoverlo usando il portale di gestione di Azure.

### Con SQL Server Management Studio disabilitare Database Estensione per un database

1.  In SQL Server Management Studio, in Esplora oggetti, selezionare il database per cui si desidera disabilitare Database Estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Attività**, quindi **Stretch** e **Disabilita**.

### Con Transact-SQL disabilitare Database Estensione per un database
Eseguire il comando indicato di seguito.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

## Vedere anche

[Opzioni ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Sospendere e riprendere Database Estensione](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0316_2016-->