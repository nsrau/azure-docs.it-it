---
title: Trasformare i dati nel cloud con PowerShell
description: Questo script PowerShell trasforma i dati nel cloud eseguendo il programma Spark in un cluster Azure HDInsight Spark.
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: c09d0532b845472d0ccaac1ad57e3772630bb5c9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932057"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Script PowerShell: trasformare dati nel cloud con Azure Data Factory

Questo script PowerShell di esempio crea una pipeline che trasforma i dati nel cloud eseguendo il programma Spark in un cluster Azure HDInsight Spark. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Account di archiviazione di Azure**. Creare uno script Python e un file di input, quindi caricarli nella risorsa di archiviazione di Azure. L'output del programma Spark viene archiviato in questo account di archiviazione. Il cluster Spark su richiesta usa lo stesso account di archiviazione come risorsa di archiviazione primaria.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Caricare lo script Python nell'account di archiviazione BLOB
1. Creare un file Python denominato **WordCount_Spark.py** con il contenuto seguente: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Sostituire **&lt;storageAccountName&gt;** con il nome del proprio account di archiviazione di Azure. Salvare quindi il file. 
3. Nell'Archivio BLOB di Azure creare un contenitore denominato **adftutorial**, se non esiste. 
4. Creare una cartella denominata **spark**.
5. Creare una sottocartella denominata **script** nella cartella **spark**. 
6. Caricare il file **WordCount_Spark.py** nella sottocartella **script**. 


### <a name="upload-the-input-file"></a>Caricare il file di input
1. Creare un file denominato **minecraftstory.txt** con del testo. Il programma Spark conta il numero di parole in questo testo. 
2. Creare una sottocartella denominata `inputfiles` nella cartella `spark` del contenitore BLOB. 
3. Caricare il file `minecraftstory.txt` nella sottocartella `inputfiles`. 

## <a name="sample-script"></a>Script di esempio
> [!IMPORTANT]
> Questo script crea file JSON che definiscono entità di Data Factory (servizio collegato, set di dati e pipeline) nella cartella c:\ del disco rigido.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo aver eseguito lo script di esempio, è possibile eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Per rimuovere la data factory dal gruppo di risorse, eseguire il comando seguente: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Creare una data factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Crea un servizio collegato nella data factory. Un servizio collegato collega un archivio dati o un ambiente di calcolo a una data factory. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Crea una pipeline nella data factory. Una pipeline contiene una o più attività che eseguono un'operazione specifica. Nella pipeline un'attività Spark trasforma i dati tramite l'esecuzione di un programma in un cluster Azure HDInsight Spark. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Crea un'esecuzione per la pipeline. In altre parole, esegue la pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Ottiene i dettagli dell'esecuzione dell'attività nella pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script PowerShell di Azure Data Factory sono disponibili in [Azure Data Factory PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Data Factory).
