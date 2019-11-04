---
title: Accedere ai log applicazioni di Apache Hadoop YARN-Azure HDInsight
description: Informazioni su come accedere ai log dell'applicazione YARN in un cluster HDInsight (Apache Hadoop) basato su Linux tramite la riga di comando e un Web browser.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: 263456769ab391cbc0588eed1a714a1ea5788154
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494885"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Accedere ai log dell'applicazione YARN di Apache Hadoop in HDInsight basato su Linux

Informazioni su come accedere ai log di applicazioni [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) di Apache in un cluster [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight.

## <a name="YARNTimelineServer"></a>Server di sequenza temporale YARN

Il [server di sequenza temporale YARN di Apache Hadoop](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) offre informazioni generiche sulle applicazioni completate

Il server di sequenza temporale YARN include i tipi di dati seguenti:

* ID applicazione, un identificatore univoco di un'applicazione
* Utente che ha avviato l'applicazione
* Informazioni sui tentativi effettuati per completare l'applicazione
* Contenitori usati da qualsiasi tentativo dell'applicazione specifico

## <a name="YARNAppsAndLogs"></a>Log e applicazioni YARN

YARN supporta diversi modelli di programmazione, tra cui [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html), separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. YARN usa un oggetto *ResourceManager* (RM) globale, oggetti *NodeManagers* (NM) per ogni nodo di lavoro e oggetti *ApplicationMasters* (AM) per ogni applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'oggetto RM opera con gli oggetti NM per concedere queste risorse come *contenitori*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori.

Ogni applicazione può essere costituita da più *tentativi dell'applicazione*. Se si verifica un errore in un'applicazione, è possibile ripetere un nuovo tentativo. Ogni tentativo viene eseguito in un contenitore. In un certo senso, un contenitore offre il contesto per le unità di base del lavoro eseguito da un'applicazione YARN. Tutto il lavoro fatto all'interno del contesto di un contenitore viene eseguito nel singolo nodo di lavoro in cui è stato allocato il contenitore. Per altri riferimenti, vedere [concetti relativi a Apache Hadoop Yarn](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) .

I log applicazione (e i log contenitore associati) sono essenziali per il debug di applicazioni Hadoop problematiche. YARN fornisce un Framework interessante per la raccolta, l'aggregazione e l'archiviazione dei log applicazioni con la funzionalità di [aggregazione dei log](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . La funzione di aggregazione dei log rende più deterministico l'accesso ai log dell'applicazione. Aggrega i log di tutti i contenitori in un nodo di lavoro e li archivia come file di log aggregati per ogni nodo di lavoro. Quando un'applicazione termina, il log viene archiviato nel file system predefinito. L'applicazione può usare centinaia o migliaia di contenitori, ma i log di tutti i contenitori eseguiti su un singolo nodo di lavoro vengono sempre aggregati in un unico file. Pertanto, sarà sempre disponibile un unico log per ogni nodo di lavoro usato dall'applicazione. La funzione di aggregazione dei log è abilitata per impostazione predefinita nei cluster HDInsight versione 3.0 o successiva. I log aggregati sono disponibili nella risorsa di archiviazione predefinita per il cluster. Il percorso seguente è il percorso HDFS dei log:

    /app-logs/<user>/logs/<applicationId>

In questo percorso, `user` è il nome dell'utente che ha avviato l'applicazione e `applicationId` è l'identificatore univoco assegnato a un'applicazione dall'oggetto YARN RM.

I log aggregati non sono leggibili direttamente, perché sono scritti in un [oggetto tfile][T-file], [formato binario][binary-format] indicizzato dal contenitore. Usare i log di YARN ResourceManager o gli strumenti dell'interfaccia della riga di comando per visualizzare i log come testo normale per le applicazioni o i contenitori di interesse.

## <a name="yarn-cli-tools"></a>Strumenti dell’interfaccia di riga di comando YARN

Per utilizzare gli strumenti dell'interfaccia della riga di comando YARN, è innanzitutto necessario connettersi al cluster HDInsight tramite SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

È possibile visualizzare questi log come testo normale eseguendo uno dei seguenti comandi:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Quando si eseguono questi comandi, specificare le informazioni relative a &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> e &lt;worker-node-address>.

## <a name="yarn-resourcemanager-ui"></a>Interfaccia utente di ResourceManager YARN

L'interfaccia utente di YARN ResourceManager viene eseguita sul nodo head del cluster ed è accessibile tramite l'interfaccia utente Web di Ambari. Per visualizzare i log di YARN, procedere come segue:

1. Nel Web browser passare a https://CLUSTERNAME.azurehdinsight.net. Sostituire CLUSTERNAME con il nome del cluster HDInsight.
2. Nell'elenco dei servizi a sinistra, selezionare **YARN**.

    ![Servizio Yarn Apache Ambari selezionato](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Dall’elenco a discesa **Collegamenti rapidi** selezionare uno dei nodi head del cluster e quindi **Log ResourceManager**.

    ![Collegamenti rapidi di Apache Ambari Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Viene visualizzato un elenco di collegamenti ai log YARN.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
