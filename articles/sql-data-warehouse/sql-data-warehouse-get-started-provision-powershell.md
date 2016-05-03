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
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creare SQL Data Warehouse con PowerShell

> [AZURE.SELECTOR]
- [Portale di Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

### Prerequisiti
Prima di iniziare la distribuzione, verificare che siano soddisfatti i prerequisiti seguenti.

- Un server SQL di Azure V12 per ospitare il database
- Conoscenza del nome del gruppo di risorse per SQL Server

Per informazioni dettagliate sui prerequisiti precedenti, vedere **Configurare e creare un server** nell'articolo relativo a [come creare un'istanza di SQL Data Warehouse dal portale di Azure][].

> [AZURE.NOTE]  Per usare Azure PowerShell con SQL Data Warehouse, è necessario installare Azure PowerShell versione 1.0.3 o successiva. È possibile controllare la versione in uso eseguendo **Get-Module -ListAvailable -Name Azure**. È possibile installare la versione più recente usando [Installazione guidata piattaforma Web Microsoft][]. Per altre informazioni sull'installazione della versione più recente, vedere [Come installare e configurare Azure PowerShell][].

## Creare un database di SQL Data Warehouse
1. Aprire Windows PowerShell.
2. Eseguire questo cmdlet per accedere a Gestione risorse di Azure.

	```Powershell
	Login-AzureRmAccount
	```
	
3. Selezionare la sottoscrizione da usare per la sessione corrente.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  Creare il database. Questo esempio crea un nuovo database denominato "mynewsqldw", con un livello di obiettivo di servizio "DW400" nel server denominato "sqldwserver1" incluso nel gruppo di risorse "mywesteuroperesgp1". **NOTA: la creazione di un nuovo database SQL Data Warehouse potrebbe comportare nuovi costi in fattura. Per informazioni dettagliate sui prezzi, vedere [Prezzi di SQL Data Warehouse][].**

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

I parametri necessari per il cmdlet sono i seguenti:

- **RequestedServiceObjectiveName**: quantità di DWU richiesta, nel formato "DWXXX". Un'unità DWU rappresenta un'allocazione di memoria e CPU. Ogni valore DWU rappresenta un aumento lineare di queste risorse. I valori attualmente supportati sono: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
- **DatabaseName**: il nome dell'istanza di SQL Data Warehouse che si sta creando.
- **ServerName**: il nome del server che si sta usando per la creazione (deve essere V12).
- **ResourceGroupName**: il gruppo di risorse in uso. Per ottenere i gruppi di risorse disponibili nella sottoscrizione, usare Get-AzureResource.
- **Edition**: per creare un'istanza di SQL Data Warehouse, è necessario impostare l'edizione su "DataWarehouse".

Per informazioni dettagliate sulle opzioni dei parametri, vedere [Create Database (Azure SQL Data Warehouse)][]. Per informazioni di riferimento sui comandi, vedere [New-AzureRmSqlDatabase][]

## Passaggi successivi
Al termine del provisioning di SQL Data Warehouse, è possibile provare a [caricare dati di esempio][] o scoprire come eseguire le attività di [sviluppo][], [caricamento][] o [migrazione][].

Per altre informazioni su come gestire SQL Data Warehouse a livello di codice, vedere l'articolo relativo all'uso di [cmdlet di PowerShell e API REST][].

<!--Image references-->

<!--Article references-->
[migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppo]: sql-data-warehouse-overview-develop.md
[caricamento]: sql-data-warehouse-load-with-bcp.md
[caricare dati di esempio]: sql-data-warehouse-get-started-manually-load-samples.md
[cmdlet di PowerShell e API REST]: sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: sql-database-configure-firewall-settings.md
[Come installare e configurare Azure PowerShell]: powershell-install-configure.md
[come creare un'istanza di SQL Data Warehouse dal portale di Azure]: sql-data-warehouse-get-started-provision.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Installazione guidata piattaforma Web Microsoft]: https://aka.ms/webpi-azps
[Prezzi di SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
 

<!---HONumber=AcomDC_0427_2016-->