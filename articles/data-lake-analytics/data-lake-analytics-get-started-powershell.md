---
title: Introduzione ad Azure Data Lake Analytics con Azure PowerShell | Documentazione Microsoft
description: 'Usare Azure PowerShell per creare un account di Data Lake Analytics, definire un processo di Data Lake Analytics con U-SQL e inviare il processo. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Esercitazione: Introduzione ad Azure Data Lake Analytics con Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Questo articolo illustra come usare Azure PowerShell per creare account Azure Data Lake Analytics e quindi inviare ed eseguire processi U-SQL. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione sono necessari le informazioni seguenti:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Workstation con Azure PowerShell**. Vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

## <a name="preparing-for-the-tutorial"></a>Preparazione dell'esercitazione
Per creare un account Data Lake Analytics, prima è necessario definire:

* **Gruppo di risorse di Azure**: è necessario creare un account di Data Lake Analytics all'interno di un gruppo di risorse di Azure.
* **Nome dell'account Data Lake Analytics**: il nome dell'account Data Lake deve contenere solo lettere minuscole e numeri.
* **Posizione**: uno dei data center di Azure che supporta Data Lake Analytics.
* **Account Data Lake Store predefinito**: ogni account Data Lake Analytics ha un account Data Lake Store predefinito. Questi account devono essere nello stesso percorso.

I frammenti di codice di PowerShell in questa esercitazione usano le variabili seguenti per archiviare queste informazioni.

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake

Se non si ha già un gruppo di risorse da usare, crearne uno. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Per ogni account Data Lake Analytics deve essere configurato un account Data Lake Store che viene usato per l'archiviazione dei log. È possibile usare un account esistente o creare un nuovo account. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Quando sono disponibili un gruppo di risorse e un account Data Lake Store, creare un account Data Lake Analytics.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Ottenere informazioni su un account Data Lake Analytics account

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Inviare un processo U-SQL

Creare un file di testo con lo script U-SQL seguente.

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

Inviare lo script.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>Monitorare i processi U-SQL

Elencare tutti i processi nell'account. L'output include i processi attualmente in esecuzione e quelli completati di recente.

```
Get-AdlJob -Account $adla
```

Ottenere lo stato di un processo specifico.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Invece di continuare a chiamare Get-AdlAnalyticsJob fino al termine di un processo, è possibile usare il cmdlet Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Al termine del processo, controllare se il file di output esiste elencando i file in una cartella.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Verificare l'esistenza di un file.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>Caricamento e download dei file

Scaricare l'output dello script U-SQL.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


Caricare un file da usare come input per uno script U-SQL.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>Vedere anche
* Per visualizzare la stessa esercitazione usando altri strumenti, scegliere i selettori di scheda nella parte superiore della pagina.
* Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Per informazioni sulle attività di gestione, vedere [Gestire Azure Data Lake Analytics tramite il portale di Azure](data-lake-analytics-manage-use-portal.md).

