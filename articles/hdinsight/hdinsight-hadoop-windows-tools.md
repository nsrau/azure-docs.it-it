---
title: Usare un computer Windows con Hadoop in HDInsight - Azure | Microsoft Docs
description: Lavorare da un computer Windows in Hadoop in HDInsight. Gestire ed eseguire query sui cluster con gli strumenti di PowerShell, Visual Studio e Linux. Sviluppare soluzioni Big Data con .NET.
services: hdinsight
keywords: hadoop in windows, hadoop per windows
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: article
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e4f231c1f9b903d6cc7f2b062b30d2a072be8493
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017

---

# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Lavorare all'interno dell'ecosistema Hadoop in HDInsight da un computer Windows

Informazioni sulle opzioni di sviluppo e gestione sul computer Windows per lavorare nell'ecosistema di Hadoop in HDInsight. 

HDInsight si basa su componenti Apache Hadoop e Hadoop, tecnologie open source sviluppate in Linux. HDInsight 3.4 e versioni successive usa la distribuzione Ubuntu Linux come sistema operativo sottostante per il cluster. Tuttavia, è possibile lavorare con HDInsight da un client Windows o l'ambiente di sviluppo Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Usare PowerShell per attività di distribuzione e gestione
Azure PowerShell è un ambiente di scripting che può essere usato per controllare e automatizzare attività di distribuzione e gestione in HDInsight da Windows.

Esempi di attività che è possibile eseguire con PowerShell:

* [Creare cluster usando PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Esecuzione di query Hive usando PowerShell](hdinsight-hadoop-use-hive-powershell.md)
* [Gestire cluster con PowerShell](hdinsight-administer-use-powershell.md)

Attenersi alla procedura [installare e configurare Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) per ottenere la versione più recente. Se sono presenti script che devono essere modificati per l'uso dei nuovi cmdlet per Azure Resource Manager, vedere [Migrare a strumenti di sviluppo basati su Azure Resource Manager per i cluster HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md).

## <a name="utilities-you-can-run-in-a-browser"></a>Utilità che è possibile eseguire in un browser
Le seguenti utilità hanno un'interfaccia utente Web che viene eseguita in un browser:
* **[Azure Cloud Shell (anteprima)](https://docs.microsoft.com/azure/cloud-shell/quickstart)** è una shell interattiva della riga di comando che viene eseguita nel browser e dall'interno del portale di Azure.
* **[Interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md)** è un'utilità di gestione e monitoraggio disponibile nel portale di Azure che può essere usata per gestire diversi tipi di processi, ad esempio:
    * [Usare Ambari con l'API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [vista Hive in Ambari](hdinsight-hadoop-use-hive-ambari-view.md)
    * [Vista Tez in Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Strumenti Data Lake (Hadoop) per Visual Studio
Usare gli strumenti Data Lake per Visual Studio per distribuire e gestire topologie Storm. Gli strumenti Data Lake installano anche SCP.NET SDK, che consente di sviluppare topologie Storm C# con Visual Studio.

Prima di passare agli esempi seguenti, [installare e provare gli strumenti Data Lake per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md). 

Esempi di attività che è possibile eseguire con Visual Studio e gli strumenti Data Lake per Visual Studio:
* [Distribuire e gestire topologie Storm da Visual Studio](hdinsight-storm-deploy-monitor-topology-linux.md)
* [Sviluppare topologie C# per Storm con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md). I bit includono modelli di esempio per le topologie Storm che è possibile collegare ai database, ad esempio Azure Cosmos DB e il database SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio e .NET SDK 

È possibile usare Visual Studio con .NET SDK per gestire i cluster e sviluppare applicazioni Big Data. È possibile usare altri IDE per le attività seguenti, ma gli esempi presentati sono in Visual Studio.

Esempi di attività che è possibile eseguire con .NET SDK in Visual Studio:
* [Creare cluster e lavorare in HDInsight da un'applicazione .NET Framework](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Eseguire query Hive con .NET SDK](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [Usare funzioni C# definite dall'utente con lo streaming Hive e Pig in Hadoop](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

> SUGGERIMENTO Se si eseguono soluzioni .NET con cluster HDInsight basati su Windows, è opportuno pianificare una migrazione verso cluster basati su Linux. Per altre informazioni, vedere [Migrare una soluzione .NET per HDInsight basato su Windows a HDInsight basato su Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA e IDE di Eclipse per cluster Spark
È possibile usare sia [Intellij IDEA](https://www.jetbrains.com/idea/download) che l'[IDE di Eclipse](https://www.eclipse.org/downloads/) per:
* Sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark.
* Accedere a risorse cluster di Spark.
* Sviluppare ed eseguire un'applicazione Spark in Scala localmente.

Questi articoli mostrano come: 
* Intellij IDEA: [Creare applicazioni Spark usando il plug-in Azure Toolkit per Intellij e Scala SDK.](hdinsight-apache-spark-intellij-tool-plugin.md)
* L'IDE di Eclipse o l'IDE Scala per Eclipse: [Creare applicazioni Spark e Azure Toolkit per Eclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>Notebook su Spark per data scientist 
I cluster Apache Spark in HDInsight includono notebook e kernel Zeppelin che possono essere usati con notebook Jupyter. 

* [Informazioni su come usare i kernel nei cluster Spark con notebook Jupyter per testare le applicazioni Spark](hdinsight-apache-spark-zeppelin-notebook.md)
* [Informazioni su come usare notebook Zeppelin nei cluster Spark per eseguire processi Spark](hdinsight-apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Eseguire strumenti e tecnologie basate su Linux in Windows

Se si verifica una situazione in cui è necessario usare uno strumento o una tecnologia che è disponibile solo in Linux, considerare le opzioni seguenti:

* **Bash (beta) in Windows 10** fornisce un sottosistema Linux in Windows. Bash consente di eseguire direttamente le utilità di Linux senza dover gestire un'installazione di Linux dedicata. [Installare ed eseguire la versione beta di Bash in Windows 10](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker per Windows** fornisce l'accesso a molti strumenti basati su Linux e può essere eseguito direttamente da Windows. Ad esempio è possibile usare Docker per eseguire il client Beeline per Hive direttamente da Windows. È possibile inoltre usare Docker per eseguire un notebook Jupyter locale e connettersi da remoto a Spark in HDInsight. [Introduzione a Docker per Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)**  consente di visualizzare graficamente il file system del cluster tramite una connessione SSH.

## <a name="next-steps"></a>Passaggi successivi
Se non si ha familiarità con l'uso dei cluster basati su Linux, vedere gli articoli seguenti:
* [Configurare Hadoop, Kafka, Spark o altri cluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Suggerimenti per i cluster HDInsight in Linux](hdinsight-hadoop-linux-information.md)
