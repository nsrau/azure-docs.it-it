---
title: 'Esercitazione: Connettere SQL su richiesta (anteprima) a Power BI Desktop e creare report'
description: Questa esercitazione illustra come connettere SQL su richiesta (anteprima) in Azure Synapse Analytics a Power BI Desktop e creare un report demo in base a una visualizzazione.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1bdf2d0e3613af7eec339194d6d8a446be83f365
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692401"
---
# <a name="tutorial-use-sql-on-demand-preview-with-power-bi-desktop--create-a-report"></a>Esercitazione: Usare SQL su richiesta (anteprima) con Power BI Desktop e creare un report

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Creare un database demo
> - Creare la visualizzazione usata per il report
> - Connettere Power BI Desktop a SQL su richiesta
> - Creare report in base alla visualizzazione

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario il software seguente:

- Strumento di query SQL, ad esempio [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) oppure [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
- [Power BI Desktop](https://powerbi.microsoft.com/downloads/).

Valori per i parametri seguenti:

| Parametro                                 | Descrizione                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Indirizzo dell'endpoint di servizio di SQL su richiesta    | Usato come nome del server                                   |
| Area dell'endpoint servizio di SQL su richiesta     | Usato per determinare la risorsa di archiviazione usata negli esempi |
| Nome utente e password per l'accesso all'endpoint | Usati per accedere all'endpoint                               |
| Database che verrà usato per creare le visualizzazioni     | Database usato come punto di partenza negli esempi       |

## <a name="1---create-database"></a>1 - Creare il database

Per l'ambiente demo, creare un database demo personalizzato. Questo database verrà usato per visualizzare i metadati, non per archiviare i dati effettivi.

Creare il database demo ed eliminare un database esistente, se necessario, eseguendo lo script Transact-SQL (T-SQL) seguente:

```sql
-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.databases WHERE name = 'Demo')
BEGIN
    DROP DATABASE Demo
END;
GO

-- Create new database
CREATE DATABASE [Demo];
GO
```

## <a name="2---create-credential"></a>2 - Creare le credenziali

Il servizio SQL su richiesta necessita delle credenziali per accedere ai file nella risorsa di archiviazione. Creare le credenziali per un account di archiviazione che si trova nella stessa area dell'endpoint. Sebbene SQL su richiesta possa accedere agli account di archiviazione di aree diverse, l'account di archiviazione e l'endpoint nella stessa area garantiranno prestazioni migliori.

Creare le credenziali eseguendo lo script Transact-SQL (T-SQL) seguente:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = '';
GO
```

## <a name="3---prepare-view"></a>3 - Preparare la visualizzazione

Creare la visualizzazione in base ai dati demo esterni per l'uso in Power BI eseguendo lo script Transact-SQL (T-SQL) seguente:

Creare la visualizzazione `usPopulationView` all'interno del database `Demo` con la query seguente:

```sql
DROP VIEW IF EXISTS usPopulationView;
GO

CREATE VIEW usPopulationView AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS uspv;
```

I dati demo contengono i set di dati seguenti:

Popolazione degli Stati Uniti per sesso e razza per ogni contea degli Stati Uniti derivante dal censimento decennale 2000-2010 in formato Parquet.

| Percorso della cartella                                                  | Descrizione                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /release/                                                    | Cartella padre per i dati nell'account di archiviazione demo               |
| /release/us_population_county/                               | File di dati della popolazione degli Stati Uniti in formato Parquet, partizionati per anno mediante lo schema di partizionamento Hive/Hadoop. |

## <a name="4---create-power-bi-report"></a>4 - Creare il report di Power BI

Per creare il report per Power BI Desktop, seguire questa procedura:

1. Aprire l'applicazione Power BI Desktop e selezionare **Recupera dati**.

   ![Aprire l'applicazione Power BI Desktop e selezionare Recupera dati.](./media/tutorial-connect-power-bi-desktop/step-0-open-powerbi.png)

2. Selezionare **Azure** > **Database SQL di Azure**. 

   ![Selezionare l'origine dati.](./media/tutorial-connect-power-bi-desktop/step-1-select-data-source.png)

3. Digitare il nome del server in cui si trova il database nel campo **Server**, quindi digitare `Demo` nel nome del database. Selezionare l'opzione **Importa** e quindi scegliere **OK**. 

   ![Selezionare il database nell'endpoint.](./media/tutorial-connect-power-bi-desktop/step-2-db.png)

4. Selezionare il metodo di autenticazione preferito:

    - Esempio per AAD 
  
        ![Fare clic su Accedi.](./media/tutorial-connect-power-bi-desktop/step-2.1-select-aad-auth.png)

    - Esempio per l'accesso SQL - Digitare il nome utente e la password.

        ![Usare l'accesso SQL.](./media/tutorial-connect-power-bi-desktop/step-2.2-select-sql-auth.png)


5. Selezionare la visualizzazione `usPopulationView` e quindi selezionare **Carica**. 

   ![Selezionare una visualizzazione nel database selezionato.](./media/tutorial-connect-power-bi-desktop/step-3-select-view.png)

6. Attendere il completamento dell'operazione. Verrà visualizzata una finestra popup con il messaggio `There are pending changes in your queries that haven't been applied`. Fare clic su **Applica modifiche**. 

   ![Fare clic su Applica modifiche.](./media/tutorial-connect-power-bi-desktop/step-4-apply-changes.png)

7. Attendere che la finestra di dialogo **Applica modifiche alla query** venga chiusa. L'operazione potrebbe richiedere alcuni minuti. 

   ![Attendere il completamento della query.](./media/tutorial-connect-power-bi-desktop/step-5-wait-for-query-to-finish.png)

8. Al termine del caricamento, selezionare le colonne seguenti nell'ordine specificato per creare il report:
   - countyName
   - popolamento
   - stateName

   ![Selezionare le colonne di interesse per generare un report mappa.](./media/tutorial-connect-power-bi-desktop/step-6-select-columns-of-interest.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver usato il report, seguire questa procedura per eliminare le risorse:

1. Eliminare le credenziali per l'account di archiviazione

   ```sql
   DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
   ```

2. Eliminare la visualizzazione

   ```sql
   DROP VIEW usPopulationView;
   ```

3. Eliminare il database

   ```sql
   DROP DATABASE Demo;
   ```

## <a name="next-steps"></a>Passaggi successivi

Passare all'argomento [Eseguire query sui file di archiviazione](develop-storage-files-overview.md) per informazioni su come eseguire query sui file di archiviazione usando Synapse SQL.
