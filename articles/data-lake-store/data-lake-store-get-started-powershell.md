<properties
   pageTitle="Introduzione a Data Lake Store | Azure"
   description="Usare Azure PowerShell per creare un account di Data Lake Store ed eseguire operazioni di base"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Introduzione all'archivio Azure Data Lake mediante Azure PowerShell

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [SDK per Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

Informazioni su come usare Azure PowerShell per creare un account di Azure Data Lake Store ed eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare i file di dati, eliminare l'account e così via. Per altre informazioni su Data Lake Store, vedere [Panoramica di Data Lake](data-lake-store-overview.md).

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup).


##Installare Azure PowerShell 1.0 o versioni successive

Vedere la sezione Prerequisiti in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md#prerequisites).

## Creare un account di Azure Data Lake Store

1. Dal desktop aprire una nuova finestra di Azure PowerShell e immettere il seguente frammento per accedere al proprio account di Azure, impostare la sottoscrizione e registrare il provider di Data Lake Store. Quando viene richiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

        # Log in to your Azure account
		Login-AzureRmAccount

		# List all the subscriptions associated to your account
		Get-AzureRmSubscription

		# Select a subscription
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Azure Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. Un account di Archivio Azure Data Lake è associato a un gruppo di risorse di Azure. Per iniziare, creare un gruppo di risorse di Azure.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Creare un gruppo di risorse di Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Creare un gruppo di risorse di Azure")

2. Creare un account di Azure Data Lake Store. Il nome specificato deve contenere solo lettere minuscole e numeri.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Creare un account di Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Creare un account di Azure Data Lake Store")

3. Verificare che l'account sia stato creato correttamente.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	L'output di questa operazione deve essere **True**.

## Creare strutture di directory in Azure Data Lake Store

È possibile creare una directory con il proprio account di Azure Data Lake Store per gestire e archiviare i dati.

1. Specificare una directory radice.

		$myrootdir = "/"

2. Creare una nuova directory denominata **mynewdirectory** sotto la radice specificata.

		New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Verificare che la nuova directory sia stata creata correttamente.

		Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

	Verrà visualizzato un output simile al seguente:

	![Verificare la directory](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificare la directory")


## Caricare dati in Azure Data Lake Store

È possibile caricare i dati in Data Lake Store direttamente a livello di radice o in una directory creata all'interno dell'account. I frammenti di codice riportati di seguito illustrano come caricare alcuni dati di esempio nella directory (**mynewdirectory**) creata nella sezione precedente.

Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [repository Git di Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Scaricare il file e archiviarlo in una directory locale nel computer, ad esempio C:\\sampledata.

	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## Rinominare, scaricare ed eliminare i dati da Data Lake Store

Per rinominare un file, usare il comando seguente:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Per scaricare un file, usare il comando seguente:

	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Per eliminare un file, usare il comando seguente:

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando viene richiesto, immettere **Y** per eliminare l'elemento. Se sono presenti più file da eliminare, è possibile fornire tutti i percorsi separati da una virgola.

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## Eliminare l'account di Azure Data Lake Store

Usare il comando seguente per eliminare l'account di Data Lake Store.

	Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quando viene richiesto, immettere **Y** per eliminare l'account.


## Passaggi successivi

- [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
- [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0413_2016-->