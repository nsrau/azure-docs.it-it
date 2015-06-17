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
	ms.date="04/20/2015" 
	ms.author="sidneyh"/>

# Come disinstallare i componenti del processo di database elastico

Se si verifica un errore durante il tentativo di installare il servizio processo di database elastico, eliminare il gruppo di risorse per il servizio.

## Per disinstallare i componenti del servizio

1. Aprire il [Portale di anteprima di Azure](https://ms.portal.azure.com/).
2. Passare alla sottoscrizione che contiene il processo elastico.
3. Fare clic su **Sfoglia** e fare clic su **Gruppi di risorse**.
4. Selezionare il gruppo di risorse denominato "__ElasticDatabaseJob".
5. Eliminare il gruppo di risorse.

In alternativa, utilizzare questo script di PowerShell:

1. Avviare una [finestra di Microsoft Azure PowerShell](../powershell-install-configure.md). 
2. Assicurarsi di utilizzare PowerShell SDK versione 0.8.10 o versione successiva.
3. Eseguire lo script:

		$ResourceGroupName = "__ElasticDatabaseJob"
		Switch-AzureMode AzureResourceManager
		
		$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
		if(!$resourceGroup)
		{
		    Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job is uninstalled."
		    return
		}
		
		Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
		Remove-AzureResourceGroup -Name $ResourceGroupName -Force
		Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job is now uninstalled."

## Passaggi successivi

Per reinstallare i processi di database elastici, vedere [Installazione del servizio processo di database elastico](sql-database-elastic-jobs-service-installation.md)

Per una panoramica del servizio processo elastico, vedere [Panoramica dei processi elastici](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-job-uninstall/

<!---HONumber=58--> 