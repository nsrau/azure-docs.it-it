---
title: Ridimensionare le risorse di calcolo in Azure Synapse Analytics - T-SQL
description: Ridimensionare le risorse di calcolo in Azure Synapse Analytics tramite T-SQL e SQL Server Management Studio (SSMS), aumentandone il numero per ottenere prestazioni migliori o riducendolo per diminuire i costi.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 780137c8e081917b317656de3caba60dfaea4810
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633736"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-using-t-sql"></a>Guida introduttiva: Ridimensionare le risorse di calcolo in Azure Synapse Analytics con T-SQL

Ridimensionare le risorse di calcolo in Azure Synapse Analytics (in precedenza SQL DW) tramite T-SQL e SQL Server Management Studio (SSMS), [aumentandone il numero](sql-data-warehouse-manage-compute-overview.md) per ottenere prestazioni migliori o riducendolo per diminuire i costi.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Scaricare e installare la versione più recente di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS).

## <a name="create-a-data-warehouse"></a>Creare un data warehouse

Fare riferimento a [Guida introduttiva: Creare e connettere - portale](create-data-warehouse-portal.md) per creare un data warehouse denominato **mySampleDataWarehouse**. Completare la guida di avvio rapido per verificare di avere una regola del firewall e di essere in grado di connettersi al data warehouse da SQL Server Management Studio.

## <a name="connect-to-the-server-as-server-admin"></a>Connettersi al server come amministratore del server

In questa sezione si usa [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) per stabilire una connessione al server SQL di Azure.

1. Aprire SQL Server Management Studio.

2. Immettere le informazioni seguenti nella finestra di dialogo **Connetti al server**:

   | Impostazione       | Valore consigliato | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- |
   | Tipo di server | Motore di database | Questo valore è obbligatorio |
   | Nome server | Nome completo del server | Ecco un esempio: **mySampleDataWarehouseservername.database.windows.net**. |
   | Authentication | Autenticazione di SQL Server | L'autenticazione SQL è il solo tipo di autenticazione configurato in questa esercitazione. |
   | Login | Account amministratore del server | Account specificato quando è stato creato il server. |
   | Password | Password per l'account amministratore del server | La password specificata quando è stato creato il server. |

    ![Connettersi al server](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Fare clic su **Connetti**. In SSMS si apre la finestra Esplora oggetti.

4. In Esplora oggetti espandere **Database**. Espandere quindi **mySampleDataWarehouse** per visualizzare gli oggetti nel nuovo database.

    ![Oggetti di database](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Visualizzare l'obiettivo di servizio

L'impostazione relativa all'obiettivo di servizio contiene il numero di unità Data Warehouse per il data warehouse.

Per visualizzare le unità Data Warehouse correnti per il data warehouse:

1. Nella connessione a **mySampleDataWarehouseservername.database.windows.net** espandere **Database di sistema**.
2. Fare clic con il pulsante destro del mouse su **Master** e selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
3. Eseguire la query seguente per effettuare una selezione nella vista a gestione dinamica sys.database_service_objectives.

    ```sql
    SELECT
        db.name [Database]
    ,    ds.edition [Edition]
    ,    ds.service_objective [Service Objective]
    FROM
         sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE
        db.name = 'mySampleDataWarehouse'
    ```

4. I risultati seguenti mostrano che **mySampleDataWarehouse** ha un obiettivo di servizio di DW400.

    ![iew-current-dwu](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>Ridimensionare le risorse di calcolo

In Azure Synapse è possibile aumentare o ridurre le risorse di calcolo agendo sulle unità di data warehouse. Nella pagina [Creare e connettere - portale](create-data-warehouse-portal.md)**mySampleDataWarehouse** è stato creato e inizializzato con 400 unità Data Warehouse. La procedura seguente modifica le unità Data Warehouse per **mySampleDataWarehouse**.

Per modificare le unità Data Warehouse:

1. Fare clic con il pulsante destro del mouse su **Master** e selezionare **Nuova query**.
2. Usare l'istruzione T-SQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) per modificare l'obiettivo di servizio. Eseguire la query seguente per modificare l'obiettivo di servizio impostandolo su DW300.

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Monitorare la richiesta di ridimensionamento

Per visualizzare lo stato di avanzamento della richiesta di modifica precedente, è possibile usare la sintassi T-SQL `WAITFORDELAY` per eseguire il poll della DMV sys.dm_operation_status.

Per eseguire il poll dello stato di modifica dell'oggetto servizio:

1. Fare clic con il pulsante destro del mouse su **Master** e selezionare **Nuova query**.
2. Eseguire la query seguente per eseguire il poll del DMV sys.dm_operation_status.

    ```sql
    WHILE
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```

3. L'output risultante mostra il log del polling dello stato.

    ![Stato dell'operazione](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-data-warehouse-state"></a>Controllare lo stato del data warehouse

Se un data warehouse è sospeso, non è possibile eseguire la connessione con T-SQL. Per visualizzare lo stato corrente del data warehouse, usare un cmdlet PowerShell. Per un esempio, vedere [Controllare lo stato del data warehouse - Powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state).

## <a name="check-operation-status"></a>Controllare lo stato dell'operazione

Per restituire informazioni sulle varie operazioni di gestione in Azure Synapse, eseguire la query seguente nella DMV [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). La query restituirà, ad esempio, l'operazione e lo stato dell'operazione, che sarà IN_PROGRESS o COMPLETED.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND
    major_resource_id = 'mySampleDataWarehouse'
```

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come ridimensionare le risorse di calcolo per il data warehouse. Per altre informazioni su Azure Synapse, continuare con l'esercitazione sul caricamento dei dati.

> [!div class="nextstepaction"]
>[Caricare i dati in Azure Synapse Analytics](load-data-from-azure-blob-storage-using-polybase.md)
