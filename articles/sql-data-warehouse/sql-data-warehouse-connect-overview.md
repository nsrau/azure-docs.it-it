<properties
   pageTitle="Connettersi ad Azure SQL Data Warehouse |Microsoft Azure"
   description="Panoramica sulla connessione ad Azure SQL Data Warehouse"
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
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Connettersi ad Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-connect-overview.md)
- [Autenticazione](sql-data-warehouse-authentication.md)
- [Driver](sql-data-warehouse-connection-strings.md)

Panoramica sulla connessione ad Azure SQL Data Warehouse.

## Trovare la stringa di connessione nel portale

SQL Data Warehouse è associato a un server di Azure SQL. Per connettersi è necessario il nome completo del server, ad esempio, **mioserver**.database.windows.net.

Per trovare il nome completo del server, procedere come segue:

1. Accedere al [portale di Azure][].
2. Fare clic su **Database SQL** e sul database a cui connettersi. Questo esempio usa il database di esempio AdventureWorksDW.
3. Individuare il nome completo del server.

    ![Nome completo del server][1]

## Impostazioni di connessione

SQL Data Warehouse standardizza alcune impostazioni durante la connessione e la creazione di oggetti. Queste impostazioni non possono essere sottoposte a override.

| Impostazione del database | Valore |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ATTIVA |
| [QUOTED\_IDENTIFIERS][] | ATTIVA |
| [DATEFORMAT][] | mdy |
| [DATEFORMAT][] | 7 |

## Monitoraggio di connessioni e query

Dopo aver stabilito una connessione e una sessione, è possibile scrivere e inviare query a SQL Data Warehouse. Per informazioni su come monitorare le sessioni e le query, vedere [Monitoraggio del carico di lavoro mediante DMV][].

## Passaggi successivi

Per iniziare a eseguire query sul data warehouse con Visual Studio e altre applicazioni, vedere [Eseguire query in Azure SQL Data Warehouse (Visual Studio)][].

<!--Articles-->
[Eseguire query in Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Monitoraggio del carico di lavoro mediante DMV]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[portale di Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0831_2016-->