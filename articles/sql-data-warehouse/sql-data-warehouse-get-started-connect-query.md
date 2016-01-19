<properties
   pageTitle="Introduzione: Connettersi ad Azure SQL Data Warehouse | Microsoft Azure"
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
   ms.date="01/07/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Connettersi ed eseguire query con Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

Questa procedura dettagliata mostra come connettersi ed eseguire query su un database di SQL Data Warehouse di Azure in pochi minuti usando Visual Studio. In questa procedura dettagliata si eseguiranno i passaggi seguenti:

+ Installare il software prerequisito
+ Connettersi a un database contenente il database di esempio AdventureWorksDW
+ Eseguire una query sul database di esempio  

## Prerequisiti

+ Visual Studio 2013/2015: per scaricare e installare Visual Studio 2015 e/o SSDT, vedere [Installare Visual Studio e SSDT](sql-data-warehouse-install-visual-studio.md)

## Ottenere il nome completo del server SQL di Azure

Per connettersi al database, è necessario il nome completo del server ( ***nomeserver**.database.windows.net* ) che contiene il database a cui ci si vuole connettere.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al database a cui si desidera connettersi.
3. Trovare il nome completo del server, che verrà usato nei passaggi successivi:

![][1]

## Connettersi al database SQL

1. Aprire Visual Studio.
2. Dal menu Visualizza scegliere **Esplora oggetti di SQL Server**.
 
![][2]

3. Fare clic sul pulsante **Aggiungi SQL Server**.

![][3]

4. Immettere il *nome server* trovato in precedenza.
5. Nella casella **Autenticazione** selezionare **Autenticazione di SQL Server**.
6. Immettere il **Nome di accesso** e la **Password** specificati quando è stato creato il server di database SQL e fare clic su **Connetti**.

## Eseguire query di esempio

Dopo avere registrato il server, è possibile scrivere una query.

1. Fare clic sul database utente in SSDT.

2. Fare clic sul pulsante **Nuova query**. Verrà visualizzata una nuova finestra.

![][4]

3. Immettere il codice seguente nella finestra della query:

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Eseguire la query.

	Per eseguire la query, fare clic sulla freccia verde oppure usare la combinazione di tasti seguente: `CTRL`+`SHIFT`+`E`.

## Passaggi successivi

Ora che è possibile connettersi ed eseguire query, provare a [connettersi con PowerBI][].

[connettersi con PowerBI]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect-query/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect-query/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect-query/connection-dialog.png
[4]: ./media/sql-data-warehouse-get-started-connect-query/new-query.png

<!---HONumber=AcomDC_0114_2016-->