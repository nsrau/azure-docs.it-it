---
title: Introduzione a Azure Data Lake Storage Gen1-PowerShell | Microsoft Docs
description: Usare Azure PowerShell per creare un account di Azure Data Lake Storage Gen1 ed eseguire operazioni di base.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 42ddab6991b418af3e41da9966cdab69ded87461
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837884"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Iniziare a usare Azure Data Lake Storage Gen1 tramite Azure PowerShell

> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Informazioni su come usare Azure PowerShell per creare un account di Azure Data Lake Storage Gen1 ed eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare file di dati, eliminare l'account e così via. Per ulteriori informazioni su Data Lake Storage Gen1, vedere [Panoramica di data Lake storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Autenticazione

Questo articolo usa un approccio di autenticazione più semplice con Data Lake Storage Gen1 in cui viene richiesto di immettere le credenziali dell'account Azure. Il livello di accesso all'account Data Lake Storage Gen1 e al file system viene quindi controllato dal livello di accesso dell'utente connesso. Esistono tuttavia altri approcci per l'autenticazione con Data Lake Storage Gen1, ovvero l'autenticazione dell'utente finale o l'autenticazione da servizio a servizio. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Creare un account Data Lake Storage Gen1

1. Dal desktop aprire una nuova finestra di Windows PowerShell. Immettere il frammento di codice seguente per accedere all'account Azure, impostare la sottoscrizione e registrare il provider di Data Lake Storage Gen1. Quando viene chiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Un account Data Lake Storage Gen1 è associato a un gruppo di risorse di Azure. Iniziare creando un gruppo di risorse.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Creare un gruppo di risorse di Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Creare un gruppo di risorse di Azure")

1. Creare un account Data Lake Storage Gen1. Il nome specificato deve contenere solo lettere minuscole e numeri.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Creazione di un account Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Creare un account Data Lake Storage Gen1")

1. Verificare che l'account sia stato creato correttamente.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    L'output del cmdlet dovrebbe essere **True**.

## <a name="create-directory-structures"></a>Creare strutture di directory

È possibile creare directory nell'account Data Lake Storage Gen1 per gestire e archiviare i dati.

1. Specificare una directory radice.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Creare una nuova directory denominata **mynewdirectory** sotto la radice specificata.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Verificare che la nuova directory sia stata creata correttamente.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    Dovrebbe venire visualizzato un output simile a quello dello screenshot seguente:

    ![Verifica directory](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verificare la directory")

## <a name="upload-data"></a>Caricare dati

È possibile caricare i dati in Data Lake Storage Gen1 direttamente al livello radice o in una directory creata nell'account. I frammenti di codice in questa sezione illustrano come caricare alcuni dati di esempio nella directory (**mynewdirectory**) creata nella sezione precedente.

Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Scaricare il file e archiviarlo in una directory locale nel computer, ad esempio C:\sampledata\.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Rinominare, scaricare ed eliminare dati

Per rinominare un file, usare il comando seguente:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Per scaricare un file, usare il comando seguente:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Per eliminare un file, usare il comando seguente:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Quando viene richiesto, immettere **Y** per eliminare l'elemento. Se sono presenti più file da eliminare, è possibile fornire tutti i percorsi separati da una virgola.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Elimina l'account

Usare il comando seguente per eliminare l'account Data Lake Storage Gen1.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Quando viene richiesto, immettere **Y** per eliminare l'account.

## <a name="next-steps"></a>Passaggi successivi

* [Linee guida per l'ottimizzazione delle prestazioni per l'uso di PowerShell con Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Usare Azure Data Lake Storage Gen1 per i requisiti di Big Data](data-lake-store-data-scenarios.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
