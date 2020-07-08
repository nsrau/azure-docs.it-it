---
title: Connettersi con VSTS
description: Eseguire query su Azure sinapsi Analytics con Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a3e8ea3b0ed2840e5eee7a046fa3a145f82884dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200699"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Connettersi ad Azure sinapsi Analytics con Visual Studio e SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Usare Visual Studio per eseguire query in un pool SQL all'interno di una sinapsi di Azure in pochi minuti. Questo metodo usa l'estensione di SQL Server Data Tools (SSDT) in Visual Studio 2019. 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un pool SQL esistente. Per crearne uno, vedere [Creare un pool SQL](create-data-warehouse-portal.md).
* SSDT per Visual Studio. Se si dispone di Visual Studio, probabilmente si dispone già di SSDT per Visual Studio. Per istruzioni sull'installazione e sulle opzioni, vedere [Installare Visual Studio 2015 e SSDT per SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md).
* Il nome completo dell'istanza di SQL Server. Per trovare queste informazioni, vedere [connettersi al pool SQL](../sql/connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. connettersi al pool SQL
1. Aprire Visual Studio 2019.
2. Aprire Esplora oggetti di SQL Server selezionando **Visualizza**  >  **Esplora oggetti di SQL Server**.
   
    ![Esplora oggetti di SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Fare clic sull'icona **Aggiungi SQL Server** .
   
    ![Aggiungi SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Compilare i campi nella finestra Connetti al server.
   
    ![Connetti al server](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Nome del server**. Immettere il **nome server** identificato in precedenza.
   * **Autenticazione**. Selezionare **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**.
   * **Nome utente** e **password**. Se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
   * Fare clic su **Connetti**.
5. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.
   
    ![Esplorare AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Eseguire una query di esempio
Ora che è stata stabilita una connessione al database, è possibile scrivere una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
   
    ![Nuova query](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Copiare la query T-SQL seguente nella finestra di query:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Per eseguire la query, fare clic sulla freccia verde oppure usare il collegamento seguente: `CTRL`+`SHIFT`+`E`.
   
    ![Esegui query](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.
   
    ![Risultati query](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

Per configurare l'ambiente per l'autenticazione di Azure Active Directory, vedere [Eseguire l'autenticazione in un pool SQL](sql-data-warehouse-authentication.md).
