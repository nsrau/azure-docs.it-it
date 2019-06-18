---
title: Iniziare a usare Azure Data Lake Storage Gen1 tramite l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Usare l'interfaccia della riga di comando di Azure per creare un account Data Lake Storage Gen1 ed eseguire operazioni di base
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 9431cc7fa12b86371ce6b2325aca8e13d264442e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60885346"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Iniziare a usare Azure Data Lake Store tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli-2.0.md)
>
> 

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare un account Azure Data Lake Storage Gen1 ed eseguire operazioni di base come creare cartelle, caricare e scaricare file di dati, eliminare l'account e così via. Per altre informazioni su Data Lake Storage Gen1, vedere [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md).

L'interfaccia della riga di comando di Azure è l'esperienza della riga di comando di Azure per gestire le risorse di Azure. Può essere usata in macOS, Linux e Windows. Per altre informazioni, vedere [Panoramica dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure). Per un elenco completo di comandi e per la sintassi, è anche possibile vedere le [informazioni di riferimento sull'interfaccia della riga di comando di Azure Data Lake Storage Gen1](https://docs.microsoft.com/cli/azure/dls).


## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Interfaccia della riga di comando di Azure**: vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) per istruzioni.

## <a name="authentication"></a>Authentication

Questo articolo usa un approccio di autenticazione più semplice con Data Lake Storage Gen1 in cui si accede come utente finale. Il livello di accesso all'account Data Lake Storage Gen1 e al file system viene quindi controllato dal livello di accesso dell'utente connesso. Ci sono tuttavia altri approcci oltre all'autenticazione con Data Lake Storage Gen1, ad esempio l'**autenticazione dell'utente finale** o l'**autenticazione da servizio a servizio**. Per altre informazioni e istruzioni su come eseguire l'autenticazione, vedere [Autenticazione dell'utente finale](data-lake-store-end-user-authenticate-using-active-directory.md) o [Autenticazione da servizio a servizio](data-lake-store-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure

1. Accedere alla propria sottoscrizione di Azure.

    ```azurecli
    az login
    ```

    Si ottiene un codice da usare nel passaggio successivo. Usare un Web browser per aprire la pagina https://aka.ms/devicelogin e immettere il codice per l'autenticazione. Verrà chiesto di accedere usando le proprie credenziali.

2. Dopo l'accesso, la finestra elenca tutte le sottoscrizioni di Azure associate all'account. Usare il comando seguente per usare una sottoscrizione specifica.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Creare un account Azure Data Lake Storage Gen1

1. Creare un nuovo gruppo di risorse. Nel comando seguente, fornire i valori dei parametri da utilizzare. Se il nome del percorso contiene spazi, racchiuderlo tra virgolette doppie, Ad esempio "Stati Uniti orientali 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Creare l'account Data Lake Storage Gen1.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Creare cartelle in un account Data Lake Storage Gen1

È possibile creare cartelle nell'account Azure Data Lake Storage Gen1 per gestire e archiviare i dati. Usare il comando seguente per creare una cartella denominata **mynewfolder** nella radice dell'account Data Lake Storage Gen1.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Il parametro `--folder` assicura che il comando crei una cartella. Se questo parametro non è presente, il comando crea un file vuoto denominato mynewfolder nella radice dell'account Data Lake Storage Gen1.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Caricare i dati in un account Data Lake Storage Gen1

È possibile caricare i dati in Data Lake Storage Gen1 direttamente al livello radice o in una cartella creata nell'account. I frammenti di codice riportati di seguito illustrano come caricare alcuni dati di esempio nella cartella (**mynewdirectory**) creata nella sezione precedente.

Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Scaricare il file e archiviarlo in una directory locale nel computer, ad esempio C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Per la destinazione, è necessario specificare il percorso completo, incluso il nome file.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Elencare i file in un account Data Lake Storage Gen1

Usare il comando seguente per elencare i file in un account Data Lake Storage Gen1.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

L'output di questo comando dovrebbe essere simile al seguente:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Rinominare, scaricare ed eliminare i dati da un account Data Lake Storage Gen1 

* **Per rinominare un file**, usare il comando seguente:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Per scaricare un file**, usare il comando seguente. Assicurarsi che il percorso di destinazione specificato esista già.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Il comando crea la cartella di destinazione se non esiste.
    > 
    >

* **Per eliminare un file**, usare il comando seguente:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Per eliminare la cartella **mynewfolder** e il file **vehicle1_09142014_copy.csv** con un unico comando, usare il parametro --recurse.

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Usare le autorizzazioni e gli elenchi di controllo di accesso per un account Data Lake Storage Gen1

Questa sezione illustra come gestire elenchi di controllo di accesso e autorizzazioni usando l'interfaccia della riga di comando di Azure. Per una spiegazione dettagliata di come vengono implementati gli elenchi di controllo di accesso in Azure Data Lake Storage Gen1, vedere [Controllo di accesso in Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

* **Per aggiornare il proprietario di un file o di una cartella**, usare il comando seguente:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Per aggiornare le autorizzazioni per un file o per una cartella**, usare il comando seguente:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Per ottenere gli elenchi di controllo di accesso per un determinato percorso**, usare il comando seguente:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    L'output dovrebbe essere simile al seguente:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Per impostare una voce per un elenco di controllo di accesso**, usare il comando seguente:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Per rimuovere una voce per un elenco di controllo di accesso**, usare il comando seguente:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Per rimuovere un intero elenco di controllo di accesso predefinito**, usare il comando seguente:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Per rimuovere un intero elenco di controllo di accesso non predefinito**, usare il comando seguente:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Eliminare un account Data Lake Storage Gen1
Usare il comando seguente per eliminare un account Data Lake Storage Gen1.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Quando viene richiesto, immettere **Y** per eliminare l'account.

## <a name="next-steps"></a>Passaggi successivi
* [Usare Azure Data Lake Storage Gen1 per i requisiti di Big Data](data-lake-store-data-scenarios.md) 
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
