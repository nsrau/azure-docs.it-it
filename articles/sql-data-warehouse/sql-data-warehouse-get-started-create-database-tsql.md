---
title: Creare un&quot;istanza di SQL Data Warehouse con TSQL | Documentazione Microsoft
description: Informazioni su come creare un&quot;istanza di Azure SQL Data Warehouse con TSQL
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 836d72e32e54ecef9691b55214766a1fc3ff9701
ms.contentlocale: it-it
ms.lasthandoff: 12/07/2016


---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Creare un database di SQL Data Warehouse usando Transact-SQL (TSQL)
> [!div class="op_single_selector"]
> * [portale di Azure](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Questo articolo illustra come creare un'istanza di SQL Data Warehouse usando T-SQL.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare, è necessario:

* **Account Azure**: per creare un account, vedere la [versione di valutazione gratuita][Azure Free Trial] o [MSDN Azure Credits][MSDN Azure Credits] (Crediti Azure MSDN).
* **Server SQL di Azure**: per altre informazioni dettagliate, vedere [Create an Azure SQL Database logical server with the Azure Portal][Create an Azure SQL Database logical server with the Azure Portal] \(Creare un server logico del database SQL di Azure con il portale di Azure) o [Creare un server logico del database SQL di Azure con PowerShell][Create an Azure SQL Database logical server with PowerShell].
* **Gruppo di risorse**: usare lo stesso gruppo di risorse del server di Azure SQL oppure vedere [Creare un gruppo di risorse][how to create a resource group].
* **Ambiente per l'esecuzione di T-SQL**: per eseguire T-SQL, è possibile usare [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][sqlcmd] o [SSMS][SSMS].

> [!NOTE]
> La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile.  Per altre informazioni dettagliate sui prezzi, vedere [Prezzi di SQL Data Warehouse][SQL Data Warehouse pricing].
>
>

## <a name="create-a-database-with-visual-studio"></a>Creare un database con Visual Studio
Se non si ha familiarità con Visual Studio, vedere l'articolo [Query Azure SQL Data Warehouse (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)] (Eseguire query in Azure SQL Data Warehouse (Visual Studio)).  Per iniziare, aprire Esplora oggetti di SQL Server in Visual Studio e connettersi al server che ospiterà il database di SQL Data Warehouse.  Dopo aver stabilito la connessione, è possibile creare un'istanza di SQL Data Warehouse eseguendo questo comando SQL sul database **master** .  Questo comando crea il database MySqlDwDb con un obiettivo di servizio DW400 e consente un aumento delle dimensioni del database fino al limite massimo di 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Creare un database con sqlcmd
In alternativa, è possibile usare lo stesso comando con sqlcmd eseguendo quanto segue al prompt dei comandi.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Se non specificate, le regole di confronto predefinite sono COLLATE SQL_Latin1_General_CP1_CI_AS.  Il parametro `MAXSIZE` può essere compreso tra 250 GB e 240 TB.  `SERVICE_OBJECTIVE` può essere compreso tra DW100 e DW2000 [DWU][DWU].  Per un elenco di tutti i valori validi, vedere la documentazione in MSDN relativa a [CREATE DATABASE][CREATE DATABASE].  MAXSIZE e SERVICE_OBJECTIVE possono essere entrambi modificati con un comando [ALTER DATABASE][ALTER DATABASE] T-SQL.  Le regole di confronto di un database non possono essere modificate dopo la creazione.   Prestare attenzione quando si modifica il parametro SERVICE_OBJECTIVE, perché la modifica di DWU provoca un riavvio dei servizi e il conseguente annullamento delle query in corso.  La modifica di MAXSIZE non riavvia i servizi, perché si tratta di una semplice operazione sui metadati.

## <a name="next-steps"></a>Passaggi successivi
Al termine del provisioning di SQL Data Warehouse, è possibile [caricare dati di esempio][load sample data] o vedere come eseguire le attività di [sviluppo][develop], [caricamento][load] o [migrazione][migrate].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

