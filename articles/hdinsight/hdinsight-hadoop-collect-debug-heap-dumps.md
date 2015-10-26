<properties
	pageTitle="Eseguire il debug e analizzare i servizi Hadoop con i dump dell'heap | Microsoft Azure"
	description="Raccogliere automaticamente i dump dell'heap per i servizi Hadoop e inserirli nell'account di archiviazione BLOB di Azure per il debug e l'analisi."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>


# Raccogliere i dump dell'heap nell'archivio BLOB per eseguire il debug e analizzare i servizi Hadoop

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

È possibile raccogliere automaticamente i dump dell'heap per i servizi Hadoop e posizionarli nell'account di archiviazione BLOB di Azure di un utente in HDInsightHeapDumps/. I file di dump relativi a un servizio con heap contengono uno snapshot della memoria dell'applicazione. Sono inclusi i valori delle variabili al momento della creazione del dump.

La raccolta di dump dell'heap relativi ai diversi servizi deve essere abilitata sui singoli cluster. Per impostazione predefinita, questa funzionalità è disattivata per un cluster. Poiché i dump dell'heap possono avere dimensioni elevate, è consigliabile monitorare l'account di archiviazione BLOB in cui vengono salvati dopo l'abilitazione della raccolta.

> [AZURE.NOTE]Le informazioni contenute in questo articolo si applicano solo a HDInsight basato su Windows. Per informazioni su HDInsight basato su Linux, vedere [Abilitare i dump dell'heap per i servizi Hadoop in HDInsight basato su Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="whichServices"></a>Servizi idonei per i dump dell'heap

I servizi per cui, se necessario, è possibile abilitare i dump dell'heap sono:

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration"></a>Elementi di configurazione per l'abilitazione dei dump dell'heap

Per attivare i dump dell'heap per un servizio, è necessario impostare gli elementi di configurazione appropriati nella sezione relativa al servizio in questione, specificata da **service\_name**.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Il valore di **service\_name** può essere uno qualsiasi dei servizi sopra elencati: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode o namenode.

## <a name="powershell"></a>Come abilitare i dump dell'heap con Azure PowerShell

Ad esempio, per attivare i dump dell'heap usando Azure PowerShell per jobhistoryserver, sarebbe necessario procedere come segue:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Come abilitare i dump dell'heap con Azure HDInsight .NET SDK

Ad esempio, per attivare i dump dell'heap usando Azure HDInsight .NET SDK per jobhistoryserver, sarebbe necessario procedere come segue:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=Oct15_HO3-->