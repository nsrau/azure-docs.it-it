---
title: Usare Hive di Hadoop con PowerShell in HDInsight | Documentazione Microsoft
description: Usare PowerShell per eseguire query Hive in Hadoop in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/07/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: bb5fee05bb14276ab0a9e2309f8a20cb3684df57


---
# <a name="run-hive-queries-using-powershell"></a>Esecuzione di query Hive usando PowerShell
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Questo documento fornisce un esempio di come usare Azure PowerShell nel gruppo di risorse Azure per eseguire query Hive in un cluster Hadoop in HDInsight.

> [!NOTE]
> Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).
> 
> 

**Prerequisiti**

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* **Un cluster Azure HDInsight (Hadoop in HDInsight) (basato su Windows o basato su Linux)**
* **Workstation con Azure PowerShell**.
  
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-hive-queries-using-azure-powershell"></a>Eseguire query Hive usando Azure PowerShell
Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota query Hive in HDInsight. Questo risultato si ottiene internamente usando chiamate REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (chiamato in precedenza Templeton) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di query Hive in un cluster HDInsight remoto, vengono usati i seguenti cmdlet:

* **Add-AzureRmAccount**: autentica Azure PowerShell nella sottoscrizione di Azure
* **New-AzureRmHDInsightHiveJobDefinition**: crea una nuova *definizione del processo* usando le istruzioni HiveQL specificate.
* **Start-AzureRmHDInsightJob**: invia la definizione del processo a HDInsight, avvia il processo e restituisce un oggetto *job* che può essere usato per verificare lo stato del processo.
* **Wait-AzureRmHDInsightJob**: usa l'oggetto job per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.
* **Get-AzureRmHDInsightJobOutput**: viene usato per recuperare l'output del processo.
* **Invoke-AzureRmHDInsightHiveJob**: viene usato per eseguire istruzioni HiveQL. Questo blocca il completamento della query, quindi restituisce i risultati.
* **Use-AzureRmHDInsightCluster**: imposta il cluster corrente per l'uso per il comando **Invoke-AzureRmHDInsightHiveJob**.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight:

1. Usando un editor, salvare il seguente codice come **hivejob.ps1**. È necessario sostituire **CLUSTERNAME** con il nome del cluster HDInsight.
   
        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential
   
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }
   
        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
   
        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 
   
        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green
   
        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds
   
        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds
   
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
2. Quindi, aprire un nuovo prompt dei comandi di **Azure PowerShell** . Passare al percorso del file **hivejob.ps1** , quindi usare il seguente comando per eseguire lo script:
   
        .\hivejob.ps1
   
    Quando viene eseguito lo script, verrà richiesto di immettere le credenziali dell'account HTTPS/Admin per il cluster. Si può anche richiedere di eseguire l'accesso alla sottoscrizione di Azure.
3. Quando viene completato, il processo dovrebbe restituire informazioni simili alle seguenti:
   
        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
4. Come accennato in precedenza, **Invoke-Hive** può essere usato per eseguire una query e attendere la risposta. Usare i seguenti comandi e sostituire **CLUSTERNAME** con il nome del cluster:
   
        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString
   
    L'output sarà simile al seguente:
   
        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
   
   > [!NOTE]
   > Per query HiveQL più lunghe, è possibile usare il cmdlet **Here-Strings** di Azure PowerShell o un file di script HiveQL. Nel frammento di codice seguente viene illustrato come usare il cmdlet **Invoke-Hive** per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in wasb://.
   > 
   > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   > 
   > Per altre informazioni su **Here-Strings**, vedere <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Using Windows PowerShell "Here-Strings"</a> (Uso di Here-Strings di Windows PowerShell).
   > 
   > 

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se al termine del processo non vengono restituite informazioni, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni relative all'errore per questo processo, aggiungere quanto segue alla fine del file **hivejob.ps1** , salvare il file, quindi eseguirlo nuovamente.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Vengono restituite le informazioni scritte in STDERR nel server durante l'esecuzione del processo. Tali informazioni possono essere utili per determinare la causa del problema.

## <a name="summary"></a>Riepilogo
Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Nov16_HO3-->


