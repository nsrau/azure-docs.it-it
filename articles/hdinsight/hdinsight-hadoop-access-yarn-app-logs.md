---
title: Accedere ai log applicazioni YARN Hadoop a livello di codice - Azure
description: Accedere ai log applicazioni a livello di codice su un cluster Hadoop in HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: da105be19f7d546e530298f87974fe7f3f78989f
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012216"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-windows-based-hdinsight"></a>Accedere ai log applicazioni YARN di Apache Hadoop su HDInsight basato su Windows
Questo documento illustra come accedere ai log delle applicazioni [YARN di Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) che sono finite in un cluster Apache Hadoop basato su Windows in Azure HDInsight

> [!IMPORTANT]
> Le informazioni contenute in questo documento si applicano solo ai cluster HDInsight basati su Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Per informazioni sull'accesso ai log YARN nei cluster HDInsight basati su Linux, vedere [Accedere ai log applicazioni YARN Apache Hadoop basati su Linux in HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Prerequisiti
* Un cluster HDInsight basato su Windows.  Vedere [Creare cluster Apache Hadoop basati su Windows in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>Server di sequenza temporale YARN
Il <a href="http://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Server di sequenza temporale YARN Apache Hadoop</a> fornisce informazioni generiche sulle applicazioni completate, nonché informazioni sulle applicazioni specifiche del framework, tramite due interfacce diverse. In particolare:

* L'archiviazione e il recupero di informazioni generiche sulle applicazioni nei cluster HDInsight sono stati abilitati a partire dalla versione 3.1.1.374.
* Il componente di informazioni sulle applicazioni specifiche del framework del server di sequenza temporale non è attualmente disponibile nei cluster HDInsight.

Le informazioni generiche sulle applicazioni includono i seguenti tipi di dati:

* ID applicazione, un identificatore univoco di un'applicazione
* Utente che ha avviato l'applicazione
* Informazioni sui tentativi effettuati per completare l'applicazione
* Contenitori usati da qualsiasi tentativo dell'applicazione specifico

Nei cluster HDInsight, queste informazioni vengono archiviate da Azure Resource Manager. Le informazioni vengono salvate in un archivio di cronologia nella risorsa di archiviazione predefinita per il cluster. Questi dati generici sulle applicazioni completate possono essere recuperati tramite un'API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Log e applicazioni YARN
YARN supporta diversi modelli di programmazione, separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. YARN usa un oggetto *ResourceManager* (RM) globale, oggetti *NodeManagers* (NM) per ogni nodo di lavoro e oggetti *ApplicationMasters* (AM) per ogni applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'oggetto RM opera con gli oggetti NM per concedere queste risorse come *contenitori*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori.

* Ogni applicazione può essere costituita da più *tentativi dell'applicazione*. 
* I contenitori vengono concessi a uno specifico tentativo di un'applicazione. 
* Un contenitore fornisce il contesto per un'unità di lavoro di base. 
* Il lavoro svolto all'interno del contesto di un contenitore viene eseguito nel singolo nodo del ruolo di lavoro a cui è stato allocato il contenitore. 

Per altre informazioni, vedere [Concetti YARN Apache Hadoop][YARN-concepts].

I log applicazione (e i log contenitore associati) sono essenziali per il debug di applicazioni Hadoop problematiche. YARN offre un framework utile per la raccolta, l'aggregazione e l'archiviazione dei log applicazioni tramite la funzionalità di [aggregazione dei log][log-aggregation]. La funzionalità di aggregazione dei log rende più deterministico l'accesso ai log applicazioni poiché aggrega i log di tutti i contenitori in un nodo di lavoro e li archivia sotto forma di unico file di log aggregato per ogni nodo di lavoro nel file system predefinito dopo il completamento di un'applicazione. L'applicazione può usare centinaia o migliaia di contenitori, ma i log di tutti i contenitori eseguiti su un singolo nodo di lavoro vengono aggregati in un unico file, dando luogo a un solo file per ogni nodo del ruolo di lavoro usato dall'applicazione. La funzionalità di aggregazione dei log è abilitata per impostazione predefinita nei cluster HDInsight (versione 3.0 e successive). I log aggregati si trovano nel contenitore predefinito del cluster nel percorso seguente:

    wasb:///app-logs/<user>/logs/<applicationId>

In questo percorso, *user* è il nome dell'utente che ha avviato l'applicazione e *applicationId* è l'identificatore univoco di un'applicazione assegnato dall'oggetto RM YARN.

I log aggregati non sono leggibili direttamente perché vengono scritti in un oggetto [TFile][T-file], un [formato binario][binary-format] indicizzato dal contenitore. YARN fornisce gli strumenti CLI per eseguire il dump di questi log come testo normale per le applicazioni o i contenitori di interesse. È possibile visualizzare questi log come testo normale eseguendo uno dei seguenti comandi YARN direttamente sui nodi del cluster (dopo la connessione tramite RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interfaccia utente di ResourceManager YARN
L'interfaccia utente di YARN ResourceManager viene eseguita sul nodo head del cluster ed è accessibile tramite il dashboard del portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere **Esplora** dal menu a sinistra, fare clic su **Cluster HDInsight** e quindi su un cluster basato su Windows da configurare per l'accesso ai log applicazioni YARN.
3. Nel menu in alto fare clic su **Dashboard**. In una nuova scheda del browser viene aperta una pagina denominata **Console query HDInsight**.
4. In **HDInsight Query Console** (Console query HDInsight) fare clic su **Yarn UI** (UI YARN).

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
