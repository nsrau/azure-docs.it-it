---
title: Usare R in HDInsight per la personalizzazione dei cluster - Azure | Documentazione Microsoft
description: Informazioni su come installare R tramite Azione di script e su come usare R nei cluster HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: be851270-afa5-4af0-a69e-2d343a4deeb7
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 5b9b793d49217acd9f0c6c518596a7afb5600d69
ms.contentlocale: it-it
ms.lasthandoff: 06/10/2017

---
# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installare e usare R nei cluster Hadoop HDInsight

Informazioni su come personalizzare i cluster HDInsight basati su Windows con R usando Azione di script e su come usare R nei cluster HDInsight. L'[offerta per HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) include R Server come parte del cluster HDInsight. In questo modo gli script R possono usare MapReduce e Spark per eseguire i calcoli distribuiti. Per altre informazioni, vedere [Introduzione all'uso di R Server in HDInsight](hdinsight-hadoop-r-server-get-started.md). Per informazioni sull'uso di R con un cluster basato su Linux, vedere [Installare e usare R nei cluster Hadoop di HDInsight (Linux)](hdinsight-hadoop-r-scripts-linux.md).

È possibile installare R in qualsiasi tipo di cluster (Hadoop, Storm, HBase, Spark) su Azure HDInsight usando *Azione di script*. Uno script di esempio per l'installazione di R in un cluster HDInsight è disponibile in un BLOB di archiviazione di sola lettura di Azure all'indirizzo [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

**Articoli correlati**

* [Installare e usare R nei cluster Hadoop di HDInsight (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informazioni generali sulla creazione di cluster HDInsight
* [Personalizzare cluster HDInsight mediante l'azione script][hdinsight-cluster-customize]: informazioni generali sulla personalizzazione di cluster HDInsight tramite Azione script.
* [Sviluppare script di Azione script per HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Che cos'è R?
<a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a> è un linguaggio open source e un ambiente per l'elaborazione statistica. R fornisce centinaia di funzioni statistiche integrate e un proprio linguaggio che combina aspetti di programmazione funzionale con aspetti di programmazione orientata agli oggetti. Offre inoltre funzionalità complete di grafica. R è l'ambiente di programmazione preferito da numerosi statistici e scienziati professionisti attivi in un'ampia gamma di campi.

R è compatibile con WASB (Azure Blob Storage), consentendo di elaborare i dati archiviati usando R in HDInsight.  

## <a name="install-r"></a>Installare R
Per installare R in un cluster HDInsight è disponibile uno [script di esempio](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) da un BLOB di sola lettura in archiviazione di Azure. Questa sezione contiene istruzioni su come usare lo script di esempio quando si creano cluster usando il portale di Azure.

> [!NOTE]
> Lo script di esempio è stato introdotto con il cluster HDInsight versione 3.1. Per altre informazioni sulle versioni dei cluster HDInsight, vedere [Versioni cluster HDInsight](hdinsight-component-versioning.md).
>
>

1. Durante la creazione di un cluster HDInsight dal portale, fare clic su **Configurazione facoltativa** e quindi fare clic su **Azioni script**.
2. Nella pagina **Azioni di script** immettere i valori seguenti:

    ![Usare l'azione script per personalizzare un cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Usare l'azione script per personalizzare un cluster")

    <table border='1'>
        <tr><th>Proprietà</th><th>Valore</th></tr>
        <tr><td>Nome</td>
            <td>Specificare un nome per l'azione script, ad esempio <b>Installa R</b>.</td></tr>
        <tr><td>URI script</td>
            <td>Specificare l'URI per lo script che viene richiamato per personalizzare il cluster, ad esempio <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Tipo di nodo</td>
            <td>Specificare i nodi in cui viene eseguito lo script di personalizzazione. È possibile scegliere <b>Tutti i nodi</b>, <b>Solo nodi head</b> o <b>Solo nodi di lavoro</b>.
        <tr><td>Parametri</td>
            <td>Specificare i parametri, se richiesti dallo script. Tuttavia, lo script per installare R non richiede alcun parametro, di conseguenza è possibile lasciare vuoto questo campo.</td></tr>
    </table>

    È possibile aggiungere altre azioni di script per installare più componenti nel cluster. Dopo aver aggiunto gli script, fare clic sul segno di spunta per avviare la creazione del cluster.

È inoltre possibile usare lo script per installare R in HDInsight usando Azure PowerShell o HDInsight .NET SDK. Le istruzioni relative a queste procedure vengono fornite più avanti in questo articolo.

## <a name="run-r-scripts"></a>Eseguire gli script R
Questa sezione descrive come eseguire uno script R nel cluster Hadoop con HDInsight.

1. **Stabilire una connessione desktop remoto al cluster**: dal portale, abilitare il desktop remoto per il cluster creato con R installato, quindi connettersi al cluster. Per istruzioni, vedere [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Aprire la console di R**: l'installazione di R inserisce un collegamento alla console di R sul desktop del nodo head. Fare clic su di esso per aprire la console di R.
3. **Eseguire lo script R**: lo script R può essere eseguito direttamente dalla console di R incollandolo in essa, selezionandolo e premendo Invio. Di seguito è riportato un semplice script di esempio che genera i numeri da 1 a 100 e li moltiplica per 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

Le prime due righe chiamano le librerie RHadoop che vengono installate con R. La riga finale stampa i risultati nella console. L'output dovrebbe essere simile al seguente:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Installare R usando Azure PowerShell
Vedere [Personalizzare cluster HDInsight usando l'azione script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  L'esempio illustra come installare Spark tramite Azure PowerShell. È necessario personalizzare lo script da usare [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Installare R usando .NET SDK
Vedere [Personalizzare cluster HDInsight usando l'azione script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). L'esempio illustra come installare Spark tramite .NET SDK. È necessario personalizzare lo script da usare [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).

## <a name="see-also"></a>Vedere anche
* [Installare e usare R nei cluster Hadoop di HDInsight (Linux)](hdinsight-hadoop-r-scripts-linux.md)
* [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informazioni generali sulla creazione di cluster HDInsight
* [Personalizzare cluster HDInsight mediante l'azione script][hdinsight-cluster-customize]: informazioni generali sulla personalizzazione di cluster HDInsight tramite Azione script.
* [Sviluppare script di Azione script per HDInsight](hdinsight-hadoop-script-actions.md)
* [Installare e usare Spark nei cluster HDInsight][hdinsight-install-spark]: esempio di Azione script sull'installazione di Spark
* [Installare e usare Spark nei cluster HDInsight](hdinsight-hadoop-giraph-install.md): esempio di Script azione sull'installazione di Giraph
* [Installare Solr nei cluster HDInsight](hdinsight-hadoop-solr-install-linux.md): esempio di Azione di script sull'installazione di Solr.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md

