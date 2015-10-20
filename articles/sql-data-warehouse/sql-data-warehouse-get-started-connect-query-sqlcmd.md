<properties
   pageTitle="Introduzione: Connettersi ad Azure SQL Data Warehouse | Microsoft Azure"
   description="Introduzione alla connessione a SQL Data Warehouse e all'esecuzione di alcune query."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/07/2015"
   ms.author="twounder"/>

# Connettersi ed eseguire query con SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect-query.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-query-sqlcmd.md)

Questa procedura dettagliata mostra come connettersi ed eseguire query su un database di SQL Data Warehouse di Azure in pochi minuti usando l'utilità sqlcmd.exe. In questa procedura dettagliata si eseguiranno i passaggi seguenti:

+ Installare il software prerequisito
+ Connettersi a un database contenente il database di esempio AdventureWorksDW
+ Eseguire una query sul database di esempio  

## Prerequisiti

+ [sqlcmd.exe](https://msdn.microsoft.com/library/azure/ms162773.aspx): per scaricare sqlcmd.exe, vedere [Microsoft Command Line Utilities 11 for SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501).

## Ottenere il nome completo del server SQL di Azure

Per connettersi al database, è necessario il nome completo del server (****nomeserver**.database.windows.net*) che contiene il database a cui ci si vuole connettere.

1. Passare al [portale di anteprima di Azure](https://portal.azure.com).
2. Passare al database a cui si desidera connettersi.
3. Trovare il nome completo del server, che verrà usato nei passaggi successivi:

![][1]


## Connettersi a SQL Data Warehouse con sqlcmd

Per connettersi a un'istanza specifica di SQL Data Warehouse quando si usa sqlcmd, sarà necessario aprire il prompt dei comandi e digitare **sqlcmd** seguito dalla stringa di connessione per il database di SQL Data Warehouse. La stringa di connessione deve includere i parametri seguenti:

+ **Utente (-U):** utente del server nel formato `<`User`>`
+ **Password (-P):** password associata all'utente.
+ **Server (-S):** server nel formato `<`Server Name`>`.database.windows.net
+ **Database (-D):** nome del database.
+ **Abilita identificatori delimitati (-I):** gli identificatori delimitati devono essere abilitati per consentire la connessione a un'istanza di SQL Data Warehouse.

Per connettersi a un'istanza di SQL Data Warehouse, è quindi necessario immettere quanto segue:

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Eseguire query di esempio

Dopo la connessione sarà possibile eseguire qualsiasi istruzione Transact-SQL supportata nell'istanza.

```
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT COUNT(*) FROM dbo.FactInternetSales;
2> GO
3> QUIT
```

Per altre informazioni su sqlcmd, vedere la [relativa documentazione](https://msdn.microsoft.com/library/azure/ms162773.aspx).


## Passaggi successivi

Ora che è possibile connettersi ed eseguire query, provare a [connettersi con PowerBI][].

[connettersi con PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
 

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect-query/get-server-name.png

<!---HONumber=Oct15_HO3-->