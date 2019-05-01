---
title: Usare MapReduce e PowerShell con Apache Hadoop - Azure HDInsight
description: Informazioni su come usare PowerShell per eseguire in modalità remota processi MapReduce con Apache Hadoop in HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.openlocfilehash: 29e23d5919a953566c803f2b7825a75a2993723c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721800"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Esecuzione di processi MapReduce con Apache Hadoop in HDInsight mediante PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Questo documento fornisce un esempio di come usare Azure PowerShell per eseguire un processo MapReduce in un cluster Hadoop in HDInsight.

## <a id="prereq"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Un cluster Azure HDInsight (Hadoop in HDInsight)**

  > [!IMPORTANT]  
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Workstation con Azure PowerShell**.

## <a id="powershell"></a>Eseguire un processo MapReduce

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota processi MapReduce in HDInsight. PowerShell effettua internamente chiamate REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (chiamato in precedenza Templeton) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di processi MapReduce in un cluster HDInsight remoto, vengono usati i seguenti cmdlet.

* **Connect-AzAccount**: esegue l'autenticazione di Azure PowerShell nella sottoscrizione di Azure.

* **New-AzHDInsightMapReduceJobDefinition**: crea una nuova *definizione di processo* usando le informazioni MapReduce specificate.

* **Start-AzHDInsightJob**: invia la definizione del processo ad HDInsight e avvia il processo. Viene restituito un oggetto del *processo*.

* **Wait-AzHDInsightJob**: usa l'oggetto processo per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.

* **Get-AzHDInsightJobOutput**: usato per recuperare l'output del processo.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight.

1. Usando un editor, salvare il seguente codice come **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Quindi, aprire un nuovo prompt dei comandi di **Azure PowerShell** . Passare al percorso del file **mapreducejob.ps1** , quindi usare il seguente comando per eseguire lo script.

        .\mapreducejob.ps1

    Quando si esegue lo script viene chiesto il nome del cluster HDInsight e l'account di accesso del cluster. Potrebbe anche essere richiesta l'autenticazione alla sottoscrizione di Azure.

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

Se al termine del processo non viene restituita alcuna informazione, visualizzare gli errori relativi al processo. Per visualizzare informazioni relative all'errore per questo processo, aggiungere il seguente comando alla fine del file **mapreducejob.ps1** , salvare il file, quindi eseguirlo nuovamente.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Questo cmdlet restituisce le informazioni scritte in STDERR durante l'esecuzione del processo.

## <a id="summary"></a>Riepilogo

Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire processi MapReduce in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali sui processi MapReduce in HDInsight:

* [Usare MapReduce in HDInsight Hadoop](hdinsight-use-mapreduce.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)
* [Usare Apache Pig con Apache Hadoop su HDInsight](hdinsight-use-pig.md)
