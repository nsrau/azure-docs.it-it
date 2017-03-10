---
title: Usare Pig di Hadoop con PowerShell in HDInsight | Documentazione Microsoft
description: Informazioni su come inviare processi Pig a un cluster Hadoop in HDInsight con Azure PowerShell.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: a795f7a8dbb02ad29c517dca6ab06575a8d48bf9
ms.lasthandoff: 03/01/2017

---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Usare Azure PowerShell per eseguire processi Pig con HDInsight

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Questo documento fornisce un esempio di come usare Azure PowerShell per inviare processi Pig in un cluster Hadoop in HDInsight. Pig consente di scrivere processi MapReduce usando un linguaggio (Pig Latin) che modella le trasformazioni di dati, anziché eseguire il mapping e la riduzione delle funzioni.

> [!NOTE]
> Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni Pig Latin usate negli esempi. Per informazioni sul codice Pig Latin usato in questo esempio, vedere [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Prerequisiti

* **Un cluster Azure HDInsight**

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* **Workstation con Azure PowerShell**.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a id="powershell"></a>Eseguire processi Pig mediante PowerShell

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota processi Pig in HDInsight. PowerShell internamente usa le chiamate REST verso [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di processi Pig in un cluster HDInsight remoto, vengono usati i seguenti cmdlet:

* **Login-AzureRmAccount**: autentica Azure PowerShell nella sottoscrizione di Azure
* **New-AzureRmHDInsightPigJobDefinition**: crea una *definizione del processo* usando le istruzioni Pig Latin specificate
* **Start-AzureRmHDInsightJob**: invia la definizione del processo a HDInsight, avvia il processo e restituisce un oggetto *job* che può essere usato per verificare lo stato del processo.
* **Wait-AzureRmHDInsightJob**: usa l'oggetto job per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.
* **Get-AzureRmHDInsightJobOutput**: viene usato per recuperare l'output del processo.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight.

1. Usando un editor, salvare il seguente codice come **pigjob.ps1**.
   
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        # Get cluster info
        $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
        $creds=Get-Credential -Message "Enter the login for the cluster"

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"

        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds


1. Aprire un nuovo prompt dei comandi di Windows PowerShell. Passare al percorso del file **pigjob.ps1** , quindi usare il seguente comando per eseguire lo script:
   
        .\pigjob.ps1
   
    Verrà innanzitutto richiesto di accedere alla sottoscrizione di Azure. In un secondo momento, verranno richiesti il nome dell'account HTTPS/Admin e la password per il cluster HDInsight.

2. Quando viene completato, il processo restituisce informazioni simili alle seguenti:
   
        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Risoluzione dei problemi

Se al termine del processo non vengono restituite informazioni, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni relative all'errore per questo processo, aggiungere il seguente comando alla fine del file **pigjob.ps1** , salvare il file, quindi eseguirlo nuovamente.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Vengono restituite le informazioni scritte in STDERR nel server durante l'esecuzione del processo. Tali informazioni possono essere utili per determinare la causa del problema.

## <a id="summary"></a>Riepilogo
Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire processi Pig in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a id="nextsteps"></a>Passaggi successivi
Per informazioni generali su Pig in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)


