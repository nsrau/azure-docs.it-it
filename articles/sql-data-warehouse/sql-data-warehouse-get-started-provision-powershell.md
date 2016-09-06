<properties
   pageTitle="Creare un'istanza di SQL Data Warehouse con PowerShell | Microsoft Azure"
   description="Creare un'istanza di SQL Data Warehouse con PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/> 

# Creare SQL Data Warehouse con PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Questo articolo illustra come creare un'istanza di SQL Data Warehouse usando PowerShell.

## Prerequisiti

Per iniziare, è necessario:

- **Account Azure**: vedere la [versione di valutazione gratuita][] o [Credito Azure mensile per sottoscrittori di Visual Studio][] per creare un account.
- **Azure SQL server**: vedere [Creare un server logico del database SQL di Azure][] o [Configurazione del database: creare un gruppo di risorse, il server e la regola firewall][].
- **Gruppo di risorse**: usare lo stesso gruppo di risorse del server di Azure SQL oppure vedere [Creare un gruppo di risorse][].
- **PowerShell versione 1.0.3 o successiva**: è possibile verificare la versione in uso eseguendo **Get-Module -ListAvailable -Name Azure**. È possibile installare la versione più recente da [Installazione guidata piattaforma Web Microsoft][]. Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][].

> [AZURE.NOTE] La creazione di un'istanza di SQL Data Warehouse può dare luogo a un nuovo servizio fatturabile. Per informazioni dettagliate sui prezzi, vedere [Prezzi di SQL Data Warehouse][].

## Creare un SQL Data Warehouse

1. Aprire Windows PowerShell.
2. Eseguire questo cmdlet per accedere a Gestione risorse di Azure.

	```Powershell
	Login-AzureRmAccount
	```
	
3. Selezionare la sottoscrizione da usare per la sessione corrente.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  Creare il database. Questo esempio crea un database denominato "mynewsqldw", con un livello di obiettivo di servizio "DW400" nel server denominato "sqldwserver1" incluso nel gruppo di risorse "mywesteuroperesgp1".

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
	```

I parametri obbligatori sono:

- **RequestedServiceObjectiveName**: quantità di [DWU][] richiesta. I valori supportati sono: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
- **DatabaseName**: il nome dell'istanza di SQL Data Warehouse che si sta creando.
- **ServerName**: il nome del server che si sta usando per la creazione (deve essere V12).
- **ResourceGroupName**: il gruppo di risorse in uso. Per trovare i gruppi di risorse disponibili nella sottoscrizione, usare Get-AzureResource.
- **Edition**: per creare un'istanza di SQL Data Warehouse, deve essere "DataWarehouse".

I parametri facoltativi sono:

- **CollationName**: se non specificate, le regole di confronto predefinite sono SQL\_Latin1\_General\_CP1\_CI\_AS. Non è possibile modificare le regole di confronto in un database.
- **MaxSizeBytes**: la dimensione massima predefinita di un database è 10 GB.


Per informazioni dettagliate sulle opzioni dei parametri, vedere [New-AzureRmSqlDatabase][] e [CREATE DATABASE (Azure SQL Data Warehouse)][].

## Passaggi successivi

Al termine del provisioning di SQL Data Warehouse, è possibile provare a [caricare dati di esempio][] o scoprire come eseguire le attività di [sviluppo][], [caricamento][] o [migrazione][].

Per altre informazioni su come gestire SQL Data Warehouse a livello di codice, vedere l'articolo [Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse][].

<!--Image references--> 

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrazione]: ./sql-data-warehouse-overview-migrate.md
[sviluppo]: ./sql-data-warehouse-overview-develop.md
[caricamento]: ./sql-data-warehouse-load-with-bcp.md
[caricare dati di esempio]: ./sql-data-warehouse-load-sample-databases.md
[Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Come installare e configurare Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Creare un server logico del database SQL di Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Configurazione del database: creare un gruppo di risorse, il server e la regola firewall]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Creare un gruppo di risorse]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[CREATE DATABASE (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references--> 
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps
[Prezzi di SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[versione di valutazione gratuita]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Credito Azure mensile per sottoscrittori di Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0831_2016-->