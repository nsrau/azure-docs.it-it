<properties
   pageTitle="Creare SQL Data Warehouse mediante PowerShell | Microsoft Azure"
   description="Creare SQL Data Warehouse mediante PowerShell"
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
   ms.date="03/26/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creare SQL Data Warehouse con PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Ottenere ed eseguire i cmdlet di Azure PowerShell

> [AZURE.NOTE]  Per usare Microsoft Azure PowerShell con SQL Data Warehouse, è necessario scaricare e installare la versione più recente di Azure PowerShell con i cmdlet di Gestione risorse di Azure. Per controllare la versione è possibile eseguire `Get-Module -ListAvailable -Name Azure`. Questo articolo si basa su Microsoft Azure PowerShell versione 1.0.3 o successiva.

Se PowerShell non è già installato, è necessario scaricarlo e configurarlo.

1. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://aka.ms/webpi-azps). Per altre informazioni su questo programma di installazione, vedere [Come installare e configurare Azure PowerShell][].
2. Per eseguire il modulo, nella finestra di avvio digitare **Windows PowerShell**.
3. Eseguire questo cmdlet per accedere a Gestione risorse di Azure.

	```Powershell
	Login-AzureRmAccount
	```

4. Selezionare la sottoscrizione da usare per la sessione corrente.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

## Creare un database di SQL Data Warehouse
Per distribuire un'istanza di SQL Data Warehouse, usare il cmdlet New-AzureRmSQLDatabase. Prima di eseguire il comando, verificare i prerequisiti seguenti.

### Prerequisiti

- Un server SQL di Azure V12 per ospitare il database
- Conoscere il nome del gruppo di risorse per SQL Server.

### Comando di distribuzione

Questo comando distribuisce un nuovo database in SQL Data Warehouse.

```Powershell
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"
```

Questo esempio illustra come distribuire un nuovo database denominato "mynewsqldw1", con un livello di obiettivo di servizio "DW400", nel server denominato "sqldwserver1" incluso nel gruppo di risorse denominato "mywesteuroperesgp1".

```Powershell
New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw1" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
```

I parametri necessari per questo cmdlet sono i seguenti:

 + **RequestedServiceObjectiveName**: la quantità di DWU richiesta, nel formato "DWXXX". I valori attualmente supportati sono: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 + **DatabaseName**: il nome dell'istanza di SQL Data Warehouse che si sta creando.
 + **ServerName**: il nome del server che si sta usando per la creazione (deve essere V12).
 + **ResourceGroupName**: il gruppo di risorse in uso. Per ottenere i gruppi di risorse disponibili nella sottoscrizione, usare Get-AzureResource.
 + **Edition**: per creare un'istanza di SQL Data Warehouse, è necessario impostare l'edizione su "DataWarehouse".

Per i riferimenti ai comandi, vedere [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619339.aspx).

Per le opzioni di parametro, vedere [Create Database (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt204021.aspx).

## Passaggi successivi
Al termine del provisioning di SQL Data Warehouse, è possibile [caricare dati di esempio][] o vedere come eseguire le attività di [sviluppo][], [caricamento][] o [migrazione][].

Per altre informazioni su come gestire SQL Data Warehouse a livello di codice, consultare la documentazione relativa a [PowerShell][] o all'[API REST][].



<!--Image references-->

<!--Article references-->
[migrazione]: ./sql-data-warehouse-overview-migrate.md
[sviluppo]: ./sql-data-warehouse-overview-develop/.md
[caricare dati di esempio]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Powershell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[API REST]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[firewall rules]: ../sql-database/sql-database-configure-firewall-settings.md
[Come installare e configurare Azure PowerShell]: ./powershell-install-configure.md

<!---HONumber=AcomDC_0330_2016-->