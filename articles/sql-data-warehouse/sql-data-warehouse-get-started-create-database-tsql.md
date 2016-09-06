<properties
   pageTitle="Creare un'istanza di SQL Data Warehouse con TSQL | Microsoft Azure"
   description="Informazioni su come creare un'istanza di Azure SQL Data Warehouse con TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creare un database di SQL Data Warehouse usando Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Questo articolo illustra come creare un'istanza di SQL Data Warehouse usando T-SQL.

## Prerequisiti

Per iniziare, è necessario:

- **Account Azure**: vedere la [versione di valutazione gratuita][] o [Credito Azure mensile per sottoscrittori di Visual Studio][] per creare un account.
- **Azure SQL server**: vedere [Creare un server logico del database SQL di Azure][] o [Configurazione del database: creare un gruppo di risorse, il server e la regola firewall][].
- **Gruppo di risorse**: usare lo stesso gruppo di risorse del server di Azure SQL oppure vedere [Creare un gruppo di risorse][].
- **Ambiente per l'esecuzione di T-SQL**: per eseguire T-SQL, è possibile usare [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][] o [SSMS][].

> [AZURE.NOTE] La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile. Per informazioni dettagliate sui prezzi, vedere [Prezzi di SQL Data Warehouse][].

## Creare un database con Visual Studio

Se non si ha familiarità con Visual Studio, vedere l'articolo [Eseguire query in Azure SQL Data Warehouse (Visual Studio)][]. Per iniziare, aprire Esplora oggetti di SQL Server in Visual Studio e connettersi al server che ospiterà il database di SQL Data Warehouse. Dopo aver stabilito la connessione, è possibile creare un'istanza di SQL Data Warehouse eseguendo questo comando SQL sul database **master**. Questo comando crea il database MySqlDwDb con un obiettivo di servizio DW400 e consente un aumento delle dimensioni del database fino al limite massimo di 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Creare un database con sqlcmd

In alternativa, è possibile usare lo stesso comando con sqlcmd eseguendo quanto segue al prompt dei comandi.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Se non specificate, le regole di confronto predefinite sono COLLATE SQL\_Latin1\_General\_CP1\_CI\_AS. Il parametro `MAXSIZE` può essere compreso tra 250 GB e 240 TB. `SERVICE_OBJECTIVE` può essere compreso tra DW100 e DW2000 [DWU][]. Per un elenco di tutti i valori validi, vedere la documentazione in MSDN relativa a [CREATE DATABASE][]. MAXSIZE e SERVICE\_OBJECTIVE possono essere entrambi modificati con un comando T-SQL [ALTER DATABASE][]. Le regole di confronto di un database non possono essere modificate dopo la creazione. Prestare attenzione quando si modifica il parametro SERVICE\_OBJECTIVE, perché la modifica di DWU provoca un riavvio dei servizi e il conseguente annullamento delle query in corso. La modifica di MAXSIZE non riavvia i servizi, perché si tratta di una semplice operazione sui metadati.

## Passaggi successivi

Al termine del provisioning di SQL Data Warehouse, è possibile [caricare dati di esempio][] o vedere come eseguire le attività di [sviluppo][], [caricamento][] o [migrazione][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Eseguire query in Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppo]: sql-data-warehouse-overview-develop.md
[caricamento]: sql-data-warehouse-overview-load.md
[caricare dati di esempio]: sql-data-warehouse-load-sample-databases.md
[Creare un server logico del database SQL di Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Configurazione del database: creare un gruppo di risorse, il server e la regola firewall]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Creare un gruppo di risorse]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Prezzi di SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[versione di valutazione gratuita]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Credito Azure mensile per sottoscrittori di Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0831_2016-->