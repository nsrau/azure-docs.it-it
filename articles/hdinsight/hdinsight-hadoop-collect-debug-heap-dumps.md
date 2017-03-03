---
title: Eseguire il debug e analizzare i servizi Hadoop con i dump dell&quot;heap | Documentazione Microsoft
description: Raccogliere automaticamente i dump dell&quot;heap per i servizi Hadoop e inserirli nell&quot;account di archiviazione BLOB di Azure per il debug e l&quot;analisi.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e4ec4ebb-fd32-4668-8382-f956581485c4
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: b6830f422b914722b8381a69c73cf8b02e107115
ms.lasthandoff: 02/07/2017


---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Raccogliere i dump dell'heap nell'archivio BLOB per eseguire il debug e analizzare i servizi Hadoop
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

I dump dell'heap includono uno snapshot della memoria dell'applicazione, ad esempio i valori delle variabili al momento della creazione del dump. Si rivelano quindi molto utili per diagnosticare i problemi che si verificano in fase di esecuzione. È possibile raccogliere automaticamente i dump dell'heap per i servizi Hadoop e posizionarli nell'account di archiviazione BLOB di Azure di un utente in HDInsightHeapDumps/. 

La raccolta di dump dell'heap relativi ai diversi servizi deve essere abilitata sui singoli cluster. Per impostazione predefinita, questa funzionalità è disattivata per un cluster. Poiché i dump dell'heap possono avere dimensioni elevate, è consigliabile monitorare l'account di archiviazione BLOB in cui vengono salvati dopo l'abilitazione della raccolta.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date). Le informazioni contenute in questo articolo si applicano solo a HDInsight basato su Windows. Per informazioni su HDInsight basato su Linux, vedere [Abilitare i dump dell'heap per i servizi Hadoop in HDInsight basato su Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)


## <a name="eligible-services-for-heap-dumps"></a>Servizi idonei per i dump dell'heap
È possibile abilitare dump dell'heap per i servizi seguenti:

* **hcatalog** - tempelton
* **hive** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **yarn** - resourcemanager, nodemanager, timelineserver
* **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementi di configurazione per l'abilitazione dei dump dell'heap
Per attivare i dump di heap per un servizio, è necessario impostare gli elementi di configurazione appropriati nella sezione relativa al servizio in questione, specificata da **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Il valore di **service_name** può essere uno qualsiasi dei servizi sopra elencati: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode o namenode.

## <a name="enable-using-azure-powershell"></a>Abilitare con Azure PowerShell
Ad esempio, per attivare i dump dell'heap usando Azure PowerShell per jobhistoryserver, sarebbe necessario procedere come segue:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Abilitare con .NET SDK
Ad esempio, per attivare i dump dell'heap usando Azure HDInsight .NET SDK per jobhistoryserver, sarebbe necessario procedere come segue:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

