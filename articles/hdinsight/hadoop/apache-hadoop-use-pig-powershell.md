---
title: Usare Apache Pig con PowerShell in HDInsight - Azure
description: Informazioni su come inviare processi Apache Pig a un cluster Apache Hadoop in HDInsight con Azure PowerShell.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 1e9f6778f12f4f6260bfc20c3a78f7929f13405b
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634533"
---
# <a name="use-azure-powershell-to-run-apache-pig-jobs-with-hdinsight"></a>Usare Azure PowerShell per eseguire processi Apache Pig con HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Questo documento fornisce un esempio di come usare Azure PowerShell per inviare processi Apache Pig a un cluster Apache Hadoop in HDInsight. Pig consente di scrivere processi MapReduce usando un linguaggio (Pig Latin) che modella le trasformazioni di dati, anziché eseguire il mapping e la riduzione delle funzioni.

> [!NOTE]
> Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni Pig Latin usate negli esempi. Per informazioni sul codice Pig Latin usato in questo esempio, vedere [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Prerequisiti

* **Un cluster Azure HDInsight**

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Workstation con Azure PowerShell**.

## <a id="powershell"></a>Eseguire un processo Pig

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota processi Pig in HDInsight. PowerShell internamente usa le chiamate REST verso [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) in esecuzione nel cluster HDInsight.

Durante l'esecuzione di processi Pig in un cluster HDInsight remoto, vengono usati i seguenti cmdlet:

* **Connect-AzureRmAccount**: autentica Azure PowerShell nella sottoscrizione di Azure.
* **New-AzureRmHDInsightPigJobDefinition**: crea una *definizione del processo* usando le istruzioni Pig Latin specificate.
* **Start-AzureRmHDInsightJob**: invia la definizione del processo ad HDInsight e avvia il processo. Viene restituito un oggetto del *processo*.
* **Wait-AzureRmHDInsightJob**: usa l'oggetto job per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.
* **Get-AzureRmHDInsightJobOutput**: viene usato per recuperare l'output del processo.

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

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)
