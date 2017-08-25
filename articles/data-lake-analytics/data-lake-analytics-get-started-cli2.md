---
title: Introduzione ad Azure Data Lake Analytics con l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: 'Informazioni su come usare l''interfaccia della riga di comando di Azure 2.0 per creare un account Data Lake Analytics, definire un processo di Data Lake Analytics con U-SQL e inviare il processo. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: cb5872a93bbff99161cd5f61e6e26bdb0d322587
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-cli-20"></a>Introduzione ad Azure Data Lake Analytics con l'interfaccia della riga di comando di Azure 2.0
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

In questa esercitazione verrà sviluppato un processo che legge un file con valori delimitati da tabulazioni (TSV) e lo converte in un file con valori delimitati da virgole (CSV). Per eseguire la stessa esercitazione usando altri strumenti supportati, usare l'elenco a discesa disponibile nella parte superiore di questa sezione.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione sono necessari gli elementi seguenti:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Interfaccia della riga di comando di Azure 2.0**. Vedere [Installare e configurare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Per accedere alla sottoscrizione di Azure:

```
azurecli
az login
```

Viene richiesto di passare a un URL e immettere un codice di autenticazione.  Seguire le istruzioni per immettere le credenziali.

Dopo aver eseguito l'accesso, il comando di accesso elenca le sottoscrizioni.

Per usare una sottoscrizione specifica:

```
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake
Per poter eseguire un processo è necessario un account Data Lake Analytics. Per creare un account Data Lake Analytics, specificare quanto segue:

* **Gruppo di risorse di Azure**. L'account Data Lake Analytics deve essere creato all'interno di un gruppo di risorse di Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) consente di lavorare con le risorse dell'applicazione come gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse per l'applicazione mediante un'unica operazione coordinata.  

Per elencare i gruppi di risorse esistenti nella sottoscrizione:

```
az group list
```

Per creare un nuovo gruppo di risorse:

```
az group create --name "<Resource Group Name>" --location "<Azure Location>"
```

* **Nome dell'account Data Lake Analytics**. Ogni account Data Lake Analytics ha un nome.
* **Località**. Usare uno dei data center di Azure che supporta Data Lake Analytics.
* **Account Data Lake Store predefinito**: ogni account Data Lake Analytics ha un account Data Lake Store predefinito.

Per elencare l'account Data Lake Store esistente:

```
az dls account list
```

Per creare un nuovo account Data Lake Store:

```azurecli
az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
```

Per creare un account Data Lake Analytics, usare la sintassi seguente:

```
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Dopo aver creato un account, è possibile usare i comandi seguenti per elencare gli account e visualizzarne i dettagli:

```
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Caricare dati nell’Archivio Data Lake
In questa esercitazione verrà eseguita l'elaborazione di alcuni log di ricerca.  Il log di ricerca può essere archiviato in Data Lake Store o in un archivio BLOB di Azure.

Il portale di Azure offre un’interfaccia utente per copiare alcuni file di dati di esempio nell'account Data Lake Store predefinito, tra cui anche un file di log di ricerca. Vedere [Preparare i dati di origine](data-lake-analytics-get-started-portal.md) per caricare i dati nell'account Archivio Data Lake predefinito.

Per caricare i file usando l'interfaccia della riga di comando 2.0, usare i comandi seguenti:

```
az dls fs upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls fs list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Data Lake Analytics può inoltre accedere all'archivio BLOB di Azure.  Per caricare i dati nell'archiviazione BLOB di Azure, vedere [Uso dell’interfaccia della riga di comando di Azure con Archiviazione di Azure](../storage/common/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Inviare processi di Data Lake Analytics
I processi di Data Lake Analtyics vengono scritti nel linguaggio U-SQL. Per altre informazioni su U-SQL, vedere [Introduzione a U-SQL](data-lake-analytics-u-sql-get-started.md) e [U-SQL Language Reference](http://go.microsoft.com/fwlink/?LinkId=691348) (Informazioni di riferimento sul linguaggio U-SQL).

**Per creare uno script per il processo di Data Lake Analytics**

Creare un file di testo contenente il seguente script U-SQL e salvare il file di testo nella workstation in uso:

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Questo script U-SQL legge il file di dati di origine con **Extractors.Tsv()** e quindi crea un file CSV con **Outputters.Csv()**.

Non modificare i due percorsi, a meno che il file di origine non sia stato copiato in una posizione diversa.  Data Lake Analytics creerà la cartella di output, se non esiste già.

Risulta più semplice usare i percorsi relativi dei file archiviati in account Data Lake Store predefiniti, ma è possibile usare anche percorsi assoluti.  Ad esempio:

```
adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
```

È necessario usare percorsi assoluti per accedere ai file presenti negli account di archiviazione collegati.  La sintassi dei file presenti in un account di Archiviazione di Azure collegato è:

```
wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
```

> [!NOTE]
> Il contenitore BLOB di Azure con BLOB pubblici non è supportato.      
> Il contenitore BLOB di Azure con contenitori pubblici non è supportato.      
>

**Per inviare processi**

Per inviare un processo, usare la sintassi seguente:

```
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Ad esempio:

```
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Per elencare i processi e visualizzarne i dettagli**

```
azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Per annullare processi**

```
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>Recuperare i risultati di un processo

Al termine di un processo, è possibile usare i comandi seguenti per elencare i file di output e scaricare i file:

```
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Ad esempio:

```
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Passaggi successivi

* Per il documento di riferimento dell'interfaccia della riga di comando di Data Lake Analytics 2.0, vedere [Data Lake Analytics](https://docs.microsoft.com/cli/azure/dla).
* Per il documento di riferimento dell'interfaccia della riga di comando di Data Lake Store 2.0, vedere [Data Lake Store](https://docs.microsoft.com/cli/azure/dls).
* Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Analisi Data Lake di Azure](data-lake-analytics-analyze-weblogs.md).

