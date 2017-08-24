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
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 1fe2b4b6c84e3c4d96677e3da5a94b9462e2a7bf
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Introduzione all'archivio Azure Data Lake mediante Azure PowerShell
> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interfaccia della riga di comando di Azure 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Informazioni su come usare Azure PowerShell per creare un account di Azure Data Lake Store ed eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare i file di dati, eliminare l'account e così via. Per altre informazioni su Data Lake Store, vedere [Panoramica di Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 o versioni successive**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Autenticazione
Questo articolo usa un approccio di autenticazione più semplice con Data Lake Store con cui viene richiesto di immettere le credenziali del proprio account di Azure. Il livello di accesso al file system e all'account Data Lake Store viene quindi regolato dal livello di accesso dell'utente connesso. Esistono tuttavia altri approcci oltre all'autenticazione con Data Lake Store, ad esempio l'**autenticazione dell'utente finale** o l'**autenticazione da servizio a servizio**. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Creare un account di Azure Data Lake Store
1. Dal desktop aprire una nuova finestra di Windows PowerShell e immettere il seguente frammento per accedere al proprio account di Azure, impostare la sottoscrizione e registrare il provider di Data Lake Store. Quando viene richiesto di effettuare l'accesso, assicurarsi di accedere come amministratore/proprietario della sottoscrizione:

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
3. Creare un account di Azure Data Lake Store. Il nome specificato deve contenere solo lettere minuscole e numeri.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Creare un account di Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")
4. Verificare che l'account sia stato creato correttamente.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    L'output di questa operazione deve essere **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Creare strutture di directory in Azure Data Lake Store
È possibile creare una directory con il proprio account di Azure Data Lake Store per gestire e archiviare i dati.

1. Specificare una directory radice.

        $myrootdir = "/"
2. Creare una nuova directory denominata **mynewdirectory** sotto la radice specificata.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Verificare che la nuova directory sia stata creata correttamente.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Verrà visualizzato un output simile al seguente:

    ![Verificare la directory](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Caricare dati in Azure Data Lake Store
È possibile caricare i dati in Data Lake Store direttamente a livello di radice o in una directory creata all'interno dell'account. I frammenti di codice riportati di seguito illustrano come caricare alcuni dati di esempio nella directory (**mynewdirectory**) creata nella sezione precedente.

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

## <a name="performance-guidance-while-using-powershell"></a>Linee guida sulle prestazioni durante l'uso di PowerShell

Di seguito sono indicate le impostazioni più importanti che possono essere ottimizzate per ottenere le migliori prestazioni durante l'uso di PowerShell con Data Lake Store:

| Proprietà            | Default | Descrizione |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Questo parametro consente di scegliere il numero di thread paralleli per il caricamento o il download di ogni file. Questo valore rappresenta il numero massimo di thread che può essere allocato per ogni file, ma è possibile ottenere meno thread a seconda dello scenario (ad esempio, se si sta caricando un file da 1 KB, si otterrà un thread anche se si chiedono 20 thread).  |
| ConcurrentFileCount | 10      | Questo parametro viene usato specificamente per il caricamento e il download delle cartelle. Questo parametro determina il numero di file che possono essere caricati o scaricati contemporaneamente. Questo valore rappresenta il numero massimo di file che possono essere caricati o scaricati contemporaneamente in una sola volta, ma è possibile ottenere un valore inferiore di concorrenza a seconda dello scenario (ad esempio, se si caricano due file, si otterrà il caricamento di due file contemporaneamente anche se ne vengono richiesti 15). |

**Esempio**

Questo comando scarica i file da Azure Data Lake Store al disco locale dell'utente usando 20 thread per file e 100 file contemporaneamente.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>Come è possibile determinare il valore da impostare per questi parametri?

Ecco alcune linee guida che è possibile usare.

* **Passaggio 1: Determinare il numero totale di thread** - È consigliabile iniziare calcolando il numero totale di thread da usare. Come regola generale, è consigliabile usare 6 thread per ogni core fisico.

        Total thread count = total physical cores * 6

    **Esempio**

    Supponendo che si eseguano i comandi PowerShell da una macchina virtuale D14 con 16 core

        Total thread count = 16 cores * 6 = 96 threads


* **Passaggio 2: Calcolare PerFileThreadCount** - Viene calcolato il parametro PerFileThreadCount in base alla dimensione dei file. Per i file di dimensioni inferiori a 2,5 GB, non è necessario modificare questo parametro, perché il valore predefinito di 10 è sufficiente. Per i file di dimensioni superiori a 2,5 GB, è necessario usare 10 thread come base per i primi 2,5 GB e aggiungere 1 thread per ogni incremento aggiuntivo di 256 MB alle dimensioni del file. Se si copia una cartella con un'ampia gamma di dimensioni dei file, è consigliabile raggrupparle in file di dimensioni simili. Il raggruppamento in file di dimensioni diverse può comportare prestazioni non ottimali. Se non è possibile raggruppare file di dimensioni simili, è necessario impostare PerFileThreadCount in base alle dimensioni del file più grande.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **Esempio**

    Se si dispone di 100 file da 1 GB a 10 GB, viene usata la dimensione di 10 GB come riferimento per l'equazione, interpretata come segue.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **Passaggio 3: Calcolare ConcurrentFilecount** - Usare il numero totale di thread e PerFileThreadCount per calcolare ConcurrentFileCount in base all'equazione seguente.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Esempio**

    In base ai valori di esempio usati

        96 = 40 * ConcurrentFileCount

    Pertanto, **ConcurrentFileCount** è **2.4**, che è possibile arrotondare a **2**.

### <a name="further-tuning"></a>Ottimizzazione ulteriore

È possibile richiedere un'ulteriore ottimizzazione perché è presente un intervallo di dimensioni di file da usare. Il calcolo precedente funziona bene se tutti o la maggior parte dei file è più grande e più vicina all'intervallo di 10 GB. Se invece sono presenti diverse dimensioni di file con molti file più piccoli, è possibile ridurre PerFileThreadCount. Riducendo PerFileThreadCount, è possibile aumentare il valore di ConcurrentFileCount. Pertanto, se si presume che la maggior parte dei file è minore dell'intervallo di 5 GB, è possibile eseguire nuovamente il calcolo:

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

Pertanto, **ConcurrentFileCount** avrà un valore di 96/20, ovvero 4,8, arrotondato a **4**.

È possibile continuare a ottimizzare le impostazioni modificando il valore di **PerFileThreadCount** in base alla distribuzione delle dimensioni dei file.

### <a name="limitation"></a>Limitazione

* **Numero di file minore di ConcurrentFileCount**: se il numero di file che si sta caricando è minore del valore di **ConcurrentFileCount** calcolato, è necessario ridurre **ConcurrentFileCount** in modo che risulti uguale al numero di file. È possibile usare i thread rimanenti per aumentare **PerFileThreadCount**.

* **Troppi thread**: se si aumenta il numero dei thread in maniera eccessiva senza aumentare le dimensioni del cluster, si corre il rischio di ridurre le prestazioni. È possibile che si verifichino problemi di conflitto dovuti al cambio di contesto nella CPU.

* **Concorrenza insufficiente**: se la concorrenza non è sufficiente, è possibile che il cluster sia troppo piccolo. È possibile aumentare il numero di nodi del cluster in modo da avere maggiore concorrenza.

* **Errori di limitazione**: se la concorrenza è troppo elevata, è possibile che vengano visualizzati errori di limitazione. Se si riscontrano errori di limitazione, è necessario ridurre la concorrenza o contattare Microsoft.

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


