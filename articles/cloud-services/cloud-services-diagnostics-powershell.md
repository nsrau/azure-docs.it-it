<properties 
	pageTitle="Abilitare la diagnostica nei servizi Cloud di Azure tramite PowerShell | Microsoft Azure" 
	description="Informazioni su come abilitare la diagnostica per i servizi cloud tramite PowerShell" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="sbtron" 
	manager="" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="saurabh"/>


# Abilitare la diagnostica nei servizi Cloud di Azure tramite PowerShell

È possibile raccogliere dati di diagnostica come log delle applicazioni, contatore delle prestazioni ecc. da un servizio Cloud mediante l'estensione Diagnostica di Azure. Questo articolo descrive come abilitare l'estensione Diagnostica di Azure per un servizio Cloud tramite PowerShell. Per i prerequisiti necessari per questo articolo, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

## Abilitare l'estensione delle funzionalità di diagnostica come parte della distribuzione di un servizio Cloud

Questo approccio è utile per il tipo di integrazione continua di scenari in cui può essere abilitata l'estensione della diagnostica. È possibile abilitare l'estensione della diagnostica come parte della distribuzione del servizio cloud passando per il parametro *ExtensionConfiguration* al cmdlet [New-AzureDeployment](https://msdn.microsoft.com/library/azure/mt589089.aspx). Il parametro *ExtensionConfiguration* accetta una matrice di configurazioni di diagnostica che può essere creata utilizzando il cmdlet [New AzureServiceDiagnosticsExtensionConfig](https://msdn.microsoft.com/library/azure/mt589168.aspx).

Nell'esempio seguente viene illustrato come abilitare la diagnostica per un servizio cloud con un WebRole e un WorkerRole, ciascuno dei quali dispone di una configurazione di diagnostica diversa.

	$service_name = "MyService"
	$service_package = "CloudService.cspkg"
	$service_config = "ServiceConfiguration.Cloud.cscfg"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey

	$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -Storage_context $storageContext -DiagnosticsConfigurationPath $webrole_diagconfigpath
	$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath
	  
	 
	New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig) 



## Abilitare l'estensione della diagnostica in un servizio Cloud esistente

È possibile usare il cmdlet [Set AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589140.aspx) per abilitare la diagnostica in un servizio Cloud che è già in esecuzione.


	$service_name = "MyService"
	$diagnostics_storagename = "myservicediagnostics"
	$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
	$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
	$primary_storagekey = (Get-AzureStorageKey -StorageAccountName "$diagnostics_storagename").Primary
	$storage_context = New-AzureStorageContext -StorageAccountName $diagnostics_storagename -StorageAccountKey $primary_storagekey
 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $webrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WebRole" 
	Set-AzureServiceDiagnosticsExtension -StorageContext $storage_context -DiagnosticsConfigurationPath $workerrole_diagconfigpath -ServiceName $service_name -Slot Production -Role "WorkerRole"
 

## Ottenere la configurazione di estensione della diagnostica corrente
Utilizzare il cmdlet [Get AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589204.aspx) per ottenere la configurazione di diagnostica corrente per un servizio cloud.
	
	Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"

## Rimuovere l'estensione della diagnostica
Per disattivare la diagnostica in un servizio cloud, è possibile utilizzare il cmdlet [Remove AzureServiceDiagnosticsExtension](https://msdn.microsoft.com/library/azure/mt589183.aspx).

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"

Se è stata abilitata l'estensione della diagnostica utilizzando *Set AzureServiceDiagnosticsExtension* o *New AzureServiceDiagnosticsExtensionConfig* senza il parametro *Ruolo*, allora è possibile rimuovere l'estensione utilizzando *Remove AzureServiceDiagnosticsExtension* senza il parametro *Ruolo*. Se il parametro *Ruolo* è stato utilizzato al momento di abilitare l'estensione, allora deve essere utilizzato anche quando si rimuove l'estensione.

Per rimuovere l'estensione della diagnostica da ogni singolo ruolo:

	Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"


## Passaggi successivi

- Per altre istruzioni sull'uso della diagnostica di Azure e di altre tecniche per la risoluzione dei problemi, vedere [Abilitazione della diagnostica in servizi Cloud di Azure e macchine virtuali](cloud-services-dotnet-diagnostics.md).
- Lo [Schema di configurazione della diagnostica](https://msdn.microsoft.com/library/azure/dn782207.aspx) illustra le varie opzioni di configurazione xml per l'estensione della diagnostica.
- Per informazioni su come abilitare l'estensione della diagnostica per le macchine virtuali, vedere [Creare una macchina virtuale Windows con monitoraggio e diagnostica mediante i modelli di Gestione risorse di Azure](virtual-machines-extensions-diagnostics-windows-template.md)  

<!---HONumber=Nov15_HO4-->