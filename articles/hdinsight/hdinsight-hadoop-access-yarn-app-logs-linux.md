---
title: Accesso ai log delle applicazioni Apache Hadoop YARN - Azure HDInsightAccess Apache Hadoop YARN application logs - Azure HDInsight
description: Informazioni su come accedere ai log dell'applicazione YARN in un cluster HDInsight (Apache Hadoop) basato su Linux tramite la riga di comando e un Web browser.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764388"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Accedere ai log dell'applicazione YARN di Apache Hadoop in HDInsight basato su Linux

Informazioni su come accedere ai log di applicazioni [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Yet Another Resource Negotiator) di Apache in un cluster [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight.

## <a name="what-is-apache-yarn"></a>Che cos'è Apache YARN?

YARN supporta diversi modelli di programmazione, tra cui [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html), separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. YARN usa un oggetto *ResourceManager* (RM) globale, oggetti *NodeManagers* (NM) per ogni nodo di lavoro e oggetti *ApplicationMasters* (AM) per ogni applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'oggetto RM opera con gli oggetti NM per concedere queste risorse come *contenitori*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori.

Ogni applicazione può essere costituita da più *tentativi dell'applicazione*. Se si verifica un errore in un'applicazione, è possibile ripetere un nuovo tentativo. Ogni tentativo viene eseguito in un contenitore. In un certo senso, un contenitore offre il contesto per le unità di base del lavoro eseguito da un'applicazione YARN. Tutto il lavoro fatto all'interno del contesto di un contenitore viene eseguito nel singolo nodo di lavoro in cui è stato allocato il contenitore. Vedere [Hadoop: Writing YARN Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)o [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) per ulteriori riferimenti.

Per ridimensionare il cluster in modo da supportare una maggiore velocità effettiva di elaborazione, è possibile usare la [scalabilità automatica](hdinsight-autoscale-clusters.md) o [la scalabilità manuale dei cluster usando alcune lingue diverse.](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters)

## <a name="yarn-timeline-server"></a>Server di sequenza temporale YARN

Il [server di sequenza temporale YARN di Apache Hadoop](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) offre informazioni generiche sulle applicazioni completate

Il server di sequenza temporale YARN include i tipi di dati seguenti:

* ID applicazione, un identificatore univoco di un'applicazione
* Utente che ha avviato l'applicazione
* Informazioni sui tentativi effettuati per completare l'applicazione
* Contenitori usati da qualsiasi tentativo dell'applicazione specifico

## <a name="yarn-applications-and-logs"></a>Log e applicazioni YARN

YARN supporta diversi modelli di programmazione, tra cui [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html), separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. YARN usa un oggetto *ResourceManager* (RM) globale, oggetti *NodeManagers* (NM) per ogni nodo di lavoro e oggetti *ApplicationMasters* (AM) per ogni applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'oggetto RM opera con gli oggetti NM per concedere queste risorse come *contenitori*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori.

Ogni applicazione può essere costituita da più *tentativi dell'applicazione*. Se si verifica un errore in un'applicazione, è possibile ripetere un nuovo tentativo. Ogni tentativo viene eseguito in un contenitore. In un certo senso, un contenitore offre il contesto per le unità di base del lavoro eseguito da un'applicazione YARN. Tutto il lavoro fatto all'interno del contesto di un contenitore viene eseguito nel singolo nodo di lavoro in cui è stato allocato il contenitore. Per altri riferimenti, vedere [Concetti di Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html).

I log applicazione (e i log contenitore associati) sono essenziali per il debug di applicazioni Hadoop problematiche. YARN offre un framework utile per la raccolta, l'aggregazione e l'archiviazione dei log applicazioni tramite la funzionalità di [aggregazione dei log](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/). La funzione di aggregazione dei log rende più deterministico l'accesso ai log dell'applicazione. Aggrega i log di tutti i contenitori in un nodo di lavoro e li archivia come file di log aggregati per ogni nodo di lavoro. Quando un'applicazione termina, il log viene archiviato nel file system predefinito. L'applicazione può usare centinaia o migliaia di contenitori, ma i log di tutti i contenitori eseguiti su un singolo nodo di lavoro vengono sempre aggregati in un unico file. Quindi c'è solo 1 log per nodo di lavoro utilizzato dall'applicazione. La funzione di aggregazione dei log è abilitata per impostazione predefinita nei cluster HDInsight versione 3.0 o successiva. I log aggregati sono disponibili nella risorsa di archiviazione predefinita per il cluster. Il percorso seguente è il percorso HDFS dei log:

```
/app-logs/<user>/logs/<applicationId>
```

In questo percorso, `user` è il nome dell'utente che ha avviato l'applicazione e `applicationId` è l'identificatore univoco assegnato a un'applicazione dall'oggetto YARN RM.

I log aggregati non sono direttamente leggibili, poiché sono scritti in un [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [formato binario](https://issues.apache.org/jira/browse/HADOOP-3315) indicizzato dal contenitore. Usare i log di YARN ResourceManager o gli strumenti dell'interfaccia della riga di comando per visualizzare i log come testo normale per le applicazioni o i contenitori di interesse.

## <a name="yarn-logs-in-an-esp-cluster"></a>Registri di filati in un cluster ESP

Due configurazioni devono essere `mapred-site` aggiunte all'usanza in Ambari.

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net`passare `CLUSTERNAME` a , dove è il nome del cluster.

1. Dall'interfaccia utente di Ambari, accedere a **MapReduce2** > **Configs** > **Advanced** > Custom**mapred-site**.

1. Aggiungere *uno* dei seguenti set di proprietà:

    **Impostazione 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Set 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Salvare le modifiche e riavviare tutti i servizi interessati.

## <a name="yarn-cli-tools"></a>Strumenti dell’interfaccia di riga di comando YARN

1. Usare [il comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster, quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Elencare tutti gli ID applicazione delle applicazioni Yarn attualmente in esecuzione con il seguente comando:

    ```bash
    yarn top
    ```

    Si noti l'ID dell'applicazione dalla `APPLICATIONID` colonna i cui log devono essere scaricati.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. È possibile visualizzare questi log come testo normale eseguendo uno dei seguenti comandi:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Quando si eseguono questi comandi, specificare le informazioni relative a &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> e &lt;worker-node-address>.

### <a name="other-sample-commands"></a>Altri comandi di esempio

1. Scaricare i registri dei contenitori Yarn per tutti i master dell'applicazione con il comando seguente. Verrà creato il file di log denominato `amlogs.txt` nel formato di testo.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Scaricare i log dei contenitori YARN solo per gli schemi dell'applicazione più recenti con il comando seguente:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Scaricare i log dei contenitori YARN per i primi due schemi dell'applicazione con il comando seguente:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Scaricare tutti i log dei contenitori di YARN con il comando seguente:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Scaricare i log dei contenitori di YARN per un determinato contenitore con il comando seguente:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>Interfaccia utente di ResourceManager YARN

L'interfaccia utente di YARN ResourceManager viene eseguita sul nodo head del cluster È accessibile tramite l'interfaccia utente Web di Ambari. Per visualizzare i log di YARN, procedere come segue:

1. Nel Web browser passare a `https://CLUSTERNAME.azurehdinsight.net`. Sostituire CLUSTERNAME con il nome del cluster HDInsight.

2. Nell'elenco dei servizi a sinistra selezionare **YARN**.

    ![Servizio Filati Apache Ambari selezionato](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Dall’elenco a discesa **Collegamenti rapidi** selezionare uno dei nodi head del cluster e quindi **Log ResourceManager**.

    ![Collegamenti rapidi Apache Ambari Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Viene visualizzato un elenco di collegamenti ai registri YARN.
