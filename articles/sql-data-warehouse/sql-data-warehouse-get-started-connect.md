<properties
   pageTitle="Connettersi a SQL Data Warehouse con Visual Studio | Microsoft Azure"
   description="Introduzione alla connessione a SQL Data Warehouse e all'esecuzione di alcune query."
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
   ms.date="05/13/2016"
   ms.author="sonyama;barbkess"/>

# Connettersi a SQL Data Warehouse con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)

Questa procedura dettagliata mostra come connettersi a un'istanza di Azure SQL Data Warehouse in pochi minuti usando l'estensione SQL Server Data Tools (SSDT) in Visual Studio. Una volta stabilita la connessione, si eseguirà una query semplice.

## Prerequisiti

+ Dati AdventureWorksDW di esempio in SQL Data Warehouse. Per crearne un'istanza, vedere [Creare un SQL Data Warehouse][].
+ SQL Server Data Tools per Visual Studio. Per istruzioni sull'installazione e sulle opzioni, vedere [Installare Visual Studio 2015 e SSDT per SQL Data Warehouse][].

## Passaggio 1: Trovare il nome completo del server SQL Azure

Il database di SQL Data Warehouse è associato a un server di Azure SQL. Per connettersi al database, è necessario il nome completo del server, ad esempio **nomeserver**.database.windows.net*.

Per trovare il nome completo del server.

1. Accedere al [portale di Azure][].
2. Fare clic su **Database SQL** e sul database a cui connettersi. Questo esempio usa il database di esempio AdventureWorksDW.
3. Individuare il nome completo del server.

    ![Nome completo del server][1]

## Passaggio 2: Connettersi all'istanza di SQL Data Warehouse

1. Aprire Visual Studio 2013 o 2015.
2. Aprire Esplora oggetti di SQL Server. A questo scopo, selezionare **Visualizza** > **Esplora oggetti di SQL Server**.

    ![Esplora oggetti di SQL Server.][2]

3. Fare clic sull'icona **Aggiungi SQL Server**.

    ![Aggiungi SQL Server][3]

4. Compilare i campi nella finestra Connetti al server.

    ![Connetti al server][4]

    - **Nome server**. Immettere il **nome del server** identificato in precedenza.
    - **Autenticazione**. Selezionare **Autenticazione SQL Server** o **Autenticazione integrata di Active Directory**.
    - **Nome utente** e **Password**. Se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
    - Fare clic su **Connect**.

5. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.

    ![Esplorare AdventureWorksDW][5]

## Passaggio 3: Eseguire una query di esempio

Ora che è stata stabilita una connessione al database, è possibile scrivere una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.

2. Scegliere **Nuova query**. Verrà visualizzata una nuova finestra di query.

    ![Nuova query][6]

3. Copiare la query TSQL seguente nella finestra di query:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Eseguire la query. A questo scopo, fare clic sulla freccia verde oppure usare la combinazione di tasti seguente: `CTRL`+`SHIFT`+`E`.

    ![Esegui query][7]

5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.

    ![Risultati query][8]

## Passaggi successivi

Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con PowerBI][].

Per configurare l'ambiente per l'autenticazione di Windows, vedere [Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory][].

<!--Arcticles-->
[Creare un SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installare Visual Studio 2015 e SSDT per SQL Data Warehouse]: sql-data-warehouse-install-visual-studio.md
[Connessione al database SQL oppure a SQL Data Warehouse con l'autenticazione di Azure Active Directory]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[visualizzare i dati con PowerBI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md

<!--Other-->
[portale di Azure]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0601_2016-->