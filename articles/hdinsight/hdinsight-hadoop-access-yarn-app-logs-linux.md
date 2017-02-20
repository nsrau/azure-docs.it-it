---
title: Accedere ai log delle applicazioni YARN in HDInsight basato su Linux | Documentazione Microsoft
description: "Informazioni su come accedere ai log dell’applicazione YARN in un cluster HDInsight (Hadoop) basato su Linux tramite la riga di comando e un browser web."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: dd5471da4d1e69b51d355784dfa2551bc61e9ad9
ms.openlocfilehash: 508ea94278dc2410e5b9ea1ba760a8a923f12bbd


---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Accedere ai log delle applicazioni YARN in HDInsight basato su Linux
In questo documento viene illustrato come accedere ai log delle applicazioni YARN (Yet Another Resource Negotiator) che sono finite in un cluster Hadoop in Azure HDInsight.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti
* Un cluster HDInsight basato su Linux.
* È necessario [creare un tunnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) prima di poter accedere all'interfaccia utente Web dei log di ResourceManager.

## <a name="a-nameyarntimelineserverayarn-timeline-server"></a><a name="YARNTimelineServer"></a>Server di sequenza temporale YARN
Il [server di sequenza temporale YARN](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) offre informazioni generiche sulle applicazioni completate e informazioni sulle applicazioni specifiche del framework, tramite due interfacce diverse. In particolare:

* L'archiviazione e il recupero di informazioni generiche sulle applicazioni nei cluster HDInsight sono stati abilitati a partire dalla versione 3.1.1.374.
* Il componente di informazioni sulle applicazioni specifiche del framework del server di sequenza temporale non è attualmente disponibile nei cluster HDInsight.

Le informazioni generiche sulle applicazioni includono i seguenti tipi di dati:

* ID applicazione, un identificatore univoco di un'applicazione
* Utente che ha avviato l'applicazione
* Informazioni sui tentativi effettuati per completare l'applicazione
* Contenitori usati da qualsiasi tentativo dell'applicazione specifico

## <a name="a-nameyarnappsandlogsayarn-applications-and-logs"></a><a name="YARNAppsAndLogs"></a>Log e applicazioni YARN

YARN supporta diversi modelli di programmazione, tra cui MapReduce, separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. A tale scopo, vengono usati un oggetto *ResourceManager* (RM) globale, oggetti *NodeManagers* (NM) per ogni nodo di lavoro e oggetti *ApplicationMasters* (AM) per ogni applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'oggetto RM opera con gli oggetti NM per concedere queste risorse come *contenitori*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori.

Ogni applicazione può essere costituita da più *tentativi dell'applicazione*. In questo modo un'applicazione ritenta un'operazione quando si verifica un arresto anomalo o si perde la comunicazione tra un AM e un RM. Ogni tentativo viene eseguito in un contenitore. In un certo senso, un contenitore offre il contesto per le unità di base del lavoro eseguito da un'applicazione YARN. Tutto il lavoro fatto all'interno del contesto di un contenitore viene eseguito nel singolo nodo di lavoro in cui è stato allocato il contenitore. Per altri riferimenti, vedere [Concetti YARN][YARN-concepts].

I log applicazione (e i log contenitore associati) sono essenziali per il debug di applicazioni Hadoop problematiche. YARN offre un framework utile per la raccolta, l'aggregazione e l'archiviazione dei log applicazioni tramite la funzionalità di [aggregazione dei log][log-aggregation]. La funzione di aggregazione dei log rende più deterministico l'accesso ai log dell'applicazione. Aggrega i log di tutti i contenitori in un nodo di lavoro e li archivia come file di log aggregati per ogni nodo di lavoro. Quando un'applicazione termina, il log viene archiviato nel file system predefinito. L'applicazione può usare centinaia o migliaia di contenitori, ma i log di tutti i contenitori eseguiti su un singolo nodo di lavoro verranno sempre aggregati in un unico file. Di conseguenza viene creato a un solo file di log per ogni nodo di lavoro usato dall'applicazione. La funzionalità di aggregazione dei log è abilitata per impostazione predefinita nei cluster HDInsight (versione 3.0 e successive). I log aggregati si trovano nel contenitore predefinito del cluster nel percorso seguente:

    wasbs:///app-logs/<user>/logs/<applicationId>

In questo percorso, *user* è il nome dell'utente che ha avviato l'applicazione e *applicationId* è l'identificatore univoco di un'applicazione assegnato dall'oggetto RM YARN.

I log aggregati non sono leggibili direttamente perché vengono scritti in un oggetto [TFile][T-file], un [formato binario][binary-format] indicizzato dal contenitore. È necessario utilizzare i log del ResourceManager YARN o gli strumenti dell’interfaccia di riga di comando per visualizzare questi log come testo normale per le applicazioni o i contenitori di interesse. 

## <a name="yarn-cli-tools"></a>Strumenti dell’interfaccia di riga di comando YARN

Per utilizzare gli strumenti dell'interfaccia della riga di comando YARN, è innanzitutto necessario connettersi al cluster HDInsight tramite SSH. Per informazioni sull'uso di SSH con HDInsight, vedere uno dei documenti seguenti:

* [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

È possibile visualizzare questi log come testo normale eseguendo uno dei seguenti comandi:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

È necessario specificare le informazioni di &lt;applicationId>, &lt;utente-che-ha-avviato-l’applicazione>, &lt;containerId> e &ltindirizzo-nodo-lavoro> quando si eseguono questi comandi.

## <a name="yarn-resourcemanager-ui"></a>Interfaccia utente di ResourceManager YARN
L'interfaccia utente di ResourceManager YARN viene eseguita sul nodo head del cluster ed è accessibile tramite l’interfaccia utente Web Ambari; tuttavia, è innanzitutto necessario [creare un tunnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) prima di poter accedere all’interfaccia utente di ResourceManager.

Dopo aver creato un tunnel SSH, utilizzare la procedura seguente per visualizzare i log YARN:

1. Nel browser Web, accedere a https://CLUSTERNAME.azurehdinsight.net. Sostituire CLUSTERNAME con il nome del cluster HDInsight.
2. Nell'elenco dei servizi a sinistra, selezionare **YARN**.
   
    ![Servizio Yarn selezionato](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Dall’elenco a discesa **Collegamenti rapidi** selezionare uno dei nodi head del cluster e quindi **Log ResourceManager**.
   
    ![Collegamenti rapidi Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
   
    Viene visualizzato un elenco di collegamenti ai log YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/



<!--HONumber=Feb17_HO1-->


