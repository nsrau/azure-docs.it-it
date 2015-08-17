<properties 
 pageTitle="Gestire Cache Redis di Azure con Azure PowerShell" 
 description="Informazioni su come eseguire attività amministrative per Cache Redis di Azure con Azure PowerShell." 
 services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="multiple" 
   ms.date="08/04/2015"
   ms.author="riande"/>

# Gestire Cache Redis di Azure con Azure PowerShell

Questa esercitazione fornisce informazioni di avvio rapido per creare, aggiornare ed eliminare Cache Redis di Azure.

## Prerequisiti ##

Per usare Windows PowerShell con Gestione risorse, è necessario disporre dei programmi seguenti:

- Windows PowerShell, versione 3.0 o 4.0. Per individuare la versione di Windows PowerShell, digitare `$PSVersionTable` e verificare che il valore di `PSVersion` sia 3.0 o 4.0. Per installare una versione compatibile, vedere [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).
	
- Azure PowerShell 0.8.0 o versione successiva. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Questa esercitazione è rivolta a utenti non esperti di Windows PowerShell. Per altre informazioni su Windows PowerShell, vedere [Introduzione a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Per informazioni dettagliate sui cmdlet usati in questa esercitazione, eseguire il cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Ad esempio, per informazioni sul cmdlet Add-AzureAccount, digitare:

	Get-Help Add-AzureAccount -Detailed

## Semplice script di Azure PowerShell per Cache Redis  ##

Lo script seguente illustra come creare, aggiornare ed eliminare Cache Redis di Azure.

		# Redis cache operations require mode set to AzureResourceManager.
		Switch-AzureMode AzureResourceManager
		$VerbosePreference = "Continue" 
		
		# Create a new cache.
		$cacheName = "MovieCache91117"
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
			$cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {       
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName         
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -MaxMemoryPolicy AllKeysLRU -Name $cacheName -ResourceGroupName $resourceGroupName
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force 

## Passaggi successivi

Per ulteriori informazioni sull'utilizzo di Windows PowerShell con Azure, vedere le risorse seguenti:
 
- [Cmdlet di Gestione risorse di Azure](http://go.microsoft.com/fwlink/?LinkID=394765): informazioni sull'uso dei cmdlet nel modulo AzureResourceManager.
- [Uso dei gruppi di risorse per la gestione delle risorse di Azure](../azure-portal/resource-group-portal): informazioni su come creare e gestire gruppi di risorse nel portale di gestione di Azure.
- [Blog di Azure](http://blogs.msdn.com/windowsazure): informazioni sulle nuove funzionalità di Azure.
- [Blog di Windows PowerShell](http://blogs.msdn.com/powershell): informazioni sulle nuove funzionalità di Windows PowerShell.
- [Blog "Hey, Scripting Guy!"](http://blogs.technet.com/b/heyscriptingguy/): suggerimenti e consigli basati sull'esperienza dei membri della community di Windows PowerShell.

<!---HONumber=August15_HO6-->