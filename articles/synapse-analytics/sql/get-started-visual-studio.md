---
title: Connettersi a SQL Synapse ed eseguire query su Synapse sql con Visual Studio e SSDTConnect to and query Synapse SQL with Visual Studio and SSDT
description: Usare Visual Studio per eseguire query sul pool SQL usando Azure Synapse Analytics.Use Visual Studio to query SQL pool using Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428563"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Connettersi a Synapse SQL con Visual Studio e SSDTConnect to Synapse SQL with Visual Studio and SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [Sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Usare Visual Studio per eseguire query sul pool SQL usando Azure Synapse Analytics.Use Visual Studio to query SQL pool using Azure Synapse Analytics. Questo metodo utilizza l'estensione SQL Server Data Tools (SSDT) in Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Strumenti supportati per SQL su richiesta (anteprima)Supported tools for SQL on-demand (preview)

Visual Studio non è attualmente supportato per SQL su richiesta (anteprima). Tuttavia, Azure Data Studio (anteprima)o è uno strumento completamente supportato. SQL Server Management StudioSQL Server Management Studio è parzialmente supportato dalla versione 18.4 e dispone di funzionalità limitate, ad esempio la connessione e l'esecuzione di query.

## <a name="prerequisites"></a>Prerequisiti
Per usare questa esercitazione, è necessario disporre dei componenti seguenti:To use this tutorial, you need to have the following components:

* Un pool SQL esistente. Se non si dispone di uno, vedere [creare un pool SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per completare questo prerequisito.
* SSDT per Visual Studio. Se si dispone di Visual Studio, probabilmente si dispone già di questo componente. Per istruzioni sull'installazione e sulle opzioni, vedere [Installazione di Visual Studio e SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Il nome completo dell'istanza di SQL Server. A tale scopo, vedere [Connettersi al pool SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. Connettersi al pool SQL
1. Aprire Visual Studio 2019.
2. Aprire Esplora oggetti di SQL Server. A tale scopo, selezionare **Visualizza** > **Esplora oggetti di SQL Server**.
   
    ![Esplora oggetti di SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Fare clic sull'icona **Aggiungi SQL Server** .
   
    ![Aggiungi SQL Server](./media/get-started-visual-studio/add-server.png)
4. Compilare i campi nella finestra Connetti al server.
   
    ![Connetti al server](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nome server**: Immettere il nome del **server** identificato in precedenza.
   * **Autenticazione**: Selezionare **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**:
   * **Nome utente** e **password**: Immettere il nome utente e la password se è stata selezionata l'opzione Autenticazione di SQL Server in precedenza.
   * Fare clic su **Connetti**.
5. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.
   
    ![Esplorare AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Eseguire una query di esempio
Ora che è stata stabilita una connessione al database, si scriverà una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
   
    ![Nuova query](./media/get-started-visual-studio/new-query2.png)
3. Copiare la query T-SQL seguente nella finestra della query:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Consente di eseguire la query. A questo scopo, fare clic sulla freccia verde oppure usare la combinazione di tasti seguente: `CTRL`+`SHIFT`+`E`.
   
    ![Esegui query](./media/get-started-visual-studio/run-query.png)
5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.
   
    ![Risultati query](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Passaggi successivi
Dopo aver eseguito la connessione e la query, provare a [visualizzare i dati con Power BI.](get-started-power-bi-professional.md)
Per configurare l'ambiente per l'autenticazione di Azure Active Directory, vedere [Autenticazione nel pool SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 