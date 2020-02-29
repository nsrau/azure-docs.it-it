---
title: Connettersi a SSMS
description: Usare SQL Server Management Studio (SSMS) per connettersi ed eseguire query in Azure sinapsi Analytics.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2109402a874ff8c722bd05e1e5cb62b461cb2292
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198624"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Connettersi ad Azure sinapsi Analytics con SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Usare SQL Server Management Studio (SSMS) per connettersi ed eseguire query su un data warehouse all'interno di sinapsi di Azure. 

## <a name="prerequisites"></a>Prerequisites
Per eseguire questa esercitazione, è necessario:

* Un pool SQL esistente. Per crearne uno, vedere [creare un pool SQL](sql-data-warehouse-get-started-provision.md).
* SQL Server Management Studio (SSMS) installato. [Installare SSMS](https://msdn.microsoft.com/library/hh213248.aspx) gratuitamente se non è già disponibile.
* Il nome completo dell'istanza di SQL Server. Per trovare queste informazioni, vedere [connettersi al pool SQL](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. connettersi al pool SQL
1. Aprire SSMS.
2. Aprire Esplora oggetti selezionando **File** > **Connetti Esplora oggetti**.
   
    ![Esplora oggetti di SQL Server](media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Compilare i campi nella finestra Connetti al server.
   
    ![Connetti al server](media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nome server**. Immettere il **nome server** identificato in precedenza.
   * **Autenticazione**. Selezionare **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**.
   * **Nome utente** e **Password**. Se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
   * Fare clic su **Connetti**.
4. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.
   
    ![Esplorare AdventureWorksDW](media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. eseguire una query di esempio
Ora che è stata stabilita una connessione al database, è possibile scrivere una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
   
    ![Nuova query]( media/sql-data-warehouse-query-ssms/new-query.png)
3. Copiare la query T-SQL seguente nella finestra di query:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Eseguire la query facendo clic su `Execute` o usare il collegamento seguente: `F5`.
   
    ![Esegui query](media/sql-data-warehouse-query-ssms/execute-query.png)
5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.
   
    ![Risultati query](media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è possibile connettersi ed eseguire una query, provare [a visualizzare i dati con Power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md ).
Per configurare l'ambiente per l'autenticazione Azure Active Directory, vedere eseguire l'autenticazione [al pool SQL](sql-data-warehouse-authentication.md).
