---
title: Usare Pig di Hadoop in HDInsight | Documentazione Microsoft
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
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 8b924f29594a9f0c8c161661b216a12ff65b42a9
ms.lasthandoff: 02/17/2017


---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Usare Pig con Hadoop in HDInsight
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache Pig](http://pig.apache.org/) è una piattaforma che consente la creazione di programmi per Hadoop usando un linguaggio procedurale denominato *Pig Latin*. Pig è un'alternativa a Java per la creazione di soluzioni *MapReduce* ed è incluso in Azure HDInsight.

Questo articolo illustra come usare Pig con HDInsight.

## <a id="why"></a>Perché usare Pig?
Quando si elaborano dati usando MapReduce in Hadoop, uno dei problemi principali è riuscire a implementare la logica di elaborazione usando solo una mappa e una funzione di riduzione. In caso di esigenze di elaborazione complesse, è spesso necessario interrompere l'elaborazione in più operazioni di MapReduce concatenate insieme per ottenere il risultato desiderato.

Pig non costringe l'utente a usare solo una mappa e una funzione di riduzione, ma consente di definire l'elaborazione come una serie di trasformazioni dei flussi dati fino a raggiungere l'output desiderato.

Il linguaggio Pig Latin consente di descrivere il flusso dati dall'input non elaborato fino all'output desiderato, attraverso una o più trasformazioni. I programmi in Pig Latin seguono questo modello generale:

* **Load**: lettura dei dati da manipolare dal file system
* **Transform**: manipolazione dei dati
* **Dump or store**: output dei dati sullo schermo o archiviazione per l'elaborazione

Pig Latin supporta anche funzioni definite dall'utente (UDF), che consentono di richiamare componenti esterni in grado di implementare logica difficile da modellare in Pig Latin.

Per altre informazioni su Pig Latin, vedere il [manuale di riferimento di Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e il [manuale di riferimento di Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Per un esempio sull'uso di funzioni definite dall'utente con Pig, vedere i documenti seguenti:

* [Usare DataFu con Pig in HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu è una raccolta di utili funzioni definite dall'utente gestite mediante Apache
* [Usare Python con Pig e Hive in HDInsight](hdinsight-python.md)
* [Usare C# con Hive e Pig in HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Informazioni sui dati di esempio
Questo esempio usa un file di esempio *log4j*, che viene archiviato in **/example/data/sample.log** nel contenitore dell'archivio BLOB. Ogni log all'interno del file è costituito da una riga di campi che contiene un campo `[LOG LEVEL]` per visualizzare il tipo e la gravità. Ad esempio:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Nell'esempio precedente, il livello log è ERROR.

> [!NOTE]
> È anche possibile generare un file log4j usando lo strumento di registrazione [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e quindi caricandolo nel BLOB. Per istruzioni, vedere [Caricamento di dati in HDInsight](hdinsight-upload-data.md) . Per altre informazioni sul modo in cui HDInsight usa l'archiviazione BLOB di Azure, vedere [Usare l'archiviazione BLOB di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).
> 
> 

I dati di esempio vengono archiviati nell'archivio BLOB di Azure, usata da HDInsight come file system predefinito per i cluster Hadoop. HDInsight può accedere ai file archiviati nei BLOB tramite il prefisso **wasb** . Ad esempio, per accedere al file sample.log, usare la sintassi seguente:

    wasbs:///example/data/sample.log

Poiché WASB è la risorsa di archiviazione predefinita per HDInsight, è anche possibile accedere al file usando **/example/data/sample.log** in Pig Latin.

> [!NOTE]
> La sintassi precedente, **wasbs:///**, consente di accedere ai file archiviati nel contenitore di archiviazione predefinito per il cluster HDInsight. Se durante il provisioning del cluster sono stati specificati account di archiviazione aggiuntivi e si vuole accedere ai file archiviati in tali account, è possibile accedere ai dati specificando il nome del contenitore e l'indirizzo dell'account di archiviazione, ad esempio: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.
> 
> 

## <a id="job"></a>Informazioni sull'app di esempio
Il processo Pig Latin seguente carica il file **sample.log** dal percorso di archiviazione predefinito per il cluster HDInsight. Esegue quindi una serie di trasformazioni che generano un conteggio delle occorrenze di ciascun livello di log presente nei dati di input. I risultati vengono trasmessi a STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

L'immagine seguente illustra un riepilogo dei dati generati da ogni operazione di trasformazione.

![Rappresentazione grafica delle trasformazioni][image-hdi-pig-data-transformation]

## <a id="run"></a>Eseguire il processo Pig Latin
HDInsight è in grado di eseguire processi Pig Latin in vari modi. Usare la tabella seguente per decidere il metodo più adatto alle proprie esigenze, quindi fare clic sul collegamento per visualizzare una procedura dettagliata.

| **Usare questo** se si desidera... | ...una shell **interattiva** | ...elaborazione**batch** | ...con questo **sistema operativo cluster** | ...da questo **sistema operativo client** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](hdinsight-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X o Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [.NET SDK per Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux o Windows |Windows (per ora) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux o Windows |Windows |
| [Desktop remoto](hdinsight-hadoop-use-pig-remote-desktop.md) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Esecuzione di processi Pig in Azure HDInsight tramite SQL Server Integration Services
È inoltre possibile utilizzare SQL Server Integration Services per eseguire un processo Pig. Il Feature Pack di Azure per SSIS fornisce i seguenti componenti che funzionano con i processi Pig in HDInsight.

* [Attività di Pig di Azure HDInsight][pigtask]
* [Gestione connessione della sottoscrizione di Azure][connectionmanager]

Altre informazioni sul Feature Pack di Azure per SSIS sono disponibili [qui][ssispack].

## <a id="nextsteps"></a>Passaggi successivi
Dopo avere appreso come usare Pig con HDInsight, vedere i collegamenti seguenti per scoprire altri modi di usare Azure HDInsight.

* [Caricare dati in HDInsight][hdinsight-upload-data]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Sqoop con Hadoop in HDInsight](hdinsight-use-sqoop.md)
* [Usare Oozie con HDInsight](hdinsight-use-oozie.md)
* [Usare processi MapReduce con HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx


[hdinsight-upload-data]: hdinsight-upload-data.md

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif


