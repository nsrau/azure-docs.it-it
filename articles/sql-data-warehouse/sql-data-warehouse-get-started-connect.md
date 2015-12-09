<properties
   pageTitle="Connettersi a SQL Data Warehouse con Visual Studio | Microsoft Azure"
   description="Introduzione alla connessione a SQL Data Warehouse e all'esecuzione di alcune query."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/22/2015"
   ms.author="twounder;barbkess"/>

# Connettersi a SQL Data Warehouse con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

Questa procedura dettagliata mostra come connettersi ed eseguire query su un database di Azure SQL Data Warehouse in pochi minuti usando SQL Server Data Tools in Visual Studio. Una volta stabilita la connessione, si eseguirà una query semplice.

## Prerequisiti

+ Database di esempio AdventureWorksDW in SQL Data Warehouse. Per crearlo, vedere [Creare un database di SQL Data Warehouse](sql-data-warehouse-get-started-create.md). 
+ SQL Server Data Tools per Visual Studio. Per istruzioni sull'installazione e sulle opzioni, vedere [Installare Visual Studio e/o SSDT](sql-data-warehouse-install-visual-studio.md)

## Passaggio 1: Trovare il nome completo del server SQL Azure

Il database è associato a un server SQL Azure. Per connettersi al database, è necessario il nome completo del server, **nomeserver**.database.windows.net*.

Per trovare il nome completo del server.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Database SQL** e sul database a cui connettersi. Questo esempio usa il database di esempio AdventureWorksDW.
3. Individuare il nome completo del server.

    ![Nome completo del server][1]

## Passaggio 2: Connettersi al database SQL

1. Aprire Visual Studio.
2. Aprire Esplora oggetti di SQL Server. A questo scopo, selezionare **Visualizza** > **Esplora oggetti di SQL Server**.
 
    ![Esplora oggetti di SQL Server.][2]

3. Fare clic sull'icona **Aggiungi SQL Server**.

    ![Aggiungi SQL Server][3]

1. Compilare i campi nella finestra Connetti al server.

    ![Connetti al server][4]

    - **Nome server**. Immettere il *nome server* trovato in precedenza.
    - **Autenticazione**. Selezionare Autenticazione di SQL Server.
    - **Account di accesso** e **Password**. Immettere l'account di accesso e la password per il server SQL Azure.
    - Fare clic su **Connetti**.

1. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.

    ![Esplorare AdventureWorksDW][5]


## Passaggio 3: Eseguire una query di esempio

Dopo essersi connessi al server, è possibile scrivere una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server. 

2. Scegliere **Nuova query**. Verrà visualizzata una nuova finestra di query.

    ![Nuova query][6]

3. Copiare la query TSQL seguente nella finestra di query:

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Eseguire la query. A questo scopo, fare clic sulla freccia verde oppure usare la combinazione di tasti seguente: `CTRL`+`SHIFT`+`E`.

    ![Esegui query][7]

1. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.

    ![Risultati query][8]

## Passaggi successivi

Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con PowerBI][].

[visualizzare i dati con PowerBI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_1203_2015-->