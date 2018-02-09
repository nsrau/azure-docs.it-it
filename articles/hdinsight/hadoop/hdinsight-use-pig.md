---
title: Usare Pig di Hadoop in HDInsight | Microsoft Docs
description: Informazioni su come usare Pig con Hadoop in HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: larryfr
ms.openlocfilehash: ca68391db99d882887e41005928a30610c7cb930
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Usare Pig con Hadoop in HDInsight

Informazioni su come usare [Apache Pig](http://pig.apache.org/) con HDInsight.

Pig è una piattaforma che consente la creazione di programmi per Hadoop usando un linguaggio procedurale denominato *Pig Latin*. Pig è un'alternativa a Java per la creazione di soluzioni *MapReduce* ed è incluso in Azure HDInsight. Usare la tabella seguente per individuare i vari modi in cui Pig può essere usato con HDInsight:

| **Usare questo** se si desidera... | ...una shell **interattiva** | ...elaborazione**batch** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [API REST](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [.NET SDK per Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux o Windows |Windows (per ora) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux o Windows |Windows |

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Perché usare Pig

Quando si elaborano dati usando MapReduce in Hadoop, uno dei problemi principali è riuscire a implementare la logica di elaborazione usando solo una mappa e una funzione di riduzione. In caso di esigenze di elaborazione complesse, è spesso necessario interrompere l'elaborazione in più operazioni di MapReduce concatenate insieme per ottenere il risultato desiderato.

Pig consente di definire l'elaborazione come una serie di trasformazioni a cui vengono sottoposti i dati fino a raggiungere l'output desiderato.

Il linguaggio Pig Latin consente di descrivere il flusso dati dall'input non elaborato fino all'output desiderato, attraverso una o più trasformazioni. I programmi in Pig Latin seguono questo modello generale:

* **Load**: lettura dei dati da manipolare dal file system

* **Transform**: manipolazione dei dati

* **Dump or store**: output dei dati sullo schermo o archiviazione per l'elaborazione

### <a name="user-defined-functions"></a>Funzioni definite dall'utente

Pig Latin supporta anche funzioni definite dall'utente (UDF), che consentono di richiamare componenti esterni in grado di implementare logica difficile da modellare in Pig Latin.

Per altre informazioni su Pig Latin, vedere il [manuale di riferimento di Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e il [manuale di riferimento di Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Per un esempio sull'uso di funzioni definite dall'utente con Pig, vedere i documenti seguenti:

* [Usare DataFu con Pig in HDInsight](apache-hadoop-use-pig-datafu-udf.md) - DataFu è una raccolta di utili funzioni definite dall'utente gestite mediante Apache
* [Usare Python con Pig e Hive in HDInsight](python-udf-hdinsight.md)
* [Usare C# con Hive e Pig in HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Dati di esempio

HDInsight offre diversi set di dati di esempio, archiviati nelle directory `/example/data` e `/HdiSamples`. Queste directory si trovano nella risorsa di archiviazione predefinita per il cluster. L'esempio di Pig in questo documento usa il file *log4j* da `/example/data/sample.log`.

Ogni log all'interno del file è costituito da una riga di campi che contiene un campo `[LOG LEVEL]` per visualizzare il tipo e la gravità. Ad esempio:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Nell'esempio precedente, il livello log è ERROR.

> [!NOTE]
> È anche possibile generare un file log4j usando lo strumento di registrazione [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e quindi caricandolo nel BLOB. Per istruzioni, vedere [Caricamento di dati in HDInsight](../hdinsight-upload-data.md) . Per altre informazioni sul modo in cui HDInsight usa l'archiviazione BLOB di Azure, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](../hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Processo di esempio

Il processo Pig Latin seguente carica il file `sample.log` dalla risorsa di archiviazione predefinita per il cluster HDInsight. Esegue quindi una serie di trasformazioni che generano un conteggio delle occorrenze di ciascun livello di log presente nei dati di input. I risultati vengono scritti in STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

L'immagine seguente illustra un riepilogo degli effetti di ogni trasformazione sui dati.

![Rappresentazione grafica delle trasformazioni][image-hdi-pig-data-transformation]

## <a id="run"></a>Eseguire il processo Pig Latin

HDInsight è in grado di eseguire processi Pig Latin in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo** se si desidera... | ...una shell **interattiva** | ...elaborazione**batch** | ...con questo **sistema operativo cluster** | ...da questo **client** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [Curl](apache-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [.NET SDK per Hadoop](apache-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux o Windows |Windows (per ora) |
| [Windows PowerShell](apache-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux o Windows |Windows |

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>Pig e SQL Server Integration Services

È possibile usare SQL Server Integration Services (SSIS) per eseguire un processo Pig. Il Feature Pack di Azure per SSIS fornisce i seguenti componenti che funzionano con i processi Pig in HDInsight.

* [Attività di Pig di Azure HDInsight][pigtask]

* [Gestione connessione della sottoscrizione di Azure][connectionmanager]

Altre informazioni sul Feature Pack di Azure per SSIS sono disponibili [qui][ssispack].

## <a id="nextsteps"></a>Passaggi successivi
Dopo avere appreso come usare Pig con HDInsight, vedere i collegamenti seguenti per scoprire altri modi di usare Azure HDInsight.

* [Caricare dati in HDInsight](../hdinsight-upload-data.md)
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop.md)
* [Usare Oozie con HDInsight](../hdinsight-use-oozie.md)
* [Usare processi MapReduce con HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
