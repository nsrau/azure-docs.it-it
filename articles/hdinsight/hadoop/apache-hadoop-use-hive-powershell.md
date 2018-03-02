---
title: Usare Hive di Hadoop con PowerShell in HDInsight - Azure | Microsoft Docs
description: Usare PowerShell per eseguire query Hive in Hadoop in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/26/2018
ms.author: larryfr
ms.openlocfilehash: fbd5ad2aedf0c3022e702a63f8e3d12735b41313
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="run-hive-queries-using-powershell"></a>Esecuzione di query Hive usando PowerShell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Questo documento fornisce un esempio di come usare Azure PowerShell nel gruppo di risorse Azure per eseguire query Hive in un cluster Hadoop in HDInsight.

> [!NOTE]
> Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Hadoop basato su Linux in HDInsight versione 3.4 o successiva.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un client con Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Eseguire una query Hive

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota query Hive in HDInsight. I cmdlet effettuano internamente chiamate REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) sul cluster HDInsight.

Durante l'esecuzione di query Hive in un cluster HDInsight remoto, vengono usati i seguenti cmdlet:

* `Add-AzureRmAccount`: esegue l'autenticazione di Azure PowerShell nella sottoscrizione di Azure.
* `New-AzureRmHDInsightHiveJobDefinition`: crea una *definizione del processo* usando le istruzioni HiveQL specificate.
* `Start-AzureRmHDInsightJob`: invia la definizione del processo ad HDInsight e avvia il processo. Viene restituito un oggetto del *processo*.
* `Wait-AzureRmHDInsightJob`: usa l'oggetto processo per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.
* `Get-AzureRmHDInsightJobOutput`: usato per recuperare l'output del processo.
* `Invoke-AzureRmHDInsightHiveJob`: usato per eseguire le istruzioni HiveQL. Questo cmdlet blocca il completamento della query, quindi restituisce i risultati.
* `Use-AzureRmHDInsightCluster`: imposta il cluster corrente da usare per il comando `Invoke-AzureRmHDInsightHiveJob`.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight:

1. Usando un editor salvare il codice seguente come `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Quindi, aprire un nuovo prompt dei comandi di **Azure PowerShell** . Passare alla directory del file `hivejob.ps1` e quindi usare il comando seguente per eseguire lo script:

        .\hivejob.ps1

    Quando viene eseguito lo script, viene richiesto di immettere il nome cluster e le credenziali dell'account HTTPS/Admin per il cluster. Può anche essere richiesto di eseguire l'accesso alla sottoscrizione di Azure.

3. Al termine, il processo restituisce informazioni simili al testo seguente:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Come accennato in precedenza, è possibile usare `Invoke-Hive` per eseguire una query e attendere la risposta. Usare lo script seguente per verificare il funzionamento di Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    L'output ha un aspetto simile al testo seguente:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Per query HiveQL più lunghe, è possibile usare il cmdlet **Here-Strings** di Azure PowerShell o un file di script HiveQL. Il frammento di codice seguente illustra come usare il cmdlet `Invoke-Hive` per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in wasb://.
   >
   > `Invoke-AzureRmHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Per altre informazioni su **Here-Strings**, vedere <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Using Windows PowerShell "Here-Strings"</a> (Uso di Here-Strings di Windows PowerShell).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se al termine del processo non viene restituita alcuna informazione, visualizzare i log degli errori. Per visualizzare le informazioni sugli errori relative a questo processo, aggiungere il codice seguente alla fine del file `hivejob.ps1`, salvare il file e quindi eseguirlo nuovamente.

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Questo cmdlet restituisce le informazioni scritte in STDERR durante l'esecuzione del processo.

## <a name="summary"></a>Summary

Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)
