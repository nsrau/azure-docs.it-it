<properties
   pageTitle="Eseguire query in Azure SQL Data Warehouse (Visual Studio) | Microsoft Azure"
   description="Eseguire query in SQL Data Warehouse con Visual Studio."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# Eseguire query in Azure SQL Data Warehouse (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

È possibile usare Visual Studio per eseguire query in Azure SQL Data Warehouse in pochi minuti. Questo metodo usa l'estensione di SQL Server Data Tools (SSDT) in Visual Studio.

## Prerequisiti

Per eseguire questa esercitazione, è necessario:

+ Un'istanza di SQL Data Warehouse esistente. Per crearne una, vedere [Creare un Azure SQL Data Warehouse][].
+ SSDT per Visual Studio. Se Visual Studio è già installato, probabilmente SSDT è già disponibile. Per istruzioni sull'installazione e sulle opzioni, vedere [Installare Visual Studio 2015 e SSDT per SQL Data Warehouse][].
+ Il nome completo dell'istanza di SQL Server. Per trovarlo, vedere [Connect to Azure SQL Data Warehouse][] \(Connettersi ad Azure SQL Data Warehouse).

## 1\. Connettersi all'istanza di SQL Data Warehouse

1. Aprire Visual Studio 2013 o 2015.
2. Aprire Esplora oggetti di SQL Server. A questo scopo, selezionare **Visualizza** > **Esplora oggetti di SQL Server**.

    ![Esplora oggetti di SQL Server.][1]

3. Fare clic sull'icona **Aggiungi SQL Server**.

    ![Aggiungi SQL Server][2]

4. Compilare i campi nella finestra Connetti al server.

    ![Connetti al server][3]

    - **Nome server**. Immettere il **nome server** identificato in precedenza.
    - **Autenticazione**. Selezionare **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**.
    - **Nome utente** e **Password**. Se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
    - Fare clic su **Connect**.

5. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.

    ![Esplorare AdventureWorksDW][4]

## 2\. Eseguire una query di esempio

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

## Passaggi successivi

Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con PowerBI][].

Per configurare l'ambiente per l'autenticazione di Azure Active Directory, vedere [Authentication to Azure SQL Data Warehouse][] \(Autenticazione in Azure SQL Data Warehouse).

<!--Arcticles-->
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Creare un Azure SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installare Visual Studio 2015 e SSDT per SQL Data Warehouse]: sql-data-warehouse-install-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md
[visualizzare i dati con PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md

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

<!---HONumber=AcomDC_0622_2016-->