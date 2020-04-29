---
title: Connettersi a SSMS
description: Usare SQL Server Management Studio (SSMS) per connettersi ed eseguire query in Azure sinapsi Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0d65ea9b684439cab384bbaa09fcdef1302b6bcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743853"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Connettersi ad Azure sinapsi Analytics con SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Usare SQL Server Management Studio (SSMS) per connettersi ed eseguire query su un data warehouse all'interno di sinapsi di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario:

* Un pool SQL esistente. Per crearne uno, vedere [Creare un pool SQL](create-data-warehouse-portal.md).
* SQL Server Management Studio (SSMS) installato. [Scaricare SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gratuitamente se non è già disponibile.
* Il nome completo dell'istanza di SQL Server. Per trovare queste informazioni, vedere [connettersi al pool SQL](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. connettersi al pool SQL

1. Aprire SSMS.
2. Aprire Esplora oggetti selezionando Esplora oggetti **file** > **Connetti**.

    ![Esplora oggetti di SQL Server](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Compilare i campi nella finestra Connetti al server.

   ![Connetti al server](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Nome del server**. Immettere il **nome server** identificato in precedenza.
   * **Autenticazione**. Selezionare **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**.
   * **Nome utente** e **password**. Se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
   * Fare clic su **Connetti**.
4. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.

   ![Esplorare AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. eseguire una query di esempio

Ora che è stata stabilita una connessione al database, è possibile scrivere una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.

   ![Nuova query](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Copiare la query T-SQL seguente nella finestra di query:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Eseguire la query facendo clic `Execute` o usare il collegamento seguente: `F5`.

   ![Esegui query](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.

   ![Risultati query](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md). Per configurare l'ambiente per l'autenticazione Azure Active Directory, vedere eseguire l'autenticazione [al pool SQL](sql-data-warehouse-authentication.md).
