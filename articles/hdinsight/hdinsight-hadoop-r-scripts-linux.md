---
title: Installare R in HDInsight basati su Linux | Microsoft Docs
description: Informazioni su come installare e usare R per personalizzare cluster Hadoop basati su Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: larryfr

---
# Installare e usare R nei cluster Hadoop HDInsight
È possibile installare R in qualsiasi tipo di cluster in Hadoop su HDInsight usando la personalizzazione dei cluster **Script Action**. Questo consente ai data scientist e agli analisti di usare R per distribuire il potente framework di programmazione MapReduce/YARN per elaborare grandi quantità di dati nei cluster Hadoop distribuiti in HDInsight.

> [!IMPORTANT]
> L'offerta del [piano Premium](https://azure.microsoft.com/pricing/details/hdinsight/) per HDInsight include R Server nell'ambito del cluster HDInsight. In questo modo gli script R possono usare MapReduce e Spark per eseguire i calcoli distribuiti. Per altre informazioni, vedere [Introduzione all'uso di R Server su HDInsight](hdinsight-hadoop-r-server-get-started.md).
> 
> 

## Che cos'è R?
<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a> è un linguaggio open source e un ambiente per l'elaborazione statistica. R fornisce centinaia di funzioni statistiche integrate e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica. R è l'ambiente di programmazione preferito da numerosi statistici e scienziati professionisti attivi in un'ampia gamma di campi.

Gli script R possono essere eseguiti su cluster Hadoop in HDInsight personalizzati mediante Script Action al momento della creazione per installare l'ambiente R. R è compatibile con WASB (Azure Blob Storage), consentendo di elaborare i dati archiviati usando R in HDInsight.

## Caratteristiche dello script
L'azione di script usata per installare R nel cluster HDInsight consente di installare i pacchetti Ubuntu seguenti che offrono un'installazione di R di base:

* [r-base](http://packages.ubuntu.com/precise/r-base): pacchetto R GNU di base
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): pacchetti R GNU aggiuntivi

Vengono inoltre installati i pacchetti RHadoop seguenti, che garantiscono l'integrazione con MapReduce e HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): consente agli sviluppatori di R di usare Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): consente agli sviluppatori di R di usare Hadoop HDFS (WASB per HDInsight)

Vengono inoltre installati i pacchetti R seguenti:

| Pacchetto R | Funzionalità offerte |
| --- | --- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) |Interfaccia da R a Java di basso livello. |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) |Integrazione di R e C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) |Serializzazione/deserializzazione di oggetti R in JSON. |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) |Funzioni per le operazioni bit per bit sui vettori di tipo Integer. |
| [digest](https://cran.r-project.org/web/packages/digest/index.html) |Creazione di digest hash crittografici di oggetti R. |
| [functional](https://cran.r-project.org/web/packages/functional/index.html) |Curry, Compose e altre funzioni di ordine superiore. |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) |Ristrutturazione e aggregazione dei dati in modo flessibile. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) |Wrapper semplici e coerenti per le comuni operazioni di stringa. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) |Strumenti per la divisione, l'applicazione e la combinazione dei dati. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) |Strumenti per lo spostamento di statistiche nella finestra, GIF, Base64, ROC AUC e così via. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) |Funzioni di corrispondenza approssimativa di stringa distanza di stringa. |

## Installare R mediante azioni di script
L'azione script seguente viene usata per installare R in un cluster HDInsight. https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh

Questa sezione fornisce istruzioni su come usare lo script quando si crea un nuovo cluster usando il portale di Azure.

> [!NOTE]
> Per applicare le azioni script è possibile usare anche Azure PowerShell, l'interfaccia della riga di comando di Azure, HDInsight .NET SDK o i modelli di Azure Resource Manager. È anche possibile applicare azioni script a cluster già in esecuzione. Per altre informazioni, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).
> 
> 

1. Avviare il provisioning di un cluster seguendo i passaggi descritti in [Effettuare il provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md#portal) senza completarlo.
2. Nel pannello **Configurazione facoltativa** selezionare **Azioni script** e specificare le informazioni seguenti:
   
   * **NOME**: immettere un nome descrittivo per l'azione script.
   * **URI SCRIPT**: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
   * **HEAD**: selezionare questa opzione.
   * **LAVORO**: selezionare questa opzione.
   * **ZOOKEEPER**: selezionare questa opzione per l'installazione nel nodo Zookeeper
   * **PARAMETERS**: lasciare questo campo vuoto
3. Nella parte inferiore di **Azioni di script** usare il pulsante **Seleziona** per salvare la configurazione. Usare infine il pulsante **Seleziona** nella parte inferiore del pannello **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.
4. Continuare il provisioning del cluster come descritto in [Effettuare il provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## Eseguire gli script R
Al termine del provisioning del cluster, seguire questa procedura per usare R per eseguire un'operazione MapReduce nel cluster.

1. Connettersi al cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:
   
   * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. Dal prompt `username@hn0-CLUSTERNAME:~$` immettere il comando seguente per avviare una sessione R interattiva:
   
        R
3. Immettere il programma R seguente. In questo modo verranno generati i numeri da 1 a 100 e verranno moltiplicati per 2.
   
        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
   
    La prima riga chiama la libreria RHadoop rmr2, usata per le operazioni MapReduce.
   
    La seconda riga genera i valori 1 - 100, quindi li archivia nel file system Hadoop mediante `to.dfs`.
   
    La terza riga crea un processo MapReduce tramite la funzionalità offerta da rmr2 e avvia l'elaborazione. Quando l'elaborazione ha inizio, si dovrebbero visualizzare più righe che scorrono.
4. Usare quindi il comando seguente per visualizzare il percorso temporaneo in cui è stato archiviato l'output di MapReduce:
   
        print(calc())
   
    Il percorso dovrebbe essere simile a `/tmp/file5f615d870ad2`. Per visualizzare l'output effettivo, usare il comando seguente:
   
        print(from.dfs(calc))
   
    L'output dovrebbe essere simile al seguente:
   
        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200
5. Per uscire da R, immettere quanto segue:
   
        q()

## Passaggi successivi
* [Installare e usare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Hue è un'interfaccia utente che semplifica la creazione, l'esecuzione e il salvataggio di processi Pig e Hive, nonché l'esplorazione dell'archivio predefinito per il cluster HDInsight.
* [Installare Giraph in cluster HDInsight](hdinsight-hadoop-giraph-install.md). Usare la personalizzazione cluster per installare Giraph in cluster Hadoop di HDInsight. Giraph consente di elaborare grafici con Hadoop e può essere usato con Azure HDInsight.
* [Installare Solr in cluster HDInsight](hdinsight-hadoop-solr-install.md). Usare la personalizzazione cluster per installare Solr in cluster Hadoop di HDInsight. Solr consente di eseguire operazioni di ricerca avanzate sui dati archiviati.
* [Installare Hue nei cluster HDInsight](hdinsight-hadoop-hue-linux.md). Usare la personalizzazione dei cluster per installare Hue nei cluster Hadoop di HDInsight. Hue è un insieme di applicazioni Web che consente di interagire con un cluster Hadoop.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0921_2016-->