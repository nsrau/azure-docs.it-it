---
title: 'Script PowerShell: trasformare dati nel cloud con Data Factory | Microsoft Docs'
description: Questo script PowerShell trasforma i dati nel cloud eseguendo il programma Spark in un cluster Azure HDInsight Spark.
services: data-factory
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: shlo
ms.openlocfilehash: 27458a48c04a6d4ec612252dc298d454a48cf009
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Script PowerShell: trasformare dati nel cloud con Azure Data Factory

Questo script PowerShell di esempio crea una pipeline che trasforma i dati nel cloud eseguendo il programma Spark in un cluster Azure HDInsight Spark. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>prerequisiti
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

Dopo aver eseguito lo script di esempio, è possibile eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse associate a questo:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Per rimuovere la data factory dal gruppo di risorse, eseguire il comando seguente: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2) | Creare una data factory. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedservice) | Crea un servizio collegato nella data factory. Un servizio collegato collega un archivio dati o un ambiente di calcolo a una data factory. |
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactorv2ypipeline) | Crea una pipeline nella data factory. Una pipeline contiene una o più attività che eseguono un'operazione specifica. Nella pipeline un'attività Spark trasforma i dati tramite l'esecuzione di un programma in un cluster Azure HDInsight Spark. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2pipelinerun) | Crea un'esecuzione per la pipeline. In altre parole, esegue la pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Ottiene i dettagli dell'esecuzione dell'attività nella pipeline. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script PowerShell di Azure Data Factory sono disponibili in [Azure Data Factory PowerShell samples](../samples-powershell.md) (Esempi di PowerShell per Azure Data Factory).