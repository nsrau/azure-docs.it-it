<properties 
	pageTitle="Gestire Ricerca di Azure con script di PowerShell | Microsoft Azure | Servizio di ricerca cloud ospitato" 
	description="Gestire il servizio Ricerca di Azure con script di PowerShell. Creare o aggiornare un servizio Ricerca di Azure e gestire le relative chiavi amministratore" 
	services="search" 
	documentationCenter="" 
	authors="seansaleh" 
	manager="mblythe" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="search" 
	ms.devlang="na" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="powershell" 
	ms.date="08/15/2016" 
	ms.author="seasa"/>

# Gestire il servizio Ricerca di Azure con PowerShell
> [AZURE.SELECTOR]
- [Portale](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

Questo argomento descrive i comandi di PowerShell che consentono di eseguire molte delle attività di gestione dei servizi di Ricerca di Azure. Illustreremo in dettaglio come creare un servizio di ricerca, come ridimensionarlo e come gestire le relative chiavi API. Questi comandi si affiancano alle opzioni di gestione disponibili nella pagina relativa alle [API REST di gestione di Ricerca di Azure](http://msdn.microsoft.com/library/dn832684.aspx).

## Prerequisiti
 
- È necessario disporre di Azure PowerShell 1.0 o versioni successive. Per istruzioni, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
- In PowerShell è necessario connettersi alla sottoscrizione di Azure come descritto di seguito.

In primo luogo, è necessario accedere a Microsoft Azure con questo comando:

	Login-AzureRmAccount

Specificare l'indirizzo e-mail del proprio account Microsoft Azure e la relativa password nella finestra di dialogo di accesso a Microsoft Azure.

In alternativa è possibile [accedere in modo non interattivo con un'entità servizio](../resource-group-authenticate-service-principal.md).

Se sono disponibili più sottoscrizioni di Azure, è necessario impostare la sottoscrizione di Azure in uso. Per visualizzare un elenco di sottoscrizioni correnti, eseguire questo comando.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Per specificare la sottoscrizione, eseguire il comando seguente. Nell'esempio seguente, il nome della sottoscrizione è `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## Comandi utili per iniziare

	$serviceName = "your-service-name-lowercase-with-dashes"
	$sku = "free" # or "basic" or "standard" for paid services
	$location = "West US"
	# You can get a list of potential locations with
	# (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
	$resourceGroupName = "YourResourceGroup" 
	# If you don't already have this resource group, you can create it with 
	# New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

	# Register the ARM provider idempotently. This must be done once per subscription
	Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search" -Force

	# Create a new search service
	# This command will return once the service is fully created
	New-AzureRmResourceGroupDeployment `
		-ResourceGroupName $resourceGroupName `
		-TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
		-NameFromTemplate $serviceName `
		-Sku $sku `
		-Location $location `
		-PartitionCount 1 `
		-ReplicaCount 1
	
	# Get information about your new service and store it in $resource
	$resource = Get-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
	
	# View your resource
	$resource
	
	# Get the primary admin API key
	$primaryKey = (Invoke-AzureRmResourceAction `
		-Action listAdminKeys `
		-ResourceId $resource.ResourceId `
		-ApiVersion 2015-08-19).PrimaryKey

	# Regenerate the secondary admin API Key
	$secondaryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action secondary).SecondaryKey

	# Create a query key for read only access to your indexes
	$queryKeyDescription = "query-key-created-from-powershell"
	$queryKey = (Invoke-AzureRmResourceAction `
		-ResourceType "Microsoft.Search/searchServices/createQueryKey" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19 `
		-Action $queryKeyDescription).Key
	
	# View your query key
	$queryKey

	# Delete query key
	Remove-AzureRmResource `
		-ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
		-ResourceGroupName $resourceGroupName `
		-ResourceName $serviceName `
		-ApiVersion 2015-08-19
		
	# Scale your service up
	# Note that this will only work if you made a non "free" service
	# This command will not return until the operation is finished
	# It can take 15 minutes or more to provision the additional resources
	$resource.Properties.ReplicaCount = 2
	$resource | Set-AzureRmResource
	
	# Delete your service
	# Deleting your service will delete all indexes and data in the service
	$resource | Remove-AzureRmResource
	
## Passaggi successivi
	
Dopo aver creato il servizio, è possibile eseguire i passaggi successivi: compilare un [indice](search-what-is-an-index.md), [eseguire query su un indice](search-query-overview.md) e infine creare e gestire le applicazioni di ricerca personali che usano Ricerca di Azure.

- [Creare un indice di Ricerca di Azure nel portale di Azure](search-create-index-portal.md)

- [Eseguire query in un indice di Ricerca di Azure con Esplora ricerche nel portale di Azure](search-explorer.md)

- [Installare un indicizzatore per caricare dati da altri servizi](search-indexer-overview.md)

- [Come utilizzare Ricerca di Azure in .NET](search-howto-dotnet-sdk.md)

- [Analizzare il traffico di Ricerca di Azure](search-traffic-analytics.md)

<!---HONumber=AcomDC_0817_2016-->