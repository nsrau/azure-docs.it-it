---
title: Architettura di Apache Hadoop - Azure HDInsight
description: Descrive le funzionalità di archiviazione ed elaborazione di Apache Hadoop nei cluster HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: edf7655c20f1b3bad5ce4d337861bd2ff3e029db
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097181"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Architettura di Apache Hadoop in Azure HDInsight

[Apache Hadoop](https://hadoop.apache.org/) include due componenti fondamentali: l'[Apache Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) che offre l'archiviazione e [Apache Hadoop Yet Another Resource Negotiatior YARN (Yet Another Resource Negotiator)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) che supporta l'elaborazione. Con le funzionalità di archiviazione ed elaborazione un cluster diventa in grado di eseguire programmi [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) per eseguire l'elaborazione dei dati desiderata.

> [!NOTE]  
> Un file system HDFS non viene in genere distribuito all'interno del cluster HDInsight per fornire l'archiviazione. I componenti di Hadoop usano invece un livello di interfaccia compatibile con HDFS. La funzionalità di archiviazione effettiva viene fornita da Archiviazione di Azure o Azure Data Lake Storage. Per Hadoop, i processi MapReduce in esecuzione nel cluster di HDInsight vengono eseguiti come se fosse presente un HDFS e pertanto non necessitano di modifiche per supportare le esigenze di archiviazione. In Hadoop in HDInsight, l'archiviazione è esternalizzata, ma l'elaborazione YARN rimane un componente di base. Per altre informazioni, vedere [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-introduction.md).

In questo articolo vengono presentati YARN e il modo in cui coordina l'esecuzione di applicazioni in HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Informazioni di base su Apache Hadoop YARN 

YARN gestisce e orchestra l'elaborazione dei dati in Hadoop. YARN include due servizi di base che vengono eseguiti come processi sui nodi nel cluster: 

* ResourceManager 
* NodeManager

Il ResourceManager concede le risorse di calcolo del cluster ad applicazioni come i processi MapReduce. Il ResourceManager concede queste risorse come contenitori, dove ogni contenitore è costituito da un'allocazione di core CPU e di memoria RAM. Se si combinano tutte le risorse disponibili in un cluster e quindi si distribuiscono i core e la memoria in blocchi, ogni blocco di risorse è un contenitore. Ogni nodo del cluster ha una capacità per un determinato numero di contenitori, pertanto il cluster ha un limite fisso per il numero di contenitori disponibili. Il numero di risorse in un contenitore è configurabile. 

Quando un'applicazione MapReduce viene eseguita in un cluster, il ResourceManager fornisce all'applicazione i contenitori per l'esecuzione. Il ResourceManager tiene traccia dello stato delle applicazioni in esecuzione, della capacità del cluster disponibile, nonché del completamento e del rilascio delle risorse delle applicazioni. 

Il ResourceManager esegue anche un processo di server Web che fornisce un'interfaccia utente Web utilizzabile per monitorare lo stato delle applicazioni.

Quando un utente invia un'applicazione MapReduce per l'esecuzione nel cluster, l'applicazione viene inviata al ResourceManager. A sua volta, il ResourceManager alloca un contenitore sui nodi NodeManager disponibili. I nodi NodeManager sono la posizione in cui viene effettivamente eseguita l'applicazione. Il primo contenitore allocato esegue un'applicazione speciale denominata ApplicationMaster. Questo ApplicationMaster è responsabile dell'acquisizione delle risorse, sotto forma di contenitori successivi, necessarie per eseguire l'applicazione inviata. L'ApplicationMaster esamina le fasi dell'applicazione, ad esempio la fase di mapping e fase di riduzione e calcola la quantità di dati che devono essere elaborati. ApplicationMaster richiede quindi (*negozia*) le risorse dal ResourceManager per conto dell'applicazione. Il ResourceManager concede a sua volta le risorse dai NodeManager nel cluster all'ApplicationMaster, in modo che possa usarle per l'esecuzione dell'applicazione. 

I NodeManager eseguono le attività che costituiscono l'applicazione, quindi segnalano l'avanzamento e lo stato all'ApplicationMaster. A sua volta, l'ApplicationMaster segnala lo stato dell'applicazione al ResourceManager. Il ResourceManager restituisce gli eventuali risultati al client.

## <a name="yarn-on-hdinsight"></a>YARN in HDInsight

Tutti i tipi di cluster HDInsight distribuiscono YARN. Il ResourceManager viene distribuito per la disponibilità elevata con un'istanza primaria e secondaria, che vengono eseguite rispettivamente nel primo e nel secondo nodo head all'interno del cluster. È attiva una sola istanza del ResourceManager alla volta. Le istanze di NodeManager vengono eseguite sui nodi di lavoro disponibili nel cluster.

![YARN in HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Passaggi successivi

* [Usare MapReduce in Apache Hadoop in HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-introduction.md)
