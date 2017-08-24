---
title: Accedere ai log applicazioni YARN Hadoop a livello di codice - Azure | Microsoft Docs
description: Accedere ai log applicazioni a livello di codice su un cluster Hadoop in HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 798bd85dc136853a73e8a675f7a060a2ad2cf246
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017

---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Accesso ai log applicazioni YARN in HDInsight basato su Windows
Questo argomento illustra come accedere ai log delle applicazioni di YARN (Yet Another Resource Negotiator) completati in un cluster Hadoop basato su Windows in Azure HDInsight

> [!IMPORTANT]
> Le informazioni contenute in questo documento si applicano solo ai cluster HDInsight basati su Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Per informazioni sull'accesso ai log YARN in cluster HDInsight basati su Linux, vedere [Accedere ai log delle applicazioni YARN su Hadoop basato su Linux in HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Prerequisiti
* Un cluster HDInsight basato su Windows.  Vedere [Creare cluster Hadoop basati su Windows in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>Server di sequenza temporale YARN
Il <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Server di sequenza temporale YARN</a> fornisce informazioni generiche sulle applicazioni completate, nonché informazioni sulle applicazioni specifiche del framework, tramite due interfacce diverse. In particolare:

* L'archiviazione e il recupero di informazioni generiche sulle applicazioni nei cluster HDInsight sono stati abilitati a partire dalla versione 3.1.1.374.
* Il componente di informazioni sulle applicazioni specifiche del framework del server di sequenza temporale non è attualmente disponibile nei cluster HDInsight.

Le informazioni generiche sulle applicazioni includono i seguenti tipi di dati:

* ID applicazione, un identificatore univoco di un'applicazione
* Utente che ha avviato l'applicazione
* Informazioni sui tentativi effettuati per completare l'applicazione
* Contenitori usati da qualsiasi tentativo dell'applicazione specifico

Nei cluster HDInsight queste informazioni verranno archiviate da Gestione risorse di Azure in un archivio di cronologia nel contenitore predefinito dell'account di archiviazione di Azure predefinito. Questi dati generici sulle applicazioni completate possono essere recuperati tramite un'API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Log e applicazioni YARN
YARN supporta diversi modelli di programmazione, tra cui MapReduce, separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. A tale scopo, vengono usati un oggetto *ResourceManager* (RM) globale, oggetti *NodeManagers* (NM) per ogni nodo di lavoro e oggetti *ApplicationMasters* (AM) per ogni applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'oggetto RM opera con gli oggetti NM per concedere queste risorse come *contenitori*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori.

Ogni applicazione, inoltre, può essere costituita da più *tentativi dell'applicazione* finalizzati al completamento in presenza di arresti anomali o di perdita della comunicazione tra gli oggetti AM e RM. Di conseguenza, i contenitori vengono concessi a uno specifico tentativo di un'applicazione. In un certo senso, un contenitore fornisce il contesto per l'unità di base del lavoro eseguito da un'applicazione YARN e tutto il lavoro svolto all'interno del contesto di un contenitore viene eseguito nel singolo nodo di lavoro in cui è stato allocato il contenitore. Per altri riferimenti, vedere [Concetti YARN][YARN-concepts].

I log applicazione (e i log contenitore associati) sono essenziali per il debug di applicazioni Hadoop problematiche. YARN offre un framework utile per la raccolta, l'aggregazione e l'archiviazione dei log applicazioni tramite la funzionalità di [aggregazione dei log][log-aggregation]. La funzionalità di aggregazione dei log rende più deterministico l'accesso ai log applicazioni poiché aggrega i log di tutti i contenitori in un nodo di lavoro e li archivia sotto forma di unico file di log aggregato per ogni nodo di lavoro nel file system predefinito dopo il completamento di un'applicazione. L'applicazione può usare centinaia o migliaia di contenitori, ma i log di tutti i contenitori eseguiti su un singolo nodo di lavoro verranno sempre aggregati in un unico file, dando luogo a un solo file di log per ogni nodo di lavoro usato dall'applicazione. La funzionalità di aggregazione dei log è abilitata per impostazione predefinita nei cluster HDInsight (versione 3.0 e successive). I log aggregati si trovano nel contenitore predefinito del cluster nel percorso seguente:

    wasb:///app-logs/<user>/logs/<applicationId>

In questo percorso, *user* è il nome dell'utente che ha avviato l'applicazione e *applicationId* è l'identificatore univoco di un'applicazione assegnato dall'oggetto RM YARN.

I log aggregati non sono leggibili direttamente perché vengono scritti in un oggetto [TFile][T-file], un [formato binario][binary-format] indicizzato dal contenitore. YARN fornisce gli strumenti CLI per eseguire il dump di questi log come testo normale per le applicazioni o i contenitori di interesse. È possibile visualizzare questi log come testo normale eseguendo uno dei seguenti comandi YARN direttamente sui nodi del cluster (dopo la connessione tramite RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interfaccia utente di ResourceManager YARN
L'interfaccia utente di YARN ResourceManager viene eseguita sul nodo head del cluster ed è accessibile tramite il dashboard del portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Scegliere **Esplora** dal menu a sinistra, fare clic su **Cluster HDInsight** e quindi su un cluster basato su Windows da configurare per l'accesso ai log applicazioni YARN.
3. Nel menu in alto fare clic su **Dashboard**. Verrà aperta una pagina in una nuova scheda del browser denominata **Console query HDInsight**.
4. In **HDInsight Query Console** (Console query HDInsight) fare clic su **Yarn UI** (UI YARN).

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

