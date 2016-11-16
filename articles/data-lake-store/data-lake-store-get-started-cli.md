---
title: Introduzione ad Azure Data Lake Store utilizzando l&quot;interfaccia della riga di comando multipiattaforma | Microsoft Docs
description: Usare la riga di comando multipiattaforma di Azure per creare un account di Data Lake Store ed eseguire operazioni di base
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4b2449f00e298385579c4d7b229ceea18dcc598


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Introduzione ad Azure Data Lake Store tramite la riga di comando di Azure
> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Informazioni su come la riga di comando di Azure per creare un account di Azure Data Lake Store ed eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare i file di dati, eliminare l'account e così via. Per altre informazioni su Data Lake Store, vedere [Panoramica di Data Lake Store](data-lake-store-overview.md).

L'interfaccia della riga di comando di Azure viene implementata in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, incluse Windows, Mac e Linux. L'interfaccia della riga di comando di Azure è open source. Il codice sorgente viene gestito in GitHub all'indirizzo <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. In questo articolo viene illustrato solo l’uso dell’interfaccia della riga di comando di Azure con Data Lake Store. Per una guida generale sull'uso dell’interfaccia della riga di comando di Azure vedere [Come utilizzare l’interfaccia della riga di comando di Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Interfaccia della riga di comando di Azure** : per informazioni sull’installazione e la configurazione vedere [Installare e configurare l’interfaccia della riga di comando di Azure](../xplat-cli-install.md) . Assicurarsi di riavviare del computer dopo l'installazione dell’interfaccia della riga di comando.

## <a name="authentication"></a>Autenticazione
Questo articolo usa un approccio di autenticazione più semplice con Data Lake Store in cui si accede come utente finale. Il livello di accesso al file system e all'account Data Lake Store viene quindi regolato dal livello di accesso dell'utente connesso. Esistono tuttavia altri approcci oltre all'autenticazione con Data Lake Store, ad esempio l'**autenticazione dell'utente finale** o l'**autenticazione da servizio a servizio**. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Eseguire l'autenticazione in Data Lake Store con Azure Active Directory).

## <a name="login-to-your-azure-subscription"></a>Effettuare l'accesso alla sottoscrizione di Azure
1. Seguire i passaggi descritti in [Connettersi a una sottoscrizione di Azure dall'interfaccia della riga di comando di Azure](../xplat-cli-connect.md) e connettersi alla sottoscrizione usando il metodo `azure login`.
2. Elencare le sottoscrizioni associate all'account usando il comando `azure account list`.
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    Dall'output precedente **Azure-sub-1** è attualmente abilitato e l'altra sottoscrizione è **Azure-sub-2**. 
3. Selezionare la sottoscrizione da usare. Per usare la sottoscrizione Azure-sub-2, eseguire il comando `azure account set`.
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Creare un account di Azure Data Lake Store
Aprire un prompt dei comandi, una shell o una finestra del terminale ed eseguire il seguente comando.

1. Passare alla modalità Gestione risorse di Azure usando il comando seguente:
   
        azure config mode arm
2. Creare un nuovo gruppo di risorse. Nel comando seguente, fornire i valori dei parametri da utilizzare.
   
        azure group create <resourceGroup> <location>
   
    Se il nome del percorso contiene spazi, racchiuderlo tra virgolette doppie, Ad esempio "Stati Uniti orientali 2".
3. Creare un account Data Lake Store di Azure.
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Creare delle cartelle nell’account di Data Lake Store
È possibile creare delle cartelle con il proprio account di Azure Data Lake Store per gestire e archiviare i dati. Utilizzare il comando seguente per creare una cartella denominata "mynewfolder" nella directory radice di Data Lake Store.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Ad esempio:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Caricare dati in Data Lake Store
È possibile caricare i dati in Data Lake Store direttamente a livello di radice o in una cartella creata all'interno dell'account. I frammenti di codice riportati di seguito illustrano come caricare alcuni dati di esempio nella cartella (**mynewdirectory**) creata nella sezione precedente.

Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Scaricare il file e archiviarlo in una directory locale nel computer, ad esempio C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

ad esempio:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Elencare i file in Data Lake Store
Usare il comando seguente per elencare i file nell'account di Data Lake Store.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Ad esempio:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

L'output di questo comando dovrebbe essere simile al seguente:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Rinominare, scaricare ed eliminare i dati da Data Lake Store
* **Per rinominare un file**, usare il comando seguente:
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    ad esempio:
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* **Per scaricare un file**, usare il comando seguente. Assicurarsi che il percorso di destinazione specificato esista già.
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    Ad esempio:
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* **Per eliminare un file**, usare il comando seguente:
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    Ad esempio:
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    Quando viene richiesto, immettere **Y** per eliminare l'elemento.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Visualizzare l'elenco del controllo di accesso per una cartella in Data Lake Store
Utilizzare il comando seguente per visualizzare gli ACL in una cartella di Data Lake Store. Nella versione corrente, è possibile impostare gli ACL solo nella radice del Data Lake Store. Pertanto, il parametro percorso seguente sarà sempre root (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Ad esempio:

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Eliminare l'account di Data Lake Store
Usare il comando seguente per eliminare l'account di Data Lake Store.

    azure datalake store account delete <dataLakeStoreAccountName>

Ad esempio:

    azure datalake store account delete mynewdatalakestore

Quando viene richiesto, immettere **Y** per eliminare l'account.

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Nov16_HO2-->


