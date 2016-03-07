<properties 
	pageTitle="Come disinstallare lo strumento processo di database elastico" 
	description="Come disinstallare lo strumento processo di database elastico" 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="ddove; sidneyh"/>

#Disinstallare i componenti dei processi di database elastici
I componenti dei**Processi di database elastici** possono essere disinstallati utilizzando il portale o PowerShell.

##Disinstallare i componenti dei processi di database elastici utilizzando il portale di Azure

1. Aprire il [portale di Azure](https://ms.portal.azure.com/).
2. Passare alla sottoscrizione che contiene i componenti dei **processi di database elastici**, vale a dire la sottoscrizione in cui i componenti dei processi di database elastici sono stati installati.
3. Fare clic su **Sfoglia** e fare clic su **Gruppi di risorse**.
4. Selezionare il gruppo di risorse denominato "\_\_ElasticDatabaseJob".
5. Eliminare il gruppo di risorse.

##Disinstallare i componenti dei processi di database elastici utilizzando PowerShell

1.	Avviare una finestra di comando di Microsoft Azure PowerShell e passare alla sottodirectory strumenti nella cartella Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: digitare **cd strumenti**.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2.	Eseguire lo script .\\UninstallElasticDatabaseJobs.ps1 di PowerShell.

		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1
		PS C:*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

O semplicemente, eseguire lo script seguente, supponendo che i valori predefiniti se utilizzati nell'installazione dei componenti:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## Passaggi successivi

Per reinstallare i processi di database elastici, vedere [Installazione del servizio processo di database elastico](sql-database-elastic-jobs-service-installation.md)

Per ulteriori informazioni sui processi dei database elastici, vedere [Panoramica dei processi di database elastici](sql-database-elastic-jobs-overview.md).

<!--Image references-->

<!---HONumber=AcomDC_0224_2016-->