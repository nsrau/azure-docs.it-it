<properties 
	pageTitle="Chiamare i programmi Spark da Azure Data Factory" 
	description="È possibile chiamare i programmi Spark da una data factory di Azure usando l'attività MapReduce." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="spelluru"/>

# Chiamare i programmi Spark da Data Factory
## Introduzione
È possibile usare l'attività MapReduce in una pipeline di Data Factory per eseguire programmi Spark nel cluster HDInsight Spark. Prima di leggere questo articolo, vedere l'articolo relativo all'[attività MapReduce](data-factory-map-reduce.md) per informazioni dettagliate sull'uso dell'attività.

## Esempio di Spark in GitHub
L'[esempio Spark - Data Factory in GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) spiega come usare l'attività MapReduce per chiamare un programma Spark. Il programma Spark si limita a copiare i dati da un contenitore BLOB di Azure a un altro.

## Entità di Data factory
La cartella **Spark-ADF/src/ADFJsons** contiene i file per le entità di Data Factory, ad esempio servizi collegati, set di dati e pipeline.

Esistono due **servizi collegati** in questo esempio: Archiviazione di Azure e Azure HDInsight. Specificare il nome di archiviazione di Azure e i valori chiave in **StorageLinkedService.json** e URI del cluster, nome utente e password in **HDInsightLinkedService.json**.

Esistono due **set di dati** in questo esempio: **input.json** e **output.json**. Questi file si trovano nella cartella dei **set di dati**. I file rappresentano i set di dati di input e output per l'attività MapReduce.

Le pipeline di esempio sono disponibili nella cartella **ADFJsons/Pipeline**. Esaminare una pipeline per comprendere come richiamare un programma Spark usando l'attività MapReduce.

L'attività MapReduce è configurata per chiamare **com.adf.sparklauncher.jar** nel contenitore **adflibs** dell'archiviazione di Azure (specificato in StorageLinkedService.json). Il codice sorgente per questo programma si trova nella cartella Spark-ADF/src/main/java/com/adf/ ed esegue processi Spark e chiamate a spark-submit.

> [AZURE.IMPORTANT] 
Leggere con attenzione il file [README.TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) per informazioni aggiuntive e più recenti prima di usare l'esempio.
>  
> Usare il proprio cluster HDInsight Spark con questo approccio per chiamare i programmi Spark usando l'attività MapReduce. L'uso di un cluster HDInsight su richiesta non è supportato.


## Vedere anche
- [Attività Hive](data-factory-hive-activity.md)
- [Attività di Pig](data-factory-pig-activity.md)
- [Attività MapReduce](data-factory-map-reduce.md)
- [Attività di Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
- [Chiamare gli script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0831_2016-->