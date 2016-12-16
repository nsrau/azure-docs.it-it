---
title: Chiamare i programmi Spark da Azure Data Factory
description: "È possibile chiamare i programmi Spark da una data factory di Azure usando l&quot;attività MapReduce."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b3895dbf56c58e8e10e6af84afc520e5aef6e01e
ms.openlocfilehash: c2eb07b83f3096f86772f9af63a48da79ce0fd2e


---
# <a name="invoke-spark-programs-from-data-factory"></a>Chiamare i programmi Spark da Data Factory
## <a name="introduction"></a>Introduzione
È possibile usare l'attività MapReduce in una pipeline di Data Factory per eseguire programmi Spark nel cluster HDInsight Spark. Prima di leggere questo articolo, vedere l'articolo relativo all' [attività MapReduce](data-factory-map-reduce.md) per informazioni dettagliate sull'uso dell'attività. 

## <a name="spark-sample-on-github"></a>Esempio di Spark in GitHub
L' [esempio Spark - Data Factory in GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) spiega come usare l'attività MapReduce per chiamare un programma Spark. Il programma Spark si limita a copiare i dati da un contenitore BLOB di Azure a un altro. 

## <a name="data-factory-entities"></a>Entità di Data factory
La cartella **Spark-ADF/src/ADFJsons** contiene i file per le entità di Data Factory, ad esempio servizi collegati, set di dati e pipeline.  

Esistono due **servizi collegati** in questo esempio: Archiviazione di Azure e Azure HDInsight. Specificare il nome dell'archiviazione di Azure e i valori chiave in **StorageLinkedService.json** e URI del cluster, nome utente e password in **HDInsightLinkedService.json**.

Esistono due **set di dati** in questo esempio: **input.json** e **output.json**. Questi file si trovano nella cartella dei **set di dati** .  I file rappresentano i set di dati di input e output per l'attività MapReduce.

Le pipeline di esempio sono disponibili nella cartella **ADFJsons/Pipeline** . Esaminare una pipeline per comprendere come richiamare un programma Spark usando l'attività MapReduce. 

L'attività MapReduce è configurata per richiamare **com.adf.sparklauncher.jar** nel contenitore **adflibs** dell'archiviazione di Azure (specificato in StorageLinkedService.json). Il codice sorgente per questo programma si trova nella cartella Spark-ADF/src/main/java/com/adf/ ed esegue processi Spark e chiamate a spark-submit. 

> [!IMPORTANT]
> Leggere con attenzione il file [README.md](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.md) per informazioni aggiuntive e più recenti prima di usare l'esempio. 
> 
> Usare il proprio cluster HDInsight Spark con questo approccio per chiamare i programmi Spark usando l'attività MapReduce. L'uso di un cluster HDInsight su richiesta non è supportato.   
> 
> 

## <a name="see-also"></a>Vedere anche
* [Attività Hive](data-factory-hive-activity.md)
* [Attività di Pig](data-factory-pig-activity.md)
* [Attività MapReduce](data-factory-map-reduce.md)
* [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
* [Chiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Nov16_HO3-->


