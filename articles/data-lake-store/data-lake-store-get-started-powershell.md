---
title: Usare PowerShell per iniziare a usare Azure Data Lake Store | Documentazione Microsoft
description: Usare Azure PowerShell per creare un account di Data Lake Store ed eseguire operazioni di base
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 31c98b930ccb8203316e3a3b13c3c0dc7d74dd9c
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introduzione all'archivio Azure Data Lake mediante Azure PowerShell
> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfaccia della riga di comando di Azure 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

Informazioni su come usare Azure PowerShell per creare un account di Azure Data Lake Store ed eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare i file di dati, eliminare l'account e così via. Per altre informazioni su Data Lake Store, vedere [Panoramica di Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
Questo articolo usa un approccio di autenticazione più semplice con Data Lake Store con cui viene richiesto di immettere le credenziali del proprio account di Azure. Il livello di accesso al file system e all'account Data Lake Store viene quindi regolato dal livello di accesso dell'utente connesso. Esistono tuttavia altri approcci oltre all'autenticazione con Data Lake Store, ad esempio l'**autenticazione dell'utente finale** o l'**autenticazione da servizio a servizio**. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Creare un account di Azure Data Lake Store
1. Dal desktop aprire una nuova finestra di Windows PowerShell. Immettere il frammento di codice seguente per accedere al proprio account di Azure, impostare la sottoscrizione e registrare il provider di Data Lake Store. Quando viene richiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

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

    ![Creare un gruppo di risorse di Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")
3. Creare un account Archivio Azure Data Lake. Il nome specificato deve contenere solo lettere minuscole e numeri.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Creare un account di Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")
4. Verificare che l'account sia stato creato correttamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    L'output del cmdlet dovrebbe essere **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Creare strutture di directory in Azure Data Lake Store
È possibile creare una directory con il proprio account di Azure Data Lake Store per gestire e archiviare i dati.

1. Specificare una directory radice.

        $myrootdir = "/"
2. Creare una nuova directory denominata **mynewdirectory** sotto la radice specificata.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Verificare che la nuova directory sia stata creata correttamente.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Dovrebbe venire visualizzato un output simile a quello dello screenshot seguente:

    ![Verificare la directory](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Caricare dati in Azure Data Lake Store
È possibile caricare i dati in Data Lake Store direttamente a livello di radice o in una directory creata all'interno dell'account. I frammenti di codice in questa sezione illustrano come caricare alcuni dati di esempio nella directory (**mynewdirectory**) creata nella sezione precedente.

Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Scaricare il file e archiviarlo in una directory locale nel computer, ad esempio C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Rinominare, scaricare ed eliminare i dati da Data Lake Store
Per rinominare un file, usare il comando seguente:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Per scaricare un file, usare il comando seguente:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Per eliminare un file, usare il comando seguente:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando viene richiesto, immettere **Y** per eliminare l'elemento. Se sono presenti più file da eliminare, è possibile fornire tutti i percorsi separati da una virgola.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Eliminare l'account di Azure Data Lake Store
Usare il comando seguente per eliminare l'account di Data Lake Store.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quando viene richiesto, immettere **Y** per eliminare l'account.

## <a name="next-steps"></a>Passaggi successivi
* [Linee guida per l'ottimizzazione delle prestazioni per l'uso di PowerShell con Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
* [Usare Azure Data Lake Store per i requisiti di Big Data](data-lake-store-data-scenarios.md) 
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)