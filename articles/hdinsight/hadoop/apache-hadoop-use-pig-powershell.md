---
title: Usare Apache Pig con PowerShell in HDInsight - Azure
description: Informazioni su come inviare processi Apache Pig a un cluster Apache Hadoop in HDInsight tramite Azure PowerShell.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 69a45a0c2c21ffafde8a4b366e1f3e90b7c8f59a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012608"
---
# <a name="use-azure-powershell-to-run-apache-pig-jobs-with-hdinsight"></a>Usare Azure PowerShell per eseguire processi Apache Pig con HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Questo documento fornisce un esempio dell'uso di Azure PowerShell per inviare processi Apache Pig a un Apache Hadoop nel cluster HDInsight. Pig consente di scrivere processi MapReduce usando un linguaggio (Pig Latin) che modella le trasformazioni di dati, anziché eseguire il mapping e la riduzione delle funzioni.

> [!NOTE]  
> Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni Pig Latin usate negli esempi. Per informazioni sul codice Pig Latin usato in questo esempio, vedere [Use Apache Pig with Apache Hadoop on HDInsight](hdinsight-use-pig.md) (Usare Apache Pig con Apache Hadoop su HDInsight).

## <a id="prereq"></a>Prerequisiti

* **Un cluster Azure HDInsight**

  > [!IMPORTANT]  
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Workstation con Azure PowerShell**.

## <a id="powershell"></a>Eseguire un processo Apache Pig

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota processi Pig in HDInsight. PowerShell internamente usa le chiamate REST verso [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di processi Pig in un cluster HDInsight remoto, vengono usati i seguenti cmdlet:

* **Connect-AzureRmAccount**: esegue l'autenticazione di Azure PowerShell nella sottoscrizione di Azure.
* **New-AzureRmHDInsightPigJobDefinition**: crea una *definizione del processo* usando le istruzioni Pig Latin specificate.
* **Start-AzureRmHDInsightJob**: invia la definizione del processo ad HDInsight e avvia il processo. Viene restituito un oggetto del *processo*.
* **Wait-AzureRmHDInsightJob**: usa l'oggetto del processo per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.
* **Get-AzureRmHDInsightJobOutput**: usato per recuperare l'output del processo.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight.

1. Usando un editor, salvare il seguente codice come **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Aprire un nuovo prompt dei comandi di Windows PowerShell. Passare al percorso del file **pigjob.ps1** , quindi usare il seguente comando per eseguire lo script:

        .\pigjob.ps1

    Viene richiesto di accedere alla sottoscrizione di Azure. Vengono quindi richiesti il nome dell'account HTTPS/Amministratore e la password per il cluster HDInsight.

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

Se al termine del processo non viene restituita alcuna informazione, visualizzare i log degli errori. Per visualizzare informazioni relative all'errore per questo processo, aggiungere il seguente comando alla fine del file **pigjob.ps1** , salvare il file, quindi eseguirlo nuovamente.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Questo cmdlet restituisce le informazioni scritte in STDERR durante l'esecuzione del processo.

## <a id="summary"></a>Riepilogo
Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire processi Pig in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a id="nextsteps"></a>Passaggi successivi
Per informazioni generali su Pig in HDInsight:

* [Usare Apache Pig con Apache Hadoop su HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)
