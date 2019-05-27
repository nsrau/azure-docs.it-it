---
title: Iniziare a usare Azure Data Lake Store Gen1 tramite PowerShell | Microsoft Docs
description: Usare Azure PowerShell per creare un account Azure Data Lake Storage Gen1 ed eseguire operazioni di base
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 5bec627f114a20033ca4364c39c048763df36b67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161440"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Iniziare a usare Azure Data Lake Storage Gen1 tramite Azure PowerShell
> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Questo articolo illustra come usare Azure PowerShell per creare un account Azure Data Lake Storage Gen1 ed eseguire operazioni di base come creare cartelle, caricare e scaricare file di dati, eliminare l'account e così via. Per altre informazioni su Data Lake Storage Gen1, vedere [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
Questo articolo usa un approccio di autenticazione più semplice con Data Lake Storage Gen1 in cui viene chiesto di immettere le credenziali dell'account Azure. Il livello di accesso all'account Data Lake Storage Gen1 e al file system viene quindi controllato dal livello di accesso dell'utente connesso. Ci sono tuttavia altri approcci oltre all'autenticazione con Data Lake Storage Gen1, ad esempio l'**autenticazione dell'utente finale** o l'**autenticazione da servizio a servizio**. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Creare un account Data Lake Storage Gen1
1. Dal desktop aprire una nuova finestra di Windows PowerShell. Immettere il frammento di codice seguente per accedere all'account Azure, impostare la sottoscrizione e registrare il provider di Data Lake Storage Gen1. Quando viene chiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Un account Data Lake Storage Gen1 è associato a un gruppo di risorse di Azure. Per iniziare, creare un gruppo di risorse di Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Creare un gruppo di risorse di Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")
3. Creare un account Data Lake Storage Gen1. Il nome specificato deve contenere solo lettere minuscole e numeri.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Creare un account Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Creare un account Data Lake Storage Gen1")
4. Verificare che l'account sia stato creato correttamente.

        Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    L'output del cmdlet dovrebbe essere **True**.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Creare strutture di directory nell'account Data Lake Storage Gen1
È possibile creare directory nell'account Data Lake Storage Gen1 per gestire e archiviare i dati.

1. Specificare una directory radice.

        $myrootdir = "/"
2. Creare una nuova directory denominata **mynewdirectory** sotto la radice specificata.

        New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Verificare che la nuova directory sia stata creata correttamente.

        Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Dovrebbe venire visualizzato un output simile a quello dello screenshot seguente:

    ![Verificare la directory](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Caricare i dati nell'account Data Lake Storage Gen1
È possibile caricare i dati in Data Lake Storage Gen1 direttamente al livello radice o in una directory creata nell'account. I frammenti di codice in questa sezione illustrano come caricare alcuni dati di esempio nella directory (**mynewdirectory**) creata nella sezione precedente.

Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Scaricare il file e archiviarlo in una directory locale nel computer, ad esempio C:\sampledata\.

    Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Rinominare, scaricare ed eliminare i dati dall'account Data Lake Storage Gen1
Per rinominare un file, usare il comando seguente:

    Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Per scaricare un file, usare il comando seguente:

    Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Per eliminare un file, usare il comando seguente:

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quando viene richiesto, immettere **Y** per eliminare l'elemento. Se sono presenti più file da eliminare, è possibile fornire tutti i percorsi separati da una virgola.

    Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Eliminare l'account Data Lake Storage Gen1
Usare il comando seguente per eliminare l'account Data Lake Storage Gen1.

    Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name

Quando viene richiesto, immettere **Y** per eliminare l'account.

## <a name="next-steps"></a>Passaggi successivi
* [Linee guida per l'ottimizzazione delle prestazioni per l'uso di PowerShell con Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Usare Azure Data Lake Storage Gen1 per i requisiti di Big Data](data-lake-store-data-scenarios.md) 
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
