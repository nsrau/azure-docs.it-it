<properties 
   pageTitle="Gestire Azure Data Lake Analytics tramite Azure PowerShell | Azure" 
   description="Informazioni su come gestire processi, origini dati e utenti di Data Lake Analytics." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/01/2015"
   ms.author="jgao"/>

# Gestire Azure Data Lake Analytics tramite Azure PowerShell

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Informazioni su come gestire gli account, le origini dati, gli utenti e i processi di Azure Data Lake Analytics tramite Azure PowerShell. Per visualizzare gli argomenti relativi alla gestione tramite altri strumenti, fare clic sul selettore di scheda riportato sopra.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- Una **sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure]https://azure.microsoft.com/it-IT/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


##Installare Azure PowerShell 1.0 e versioni successive

È innanzitutto necessario disinstallare le versioni 0.9x.

Per controllare la versione di PowerShell installata:

	Get-Module *azure*
	
Per disinstallare la versione precedente, eseguire Programmi e Funzionalità nel Pannello di controllo.

Sono disponibili due opzioni principali per l'installazione di Azure PowerShell.

- [Raccolta di PowerShell](https://www.powershellgallery.com/). Dalla console di Windows PowerShell con privilegi elevati o di PowerShell ISE con privilegi elevati, eseguire i comandi seguenti:

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM
		
		# Import Azure Service Management module
		Import-Module Azure

	Per altre informazioni, vedere [Raccolta di PowerShell](https://www.powershellgallery.com/)

- [Installazione guidata della piattaforma Web Microsoft (WebPI)](http://aka.ms/webpi-azps). Se si dispone di Azure PowerShell 0.9.x installata, verrà richiesto di disinstallare 0.9.x . Se sono stati installati dei moduli Azure PowerShell dalla raccolta di PowerShell, il programma di installazione richiede che i moduli vengano rimossi prima dell'installazione per garantire un ambiente di PowerShell Azure coerente. Per istruzioni, vedere [Come installare Azure PowerShell 1.0 tramite WebPI](https://azure.microsoft.com/blog/azps-1-0/).

WebPI riceverà degli aggiornamenti mensili. La Raccolta di PowerShell riceverà degli aggiornamenti su base continua. Se si ha familiarità con l'installazione dalla raccolta di PowerShell, quello sarà il primo canale per le ultimissime novità in Azure PowerShell.

**Per visualizzare l'elenco dei cmdlet**:

	Get-Command *Azure*DataLakeAnalytics*

**Per connettersi ad Azure, usare i cmdlet seguenti**:

	Login-AzureRmAccount
	Get-AzureRmSubscription  # for finding the Azure Subscription ID
	Set-AzureRmContext -SubscriptionID <Azure Subscription ID>
	
## Gestire account

Prima di eseguire qualsiasi processo di Analisi Data Lake, è necessario disporre di un account di Analisi Data Lake. A differenza di Azure HDInsight, un account di Analisi non è soggetto ad alcun pagamento fino a quando il processo non è in esecuzione. Viene infatti richiesto il pagamento solo per la durata di esecuzione di un processo. Per altre informazioni, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

###Creare account

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeStoreName = "<DataLakeAccountName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$location = "<Microsoft Data Center>"
	
	Write-Host "Create a resource group ..." -ForegroundColor Green
	New-AzureRmResourceGroup `
		-Name  $resourceGroupName `
		-Location $location
	
	Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
	New-AzureRmDataLakeStoreAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeStoreName `
		-Location $location 
	
	Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
	New-AzureRmDataLakeAnalyticsAccount `
		-Name $dataLakeAnalyticsAccountName `
		-ResourceGroupName $resourceGroupName `
		-Location $location `
		-DefaultDataLake $dataLakeStoreName
	
	Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
	Get-AzureRmDataLakeAnalyticsAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeAnalyticsAccountName  

È inoltre possibile usare un modello di Gruppo di risorse di Azure. Un modello per la creazione di un account di Data Lake Analytics e dell'account di Data Lake Store dipendente è descritto nell'[appendice A](#appendix-a). Salvare il modello in un file con estensione JSON e quindi usare il seguente script di PowerShell per chiamarlo:


	$AzureSubscriptionID = "<Your Azure Subscription ID>"
	
	$ResourceGroupName = "<New Azure Resource Group Name>"
	$Location = "EAST US 2"
	$DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
	$DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
	
	$DeploymentName = "MyDataLakeAnalyticsDeployment"
	$ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
	
	Login-AzureRmAccount
	
	Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
	
	# Create the resource group
	New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
	
	# Create the Data Lake Analytics account with the default Data Lake Store account.
	$parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
	New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###Elencare gli account

Elencare gli account di Data Lake Analytics all'interno della sottoscrizione corrente

	Get-AzureRmDataLakeAnalyticsAccount
	
L'output è:

	Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
	Location   : eastus2
	Name       : learn1021adla
	Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
	Tags       : {}
	Type       : Microsoft.DataLakeAnalytics/accounts

Elencare gli account di Data Lake Analytics all'interno di un gruppo di risorse specifico

	Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Ottenere i dettagli di un account specifico di Data Lake Analytics

	Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Verificare l'esistenza di un account specifico di Data Lake Analytics

	Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Il cmdlet restituirà **True** oppure **False**.

###Eliminare gli account di Data Lake Analytics

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

L'eliminazione di un account di Analytics non comporta l'eliminazione dell'account di archiviazione dipendente di Data Lake. Nell'esempio seguente vengono eliminati l'account di Data Lake Analytics e l'account di Data Lake Store predefinito

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

	Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName 
	Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## Gestire le origini dati degli account

Data Lake Analytics attualmente supporta le seguenti origini dati:

- [Archiviazione di Azure Data Lake](data-lake-storage-overview.md)
- [Archiviazione di Azure](storage-introduction.md)

Quando si crea un account di Analytics, è necessario impostare un account di archiviazione di Azure Data Lake come account di archiviazione predefinito. L'account di Data Lake Store predefinito viene usato per archiviare i metadati e i log di controllo dei processi. Dopo aver creato un account di Analytics, è possibile aggiungere altri account di archiviazione di Data Lake e/o account di archiviazione di Azure.

### Trovare l'account di Data Lake Store predefinito

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount


### Aggiungere altri account di archiviazione BLOB di Azure

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureStorageAccountName = "<AzureStorageAccountName>"
	$AzureStorageAccountKey = "<AzureStorageAccountKey>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -AccountName $dataLakeAnalyticName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### Aggiungere altri account di Data Lake Store

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureDataLakeName = "<DataLakeStoreName>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -AccountName $dataLakeAnalyticName -DataLake $AzureDataLakeName 

### Elencare le origini dati:

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DataLakeStoreAccounts
	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.StorageAccounts
	


<!-- ################################ -->
<!-- ################################ -->
## Gestire i processi

È necessario disporre di un account di Data Lake Analytics prima di poter creare un processo. Per altre informazioni, vedere [Gestire gli account di Analisi Data Lake](#manage-data-lake-analytics-accounts).

### Elencare i processi

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -State Running, Queued
	#States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Result Cancelled
	#Results: Cancelled, Failed, None, Successed 
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Name <Job Name>
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Submitter <Job submitter>

	# List all jobs submitted on January 1 (local time)
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-SubmittedAfter "2015/01/01"
		-SubmittedBefore "2015/01/02"	

	# List all jobs that succeeded on January 1 after 2 pm (UTC time)
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-State Ended
		-Result Succeeded
		-SubmittedAfter "2015/01/01 2:00 PM -0"
		-SubmittedBefore "2015/01/02 -0"

	# List all jobs submitted in the past hour
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-SubmittedAfter (Get-Date).AddHours(-1)

### Ottenere i dettagli dei processi

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -JobID <Job ID>
	
### Inviare i processi

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	#Pass script via path
	Submit-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-Name $jobName `
		-ScriptPath $scriptPath

	#Pass script contents
	Submit-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-Name $jobName `
		-Script $scriptContents

> [AZURE.NOTE]La priorità predefinita di un processo è 1000 e il livello predefinito di parallelismo per un processo è 1.


### Annullare i processi

	Stop-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-JobID $jobID


## Gestire gli elementi del catalogo

Il catalogo di U-SQL viene usato per definire la struttura dei dati e del codice in modo da poterli condividere mediante U-SQL. Il catalogo consente di ottenere le migliori prestazioni possibili con i dati in Azure Data Lake. Per altre informazioni, vedere [Usare il catalogo di U-SQL](data-lake-analytics-use-u-sql-catalog.md).

###Elencare gli elementi del catalogo

	#List databases
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database
	
	
	
	#List tables
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo"

###Ottenere i dettagli degli elementi del catalogo 

	#Get a database
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"
	
	#Get a table
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo.mytable"

###Verificare l'esistenza dell'elemento del catalogo

	Test-AzureRmDataLakeAnalyticsCatalogItem  `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"

###Creare il segreto del catalogo
	New-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")

### Modificare il segreto del catalogo
	Set-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")



###Eliminare il segreto del catalogo
	Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master"


## Usare i gruppi Gestione risorse di Azure

Le applicazioni sono in genere costituite da molti componenti, ad esempio app Web, database, server di database, risorsa di archiviazione e servizi di terze parti. Gestione risorse di Azure (ARM) consente di usare le risorse dell'applicazione come gruppo, detto Gruppo di risorse di Azure. È quindi possibile distribuire, aggiornare, monitorare o eliminare tutte le risorse per l'applicazione con una singola operazione coordinata. È possibile descrivere le risorse del gruppo in un modello JSON per la distribuzione e quindi usare tale modello per ambienti diversi, ad esempio di testing, staging e produzione. È possibile chiarire la fatturazione per l'organizzazione visualizzando i costi per l'intero gruppo. Per altre informazioni, vedere [Panoramica di Gestione risorse di Azure](resource-group-overview.md).

Un servizio di Data Lake Analtyics può includere i seguenti componenti:

- Account di Azure Data Lake Analytics
- Account di archiviazione predefinito obbligatorio di Azure Data Lake
- Account di archiviazione aggiuntivi di Azure Data Lake
- Account di archiviazione aggiuntivi di Azure

È possibile creare tutti questi componenti in un unico gruppo ARM per semplificarne la gestione.

![Account e archiviazione di Azure Data Lake Analytics](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Un account di Data Lake Analytics e gli account di archiviazione dipendenti devono trovarsi nello stesso data center di Azure. Il gruppo ARM può tuttavia trovarsi in un data center diverso.

##Vedere anche 

- [Panoramica di Analisi Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introduzione a Analisi Data Lake tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
- [Gestire Analisi Data Lake di Azure tramite il portale di Azure](data-lake-analytics-use-portal.md)
- [Monitorare e risolvere i problemi dei processi di Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##Appendice A - Modello ARM di Data Lake Analytics

Il seguente modello ARM può essere usato per distribuire un account di Data Lake Analytics e il relativo account di Data Lake Store dipendente. Salvarlo come file con estensione JSON e quindi usare uno script di PowerShell per chiamare il modello. Per altre informazioni, vedere [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md#deploy-with-powershell) e [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

	{
		"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"adlAnalyticsName": {
				"type": "string",
				"metadata": {
					"description": "The name of the Data Lake Analytics account to create."
				}
			},
			"adlStoreName": {
				"type": "string",
				"metadata": {
					"description": "The name of the Data Lake Store account to create."
				}
			}
		},
		"resources": [{
			"name": "[parameters('adlAnalyticsName')]",
			"type": "Microsoft.DataLakeAnalytics/accounts",
			"location": "East US 2",
			"apiVersion": "2015-10-01-preview",
			"dependsOn": ["[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]"],
			"tags": {
				
			},
			"properties": {
				"defaultDataLakeAccount": "[parameters('adlStoreName')]"
				}
			}
		},
		{
			"name": "[parameters('adlName')]",
			"type": "Microsoft.DataLakeStore/accounts",
			"location": "East US 2",
			"apiVersion": "2015-10-01-preview",
			"dependsOn": [],
			"tags": {
				
			}
		}],
		"outputs": {
			"adlAnalyticsAccount": {
				"type": "object",
				"value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
			},
			"adlStoreAccount": {
				"type": "object",
				"value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
			}
		}
	}

<!---HONumber=AcomDC_1203_2015-->