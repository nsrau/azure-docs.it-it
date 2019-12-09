---
title: Trasformare i dati usando l'attività MapReduce di Hadoop
description: Informazioni su come elaborare i dati eseguendo programmi Hadoop MapReduce in un cluster Azure HDInsight da un'istanza di Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 5d38e3126442bcf34c96cead2b2ea59507b50b8c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912871"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Trasformare dati tramite l'attività Hadoop MapReduce in Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-map-reduce.md)
> * [Versione corrente](transform-data-using-hadoop-map-reduce.md)

L'attività HDInsight MapReduce in una [pipeline](concepts-pipelines-activities.md) di Data Factory richiama il programma MapReduce nei cluster HDInsight [personalizzati](compute-linked-services.md#azure-hdinsight-linked-service) o [su richiesta](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Questo articolo si basa sull'articolo relativo alle [attività di trasformazione dei dati](transform-data.md) che presenta una panoramica generale della trasformazione dei dati e le attività di trasformazione supportate.

Se non si ha familiarità con Azure Data Factory, prima di leggere questo articolo leggere l'[introduzione ad Azure Data Factory](introduction.md) ed eseguire [Tutorial: transform data](tutorial-transform-data-spark-powershell.md) (Esercitazione: Trasformare i dati).

Vedere [Pig](transform-data-using-hadoop-pig.md) e [Hive](transform-data-using-hadoop-hive.md) per informazioni dettagliate sull'esecuzione di script Pig/Hive in un cluster HDInsight da una pipeline tramite le attività HDInsight Pig e Hive.

## <a name="syntax"></a>Sintassi

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Dettagli sintassi

| Proprietà          | Description                              | Obbligatoria |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nome dell'attività                     | SÌ      |
| description       | Testo descrittivo per lo scopo dell'attività | No       |
| type              | Per l'attività MapReduce, il tipo di attività è HDinsightMapReduce | SÌ      |
| linkedServiceName | Riferimento al cluster HDInsight registrato come servizio collegato in Data Factory. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md). | SÌ      |
| className         | Nome della classe da eseguire         | SÌ      |
| jarLinkedService  | Riferimento a un servizio collegato di Archiviazione di Azure usato per archiviare i file Jar. Se non si specifica questo servizio collegato, viene usato il servizio collegato Archiviazione di Azure definito nel servizio collegato HDInsight. | No       |
| jarFilePath       | Specificare il percorso dei file Jar archiviati nel servizio Archiviazione di Azure indicato da jarLinkedService. Il nome del file distingue tra maiuscole e minuscole. | SÌ      |
| jarlibs           | Percorso dei file di libreria Jar indicati dal processo archiviato nel servizio Archiviazione di Azure indicato da jarLinkedService. Il nome del file distingue tra maiuscole e minuscole. | No       |
| getDebugInfo      | Specifica quando i file di log vengono copiati nel servizio Archiviazione di Azure usato dal cluster HDInsight o specificato da jarLinkedService. Valori consentiti: None, Always o Failure. Valore predefinito: None. | No       |
| arguments         | Specifica una matrice di argomenti per un processo Hadoop. Gli argomenti vengono passati a ogni attività come argomenti della riga di comando. | No       |
| defines           | Nei riferimenti all'interno dello script Hive, specificare i parametri come coppie chiave/valore. | No       |



## <a name="example"></a>Esempio
È possibile usare l’attività MapReduce di HDInsight per l'esecuzione di file JAR di MapReduce in un cluster HDInsight. Nella definizione JSON seguente di una pipeline di esempio l'attività HDInsight è configurata per eseguire un file JAR di Mahout.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
È possibile specificare eventuali argomenti per il programma MapReduce nella sezione **arguments**. In fase di esecuzione, vengono visualizzati alcuni argomenti aggiuntivi (ad esempio: mapreduce.job.tags) dal framework di MapReduce. Per differenziare gli argomenti con gli argomenti di MapReduce, è consigliabile usare sia l'opzione che il valore come argomenti, come illustrato nell'esempio seguente (- s, --input - output e così via sono opzioni immediatamente seguite dai valori).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti che illustrano come trasformare i dati in altri modi:

* [Attività U-SQL](transform-data-using-data-lake-analytics.md)
* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
