<properties 
   pageTitle="Introduzione ad Azure Data Lake Analytics con Azure PowerShell | Azure" 
   description="Informazioni su come usare Azure PowerShell per creare un account di Data Lake Store, creare un processo di Data Lake Analytics mediante U-SQL e inviare il processo." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/21/2016"
   ms.author="edmaca"/>

# Esercitazione: Introduzione ad Azure Data Lake Analytics con Azure PowerShell

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Informazioni su come usare Azure PowerShell per creare gli account di Azure Data Lake Analytics, definire i processi di Data Lake Analytics in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi agli account di Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

In questa esercitazione si svilupperà un processo che legge un file di valori separati da tabulazioni (TSV) e lo converte in un file di valori separati da virgole (CSV). Per eseguire la stessa esercitazione usando altri strumenti supportati, fare clic sulle schede nella parte superiore di questa sezione.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Workstation con Azure PowerShell**. Vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
	
##Creare un account di Analisi Data Lake

È necessario disporre di un account di Data Lake Analytics prima di poter eseguire qualsiasi processo. Per creare un account di Data Lake Analytics, è necessario specificare quanto segue:

- **Gruppo di risorse di Azure**: è necessario creare un account di Data Lake Analytics all'interno di un gruppo di risorse di Azure. [Gestione risorse di Azure](../resource-group-overview.md) consente di lavorare con le risorse dell'applicazione come gruppo. È quindi possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione mediante un'unica operazione coordinata.  

	Per elencare i gruppi di risorse nella sottoscrizione:
    
    	Get-AzureRmResourceGroup
    
	Per creare un nuovo gruppo di risorse:

    	New-AzureRmResourceGroup `
			-Name "<Your resource group name>" `
			-Location "<Azure Data Center>" # For example, "East US 2"

- **Nome dell'account di Data Lake Analytics**
- **Posizione**: uno dei data center di Azure che supporta Data Lake Analytics.
- **Account di Data Lake predefinito**: ogni account di Data Lake dispone di un account di Data Lake predefinito.

	Per creare un nuovo account di Data Lake:

	    New-AzureRmDataLakeStoreAccount `
	        -ResourceGroupName "<Your Azure resource group name>" `
	        -Name "<Your Data Lake account name>" `
	        -Location "<Azure Data Center>"  # For example, "East US 2"

	> [AZURE.NOTE] Il nome dell'account di Data Lake deve contenere solo lettere minuscole e numeri.



**Per creare un account di Data Lake Analytics**

1. Dalla workstation Windows aprire Windows PowerShell ISE.
2. Eseguire lo script seguente:

		$resourceGroupName = "<ResourceGroupName>"
		$dataLakeStoreName = "<DataLakeAccountName>"
		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$location = "East US 2"
		
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
			-Name $dataLakeAnalyticsName `
			-ResourceGroupName $resourceGroupName `
			-Location $location `
			-DefaultDataLake $dataLakeStoreName
		
		Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
		Get-AzureRmDataLakeAnalyticsAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeAnalyticsName  

##Caricare dati in Data Lake

In questa esercitazione verranno elaborati alcuni log di ricerca. Il log di ricerca può essere archiviato in Data Lake Store o in un archivio BLOB di Azure.

Un file del log di ricerca di esempio è stato copiato in un contenitore BLOB di Azure pubblico. Usare il seguente script di PowerShell per scaricare il file nella workstation in uso e quindi caricare il file nell'account di Data Lake Store predefinito dell'account di Data Lake Analytics.

	$dataLakeStoreName = "<The default Data Lake Store account name>"
	
	$localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
	$storageAccount = "adltutorials"  # Don't modify this value.
	$container = "adls-sample-data"  #Don't modify this value.

	# Create the temp location	
	New-Item -Path $localFolder -ItemType Directory -Force 

	# Download the sample file from Azure Blob storage
	$context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
	$blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
	$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

	# Upload the file to the default Data Lake Store account	
	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Lo script di PowerShell seguente mostra come ottenere il nome dell'archivio predefinito di Data Lake per un account di Data Lake Analytics:


	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

>[AZURE.NOTE] Il portale di Azure fornisce un'interfaccia utente per copiare i file di dati di esempio nell'account di Data Lake Store predefinito. Per istruzioni, vedere [Introduzione ad Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Data Lake Analytics può inoltre accedere all'archivio BLOB di Azure. Per caricare i dati nell'archivio BLOB di Azure, vedere [Uso di Azure PowerShell con Archiviazione di Azure](../storage/storage-powershell-guide-full.md).

##Inviare processi di Data Lake Analytics

I processi di Data Lake Analtyics vengono scritti nel linguaggio U-SQL. Per altre informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL](data-lake-analytics-u-sql-get-started.md) e [Informazioni di riferimento sul linguaggio U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Per creare uno script per il processo di Data Lake Analytics**

- Creare un file di testo contenente il seguente script U-SQL e salvare il file di testo nella workstation in uso:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Questo script U-SQL legge il file dei dati di origine mediante **Extractors.Tsv()** e quindi crea un file con estensione csv mediante **Outputters.Csv()**.
    
    Non modificare i due percorsi, a meno che il file di origine non sia stato copiato in una posizione diversa. Data Lake Analytics creerà la cartella di output, se non esiste già.
	
	Risulta più semplice usare i percorsi relativi dei file archiviati negli account predefiniti di Data Lake, ma è possibile usare anche percorsi assoluti. Ad esempio
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    È necessario usare percorsi assoluti per accedere ai file presenti negli account di archiviazione collegati. La sintassi dei file presenti in un account di Archiviazione di Azure collegato è:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Non sono attualmente supportate autorizzazioni di accesso a contenitori pubblici o a contenitori BLOB di Azure con BLOB pubblici.
    
	
**Per inviare il processo**

1. Dalla workstation Windows aprire Windows PowerShell ISE.
2. Eseguire lo script seguente:

		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
		
		Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 
		                
		While (($t = Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId).State -ne "Ended"){
			Write-Host "Job status: "$t.State"..."
			Start-Sleep -seconds 5
		}
		
		Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

	Nello script il file di script U-SQL viene archiviato in c:\\tutorials\\data-lake-analytics\\copyFile.usql. Aggiornare il percorso del file di conseguenza.
 
Dopo il completamento del processo, è possibile usare i cmdlet seguenti per visualizzare l'elenco di file e scaricare il file:
	
	$resourceGroupName = "<Resource Group Name>"
	$dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
	$destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
	
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
	
	Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
	
	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## Vedere anche

- Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
- Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Per iniziare a sviluppare applicazioni U-SQL, vedere [Sviluppare script U-SQL tramite Strumenti di Data Lake per Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Analisi Data Lake di Azure](data-lake-analytics-u-sql-get-started.md).
- Per informazioni sulle attività di gestione, vedere [Gestire Analisi di Azure Data Lake tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).
- Per una panoramica su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0427_2016-->