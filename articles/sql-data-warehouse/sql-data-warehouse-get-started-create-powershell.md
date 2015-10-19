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
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/05/2015"
   ms.author="lodipalm"/>

# Creare SQL Data Warehouse mediante PowerShell

> [AZURE.NOTE]Per usare Microsoft Azure PowerShell con SQL Data Warehouse, è necessario disporre della versione 0.9.4 o versione successiva. È possibile controllare la versione eseguendo (Get-Module Azure).Version in PowerShell.

## Ottenere ed eseguire i cmdlet di Azure PowerShell
Se non si dispone già di una configurazione con PowerShell, è possibile:

1. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Per eseguire il modulo, nella finestra di avvio digitare **Microsoft Azure PowerShell**.
3. Se non si è ancora provveduto ad aggiungere il proprio account al computer, eseguire il cmdlet seguente. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell][].

            Add-AzureAccount

4. Sarà anche necessario eseguire PowerShell in modalità ARM. È possibile passare a questa modalità eseguendo il comando seguente:

            switch-azuremode AzureResourceManager

## Creazione di SQL Data Warehouse
Dopo aver verificato che PowerShell è correttamente configurato nell'account, è possibile eseguire il comando seguente per distribuire una nuova istanza di SQL Data Wareouse:

        New-AzureSqlDatabase -RequestedServiceObjectiveName "<Service Objective>" -DatabaseName "<Data Warehouse Name>" -ServerName "<Server Name>" -ResourceGroupName "<ResourceGroupName>" -Edition "DataWarehouse"

I parametri necessari per questo cmdlet sono i seguenti:

 + **RequestedServiceObjectiveName**: la quantità di DWU richiesta, nel formato "DWXXX". I valori attualmente supportati sono: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
 + **DatabaseName**: il nome dell'istanza di SQL Data Warehouse che si sta creando.
 + **ServerName**: il nome del server che si sta usando per la creazione (deve essere V12).
 + **ResourceGroupName**: il gruppo di risorse in uso. Per ottenere i gruppi di risorse disponibili nella sottoscrizione, usare Get-AzureResource.
 + **Edition**: per creare un'istanza di SQL Data Warehouse, è necessario impostare l'edizione su "DataWarehouse". 

## Passaggi successivi
Al termine del provisioning del SQL Data Warehouse, è possibile [caricare dati di esempio][] o vedere come [sviluppare][], [caricare][] o [eseguire la migrazione][].

Per altre informazioni su come gestire SQL Data Warehouse a livello di codice, consultare la documentazione relativa a [PowerShell][] o all'[API REST][].



<!--Image references-->

<!--Article references-->
[eseguire la migrazione]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-migrate/
[sviluppare]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-develop/
[caricare]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-overview-load/
[caricare dati di esempio]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Powershell]: https://azure.microsoft.com/it-IT/documentation/articles/sql-data-warehouse-reference-powershell-cmdlets/
[API REST]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[MSDN]: https://msdn.microsoft.com/it-IT/library/azure/dn546722.aspx
[firewall rules]: https://azure.microsoft.com/it-IT/documentation/articles/sql-database-configure-firewall-settings/
[Come installare e configurare Azure PowerShell]: powershell-install-configure.md

<!---HONumber=Oct15_HO2-->