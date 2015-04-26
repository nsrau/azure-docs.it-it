<properties 
	pageTitle="Raccogliere dump dell'heap per scopi di debug e analisi| Azure" 
	description="Raccogliere dump dell'heap per scopi di debug e analisi" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="bradsev"/>

# Raccogliere dump dell'heap per scopi di debug e analisi

È possibile raccogliere dump dell'heap per i servizi Hadoop e posizionarli all'interno dell'account di archiviazione BLOB di un utente in HDInsightHeapDumps/.  I file di dump relativi a un servizio con heap contengono uno snapshot della memoria dell'applicazione. Sono inclusi i valori delle variabili al momento della creazione del dump.

La raccolta di dump dell'heap relativi ai diversi servizi deve essere abilitata sui singoli cluster. Per impostazione predefinita, questa funzionalità è disattivata per un cluster. Poiché i dump dell'heap possono essere di dimensioni elevate, è consigliabile monitorare l'account di archiviazione BLOB in cui vengono salvati dopo l'abilitazione della raccolta.

## Contenuto dell'articolo

- [Per quali servizi è possibile abilitare i dump dell'heap?](#whichServices)
- [Elementi di configurazione che abilitano i dump dell'heap](#configuration)
- [Come abilitare i dump dell'heap con Azure HDInsight PowerShell](#powershell)
- [Come abilitare i dump dell'heap con HDInsight .NET SDK](#sdk)


## <a name="whichServices"></a>Per quali servizi è possibile abilitare i dump dell'heap?

I servizi per cui, se necessario, è possibile abilitare i dump dell'heap sono: 

*  **hcatalog**: tempelton
*  **hive**: hiveserver2, metastore, derbyserver 
*  **mapreduce**: jobhistoryserver 
*  **yarn**: resourcemanager, nodemanager, timelineserver 
*  **hdfs**: datanode, secondarynamenode, namenode

## <a name="configuration"></a>Elementi di configurazione che abilitano i dump dell'heap

Per attivare i dump dell'heap per un servizio, l'utente deve impostare gli elementi di configurazione appropriati nella sezione relativa al servizio in questione, specificata da service_name.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

Il valore di <service_name> può essere uno qualsiasi dei servizi sopra elencati: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode o namenode.

## <a name="powershell"></a>Come abilitare i dump dell'heap con Azure HDInsight PowerShell

Ad esempio, per attivare i dump dell'heap usando PowerShell per jobhistoryserver l'utente deve procedere come segue:

Mediante powershell sdk:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Come abilitare i dump dell'heap con HDInsight .NET SDK

Ad esempio, per attivare i dump dell'heap usando .NET SDK per jobhistoryserver l'utente deve procedere come segue:

Mediante c# sdk:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));




<!--HONumber=42-->
