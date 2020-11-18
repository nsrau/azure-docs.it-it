---
title: Connettersi a Synapse SQL con Power BI Professional
description: In questa esercitazione verranno illustrate le procedure per la connessione di Power BI Desktop a un pool SQL serverless (anteprima).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 97b611c449302c95d4b24c305ce50ee7683e85ea
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316467"
---
# <a name="connect-to-serverless-sql-pool-with-power-bi-professional"></a>Connettersi a un pool SQL serverless con Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

In questa esercitazione verranno illustrate le procedure per la connessione di Power BI Desktop a un pool SQL serverless (anteprima).

## <a name="prerequisites"></a>Prerequisiti

Per inviare query, sono necessari gli strumenti seguenti:

- Client SQL preferito:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI Desktop installato

Parametri

| Parametro                                 | Descrizione                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Indirizzo dell'endpoint servizio del pool SQL serverless    | Verrà usato come nome del server                                   |
| Area dell'endpoint servizio del pool SQL serverless     | Verrà usata per determinare quale archiviazione si userà negli esempi |
| Nome utente e password per l'accesso all'endpoint | Verranno usati per accedere all'endpoint                               |
| Database che verrà usato per creare le visualizzazioni       | Questo database verrà usato come punto di partenza negli esempi       |

## <a name="first-time-setup"></a>Prima configurazione

Prima di usare gli esempi, è necessario eseguire due passaggi:

1. Creare il database per le visualizzazioni
2. Creare le credenziali che devono essere usate dal pool SQL serverless per accedere ai file nell'archiviazione

### <a name="create-database"></a>Creazione del database

Per questo articolo di avvio rapido, è necessario creare un database da usare come demo. Per la creazione delle visualizzazioni occorre un database. Questo database verrà usato in alcune delle query di esempio in questa documentazione.

> [!NOTE]
> I database vengono usati solo per visualizzare i metadati, non per i dati effettivi.
>
> Prendere nota del nome del database usato, perché sarà necessario in un secondo momento.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Creare le credenziali

Prima di poter eseguire le query, è necessario creare le credenziali. Queste credenziali verranno usate dal servizio del pool SQL serverless per accedere ai file nella risorsa di archiviazione.

> [!NOTE]
> È necessario creare le credenziali per l'accesso all'account di archiviazione. Sebbene il pool SQL serverless possa accedere ad account di archiviazione di aree diverse, la disponibilità dell'account e dell'area di lavoro di Azure Synapse nella stessa area offrirà un'esperienza migliore in termini di prestazioni.

**Frammento di codice per la creazione di credenziali per i contenitori di dati del censimento**, eseguire:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Creare un report di Power BI Desktop

Aprire l'applicazione Power BI Desktop e selezionare l'opzione **Recupera dati**.

![Aprire l'applicazione Power BI Desktop e selezionare Recupera dati.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Passaggio 1: Selezionare l'origine dati

Scegliere **Azure** dal menu e quindi **Database SQL di Azure**.
![Selezionare l'origine dati.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Passaggio 2: Selezionare il database

Scrivere l'URL del database e il nome del database in cui risiede la visualizzazione.
![Selezionare il database nell'endpoint.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Passaggi successivi

Passare all'argomento [Eseguire query sui file di archiviazione](get-started-azure-data-studio.md) per informazioni su come connettersi a un pool SQL serverless tramite Azure Data Studio.