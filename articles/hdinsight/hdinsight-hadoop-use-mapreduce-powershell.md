---
title: Usare MapReduce e PowerShell con Hadoop | Microsoft Docs
description: "Informazioni su come usare PowerShell per eseguire in modalità remota processi MapReduce con Hadoop in HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: b24bfb08e692ec7ab6da9c60515d7cbeec9589b3
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Esecuzione di processi MapReduce con Hadoop in HDInsight mediante PowerShell

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Questo documento fornisce un esempio di come usare Azure PowerShell per eseguire un processo MapReduce in un cluster Hadoop in HDInsight.

## <a id="prereq"></a>Prerequisiti

* **Un cluster Azure HDInsight (Hadoop in HDInsight)**

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

* **Workstation con Azure PowerShell**.

## <a id="powershell"></a>Eseguire un processo MapReduce con Azure PowerShell

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota processi MapReduce in HDInsight. Questo risultato si ottiene internamente usando chiamate REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (chiamato in precedenza Templeton) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di processi MapReduce in un cluster HDInsight remoto, vengono usati i seguenti cmdlet.

* **Login-AzureRmAccount**: autentica Azure PowerShell nella sottoscrizione di Azure.

* **New-AzureRmHDInsightMapReduceJobDefinition**: crea una nuova *definizione di processo* usando le informazioni MapReduce specificate.

* **Start-AzureRmHDInsightJob**: invia la definizione del processo a HDInsight, avvia il processo e restituisce un oggetto *job* che può essere usato per verificare lo stato del processo.

* **Wait-AzureRmHDInsightJob**: usa l'oggetto job per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.

* **Get-AzureRmHDInsightJobOutput**: viene usato per recuperare l'output del processo.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight.

1. Usando un editor, salvare il seguente codice come **mapreducejob.ps1**.

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Quindi, aprire un nuovo prompt dei comandi di **Azure PowerShell** . Passare al percorso del file **mapreducejob.ps1** , quindi usare il seguente comando per eseguire lo script.

        .\mapreducejob.ps1

    Quando si esegue lo script viene chiesto il nome del cluster HDInsight e il nome dell'account Admin/HTTPS e la password per il cluster. Potrebbe anche essere richiesta l'autenticazione alla sottoscrizione di Azure.

3. Al termine del processo, viene visualizzato un output simile al testo seguente:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Questo output indica che il processo è stato completato correttamente.

    > [!NOTE]
    > Se **ExitCode** è un valore diverso da 0, vedere [Risoluzione dei problemi](#troubleshooting).

    Questo esempio consente anche di archiviare i file scaricati in un file **output.txt** nella directory in cui viene eseguito lo script.

### <a name="view-output"></a>Visualizzare l’output

Per vedere le parole e i numeri generati dal processo, aprire il file **output.txt** in un editor di testo.

> [!NOTE]
> I file di output di un processo MapReduce non sono modificabili. Se pertanto si esegue di nuovo l'esempio, è necessario cambiare il nome del file di output.

## <a id="troubleshooting"></a>Risoluzione dei problemi

Se al termine del processo non vengono restituite informazioni, potrebbe essersi verificato un errore durante l'elaborazione. Per visualizzare informazioni relative all'errore per questo processo, aggiungere il seguente comando alla fine del file **mapreducejob.ps1** , salvare il file, quindi eseguirlo nuovamente.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Questo cmdlet restituisce le informazioni scritte in STDERR nel server durante l'esecuzione del processo, che possono essere utili per determinare la causa del problema.

## <a id="summary"></a>Riepilogo

Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire processi MapReduce in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce in HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

