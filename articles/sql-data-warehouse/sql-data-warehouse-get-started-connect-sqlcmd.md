<properties
   pageTitle="Introduzione: Connettersi ad Azure SQL Data Warehouse | Microsoft Azure"
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
   ms.date="05/16/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Connettersi ed eseguire query con SQLCMD

> [AZURE.SELECTOR]
- [Visual Studio][]
- [SQLCMD][]

Questa procedura dettagliata mostra come connettersi ed eseguire query su un database di SQL Data Warehouse di Azure in pochi minuti usando l'utilità sqlcmd.exe. In questa procedura dettagliata si eseguiranno i passaggi seguenti:

+ Installare il software prerequisito
+ Connettersi a un database contenente il database di esempio AdventureWorksDW
+ Eseguire una query sul database di esempio  

## Prerequisiti

+ Per scaricare [sqlcmd.exe][], vedere [Microsoft Command Line Utilities 11 for SQL Server][].

## Ottenere il nome completo del server SQL di Azure

Per connettersi al database, è necessario il nome completo del server (****nomeserver**.database.windows.net*) che contiene il database a cui ci si vuole connettere.

1. Accedere al [portale di Azure][].
2. Passare al database a cui si desidera connettersi.
3. Trovare il nome completo del server, che verrà usato nei passaggi successivi:

![][1]


## Connettersi a SQL Data Warehouse con sqlcmd

Per connettersi a un'istanza specifica di SQL Data Warehouse quando si usa sqlcmd, sarà necessario aprire il prompt dei comandi e digitare **sqlcmd** seguito dalla stringa di connessione per il database di SQL Data Warehouse. La stringa di connessione richiede i parametri obbligatori seguenti:

+ **Server (-S):** server nel formato `<`Server Name`>`.database.windows.net
+ **Database (-d):** nome del database.
+ **Utente (-U):** utente del server nel formato `<`User`>`
+ **Password (-P):** password associata all'utente.
+ **Abilita identificatori delimitati (-I):** gli identificatori delimitati devono essere abilitati per consentire la connessione a un'istanza di SQL Data Warehouse.

Per connettersi a un'istanza di SQL Data Warehouse, è quindi necessario immettere quanto segue:

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Eseguire query di esempio

Dopo la connessione sarà possibile eseguire qualsiasi istruzione Transact-SQL supportata nell'istanza.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Per altre informazioni su sqlcmd, vedere la [relativa documentazione][sqlcmd.exe].


## Passaggi successivi

Ora che è possibile connettersi ed eseguire query, provare a [connettersi con PowerBI][].

Per configurare l'ambiente per l'autenticazione di Windows, vedere [Connessione al database SQL o SQL Data Warehouse con l'autenticazione di Azure Active Directory][].

<!--Articles-->
[Connessione al database SQL o SQL Data Warehouse con l'autenticazione di Azure Active Directory]: ../sql-database/sql-database-aad-authentication.md
[connettersi con PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/it-IT/library/ms162773.aspx
[Microsoft Command Line Utilities 11 for SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[portale di Azure]: https://portal.azure.com

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

<!---HONumber=AcomDC_0518_2016-->