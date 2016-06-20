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
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Disabilitare Database Estensione e recuperare i dati remoti

Per disabilitare Database Estensione per una tabella, selezionare **Estensione** per una tabella in SQL Server Management Studio. Selezionare quindi una delle opzioni seguenti.

-   **Disabilita | Recupera i dati da Azure**. Copiare i dati remoti per la tabella da Azure a SQL Server, quindi disabilitare Database Estensione per la tabella. Questa operazione comporta costi di trasferimento dati e non può essere annullata.

-   **Disabilita | Lascia i dati in Azure**. Disabilitare Database Estensione per la tabella. Abbandonare i dati remoti per la tabella in Azure.

È possibile usare anche Transact-SQL per disabilitare Database Estensione per una tabella o per un database.

Dopo aver disabilitato Database Estensione per una tabella, la migrazione di dati viene interrotta e i risultati della query non includono più i risultati della tabella remota.

Se si desidera sospendere la migrazione dei dati, vedere l'articolo su come [sospendere e riprendere Database Estensione](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] La disabilitazione del database di estensione per una tabella o per un database non elimina l'oggetto remoto. Per eliminare la tabella o il database remoto è necessario rimuoverli usando il portale di gestione di Azure. Gli oggetti remoti continuano a comportare costi di archiviazione di Azure fino a quando non vengono eliminati. Per ulteriori informazioni, vedere [Prezzi dei database di estensione di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Disabilitare Database Estensione per una tabella

### Con SQL Server Management Studio disabilitare Database Estensione per una tabella

1.  In SQL Server Management Studio, in Esplora oggetti, selezionare la tabella per cui si desidera disabilitare Database Estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Stretch**, quindi selezionare una delle opzioni seguenti.

    -   **Disabilita | Ripristina dati da Azure**. Copiare i dati remoti per la tabella da Azure a SQL Server, quindi disabilitare Database Estensione per la tabella. Questo comando non può essere annullato.

        >   [AZURE.NOTE] La copia dei dati remoti per la tabella da Azure a SQL Server comporta costi di trasferimento dati. Per altre informazioni, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

        Dopo aver copiato tutti i dati remoti da Azure a SQL Server, l'estensione per la tabella risulta disabilitata.

    -   **Disabilita | Lascia dati in Azure**. Disabilitare Database Estensione per la tabella. Abbandonare i dati remoti per la tabella in Azure.

    >   [AZURE.NOTE] La disabilitazione del database di estensione per una tabella non elimina i dati remoti o la tabella remota. Per eliminare la tabella remota è necessario rimuoverla usando il portale di gestione di Azure. La tabella remota continua a comportare costi di archiviazione di Azure fino alla sua eliminazione. Per ulteriori informazioni, vedere [Prezzi dei database di estensione di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Con Transact-SQL disabilitare Database Estensione per una tabella

-   Per disabilitare l'estensione per una tabella e copiare i dati remoti per la tabella da Azure a SQL Server, eseguire il comando seguente. Questo comando non può essere annullato.

    ```tsql
    ALTER TABLE <table name>
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    ```
    >   [AZURE.NOTE] La copia dei dati remoti per la tabella da Azure a SQL Server comporta costi di trasferimento dati. Per altre informazioni, vedere [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

    Dopo aver copiato tutti i dati remoti da Azure a SQL Server, l'estensione per la tabella risulta disabilitata.

-   Per disabilitare l'estensione per una tabella e abbandonare i dati remoti, eseguire il comando seguente.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] La disabilitazione del database di estensione per una tabella non elimina i dati remoti o la tabella remota. Per eliminare la tabella remota è necessario rimuoverla usando il portale di gestione di Azure. La tabella remota continua a comportare costi di archiviazione di Azure fino alla sua eliminazione. Per ulteriori informazioni, vedere [Prezzi dei database di estensione di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Disabilitare Database Estensione per un database
Prima di disabilitare Database Estensione per un database, è necessario disabilitare Database Estensione su singole tabelle con estensione abilitata nel database.

### Con SQL Server Management Studio disabilitare Database Estensione per un database

1.  In SQL Server Management Studio, in Esplora oggetti, selezionare il database per cui si desidera disabilitare Database Estensione.

2.  Fare clic con il pulsante destro del mouse e selezionare **Attività**, quindi **Stretch** e **Disabilita**.

>   [AZURE.NOTE] La disabilitazione del database di estensione per un database non elimina il database remoto. Per eliminare il database remoto è necessario rimuoverlo usando il portale di gestione di Azure. Il database remoto continua a comportare costi di archiviazione di Azure fino alla sua eliminazione. Per ulteriori informazioni, vedere [Prezzi dei database di estensione di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Con Transact-SQL disabilitare Database Estensione per un database
Eseguire il comando indicato di seguito.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] La disabilitazione del database di estensione per un database non elimina il database remoto. Per eliminare il database remoto è necessario rimuoverlo usando il portale di gestione di Azure. Il database remoto continua a comportare costi di archiviazione di Azure fino alla sua eliminazione. Per ulteriori informazioni, vedere [Prezzi dei database di estensione di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Vedere anche

[Opzioni ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Sospendere e riprendere Database Estensione](sql-server-stretch-database-pause.md)

<!---HONumber=AcomDC_0608_2016-->