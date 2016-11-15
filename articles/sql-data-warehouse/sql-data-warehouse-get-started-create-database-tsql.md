---
title: Creare un&quot;istanza di SQL Data Warehouse con TSQL | Documentazione Microsoft
description: Informazioni su come creare un&quot;istanza di Azure SQL Data Warehouse con TSQL
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 443e74834a7181560e812ce31db460ef2c4ff911


---
# <a name="create-a-sql-data-warehouse-database-by-using-transactsql-tsql"></a>Creare un database di SQL Data Warehouse usando Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [Portale di Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

Questo articolo illustra come creare un'istanza di SQL Data Warehouse usando T-SQL.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare, è necessario: 

* **Account di Azure**: vedere la [versione di valutazione gratuita][versione di valutazione gratuita] o [Crediti Azure MSDN][Crediti Azure MSDN] per creare un account.
* **Server di Azure SQL**: per maggiori dettagli, vedere [Creare un server logico del database SQl di Azure usando il portale di Azure][Creare un server logico del database SQl di Azure usando il portale di Azure] o [Creare un server logico del database SQl di Azure usando PowerShell][Creare un server logico del database SQl di Azure usando PowerShell].
* **Gruppo di risorse**: usare lo stesso gruppo di risorse del server di Azure SQL o vedere [Come creare un gruppo di risorse][Come creare un gruppo di risorse].
* **Ambiente per l'esecuzione di T-SQL**: per eseguire T-SQL, è possibile usare [Visual Studio][Installare Visual Studio e SSDT], [sqlcmd][sqlcmd] o [SSMS][SSMS].

> [!NOTE]
> La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile.  Per informazioni dettagliate sui prezzi, vedere [Prezzi di SQL Data Warehouse][Prezzi di SQL Data Warehouse].
> 
> 

## <a name="create-a-database-with-visual-studio"></a>Creare un database con Visual Studio
Se non si ha familiarità con Visual Studio, vedere l'articolo [Eseguire query in Azure SQL Data Warehouse (Visual Studio)][Eseguire query in Azure SQL Data Warehouse (Visual Studio)].  Per iniziare, aprire Esplora oggetti di SQL Server in Visual Studio e connettersi al server che ospiterà il database di SQL Data Warehouse.  Dopo aver stabilito la connessione, è possibile creare un'istanza di SQL Data Warehouse eseguendo questo comando SQL sul database **master** .  Questo comando crea il database MySqlDwDb con un obiettivo di servizio DW400 e consente un aumento delle dimensioni del database fino al limite massimo di 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Creare un database con sqlcmd
In alternativa, è possibile usare lo stesso comando con sqlcmd eseguendo quanto segue al prompt dei comandi.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Se non specificate, le regole di confronto predefinite sono COLLATE SQL_Latin1_General_CP1_CI_AS.  Il parametro `MAXSIZE` può essere compreso tra 250 GB e 240 TB.  `SERVICE_OBJECTIVE` può essere compreso tra DW100 e DW2000 [DWU][DWU].  Per un elenco di tutti i valori validi, vedere la documentazione in MSDN relativa a [CREATE DATABASE][CREATE DATABASE].  MAXSIZE e SERVICE_OBJECTIVE possono essere entrambi modificati con un comando [ALTER DATABASE][ALTER DATABASE].  Le regole di confronto di un database non possono essere modificate dopo la creazione.   Prestare attenzione quando si modifica il parametro SERVICE_OBJECTIVE, perché la modifica di DWU provoca un riavvio dei servizi e il conseguente annullamento delle query in corso.  La modifica di MAXSIZE non riavvia i servizi, perché si tratta di una semplice operazione sui metadati.

## <a name="next-steps"></a>Passaggi successivi
Al termine del provisioning di SQL Data Warehouse, è possibile provare a [caricare dati di esempio][caricare dati di esempio] o scoprire come eseguire le attività di [sviluppo][sviluppo], [caricamento][caricamento] o [migrazione][migrazione].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[Procedura: Creare un database SQL Data Warehouse nel portale di Azure]: sql-data-warehouse-get-started-provision.md
[Eseguire query in Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppo]: sql-data-warehouse-overview-develop.md
[caricamento]: sql-data-warehouse-overview-load.md
[caricare dati di esempio]: sql-data-warehouse-load-sample-databases.md
[Creare un server logico del database SQL di Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Creare un server logico del database SQL di Azure con Powershell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Creare un gruppo di risorse]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installare Visual Studio e SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Prezzi di SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[versione di valutazione gratuita]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Credito Azure mensile per sottoscrittori di Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


