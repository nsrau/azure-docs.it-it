---
title: 'Connettersi ad Azure SQL Data Warehouse: VSTS |Documentazione Microsoft'
description: Eseguire query in SQL Data Warehouse con Visual Studio.
services: sql-data-warehouse
documentationcenter: NA
author: antvgski
manager: jhubbard
editor: 
ms.assetid: daace889-95e5-4826-b2fc-047eac9d6d95
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: connect
ms.date: 10/31/2016
ms.author: anvang;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 342f32596c91d2af7c0f33138a43d5c8925d5350
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017


---
# <a name="connect-to-sql-data-warehouse-with-visual-studio-and-ssdt"></a>Connettersi a SQL Data Warehouse con Visual Studio e SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

È possibile usare Visual Studio per eseguire query in Azure SQL Data Warehouse in pochi minuti. Questo metodo usa l'estensione di SQL Server Data Tools (SSDT) in Visual Studio. 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un'istanza di SQL Data Warehouse esistente. Per crearne una, vedere [Creare un Azure SQL Data Warehouse][Create a SQL Data Warehouse].
* SSDT per Visual Studio. Se Visual Studio è già installato, probabilmente SSDT è già disponibile. Per istruzioni sull'installazione e sulle opzioni, vedere [Installazione di Visual Studio e SSDT][Installing Visual Studio and SSDT].
* Il nome completo dell'istanza di SQL Server. Per trovarlo, vedere [Connettersi ad Azure SQL Data Warehouse][Connect to SQL Data Warehouse].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. Connettersi all'istanza di SQL Data Warehouse
1. Aprire Visual Studio 2013 o 2015.
2. Aprire Esplora oggetti di SQL Server. A questo scopo, selezionare **Visualizza** > **Esplora oggetti di SQL Server**.
   
    ![Esplora oggetti di SQL Server][1]
3. Fare clic sull'icona **Aggiungi SQL Server** .
   
    ![Aggiungi SQL Server][2]
4. Compilare i campi nella finestra Connetti al server.
   
    ![Connetti al server][3]
   
   * **Nome server**. Immettere il **nome server** identificato in precedenza.
   * **Autenticazione**. Selezionare **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**.
   * **Nome utente** e **Password**. Se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
   * Fare clic su **Connect**.
5. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.
   
    ![Esplorare AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. Eseguire una query di esempio
Ora che è stata stabilita una connessione al database, è possibile scrivere una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Scegliere **Nuova query**. Verrà visualizzata una nuova finestra di query.
   
    ![Nuova query][5]
3. Copiare la query TSQL seguente nella finestra di query:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Eseguire la query. A questo scopo, fare clic sulla freccia verde oppure usare la combinazione di tasti seguente: `CTRL`+`SHIFT`+`E`.
   
    ![Esegui query][6]
5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.
   
    ![Risultati query][7]

## <a name="next-steps"></a>Passaggi successivi
Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con PowerBI][visualizing the data with PowerBI].

Per configurare l'ambiente per l'autenticazione di Azure Active Directory, vedere [Eseguire l'autenticazione in SQL Data Warehouse][Authenticate to SQL Data Warehouse].

<!--Arcticles-->
[Connect to SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[Authenticate to SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizing the data with PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png

