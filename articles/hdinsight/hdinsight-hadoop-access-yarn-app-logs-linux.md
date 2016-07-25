<properties
	pageTitle="Accedere ai log dell’applicazione YARN Hadoop in HDInsight basato su Linux | Microsoft Azure"
	description="Informazioni su come accedere ai log dell’applicazione YARN in un cluster HDInsight (Hadoop) basato su Linux tramite la riga di comando e un browser web."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="Blackmist" 
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>

# Accedere ai log delle applicazioni YARN in HDInsight basato su Linux 

In questo documento viene illustrato come accedere ai log delle applicazioni YARN (Yet Another Resource Negotiator) che sono finite in un cluster Hadoop in Azure HDInsight.

> [AZURE.NOTE] Le informazioni contenute in questo documento sono specifiche per i cluster HDInsight basati su Linux. Per informazioni sui cluster basati su Windows, vedere [Accedere ai log delle applicazioni YARN in HDInsight basato su Linux](hdinsight-hadoop-access-yarn-app-logs.md)

## Prerequisiti

* Un cluster HDInsight basato su Linux.

* È necessario [creare un tunnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) prima di poter accedere all'interfaccia utente Web dei log di ResourceManager.

## <a name="YARNTimelineServer"></a>Server di sequenza temporale YARN

Il [server di sequenza temporale YARN](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornisce informazioni generiche sulle applicazioni completate, nonché informazioni sulle applicazioni specifiche del framework, tramite due interfacce diverse. In particolare:

* L'archiviazione e il recupero di informazioni generiche sulle applicazioni nei cluster HDInsight sono stati abilitati a partire dalla versione 3.1.1.374.
* Il componente di informazioni sulle applicazioni specifiche del framework del server di sequenza temporale non è attualmente disponibile nei cluster HDInsight.

Le informazioni generiche sulle applicazioni includono i seguenti tipi di dati:

* ID applicazione, un identificatore univoco di un'applicazione
* Utente che ha avviato l'applicazione
* Informazioni sui tentativi effettuati per completare l'applicazione
* Contenitori usati da qualsiasi tentativo dell'applicazione specifico

## <a name="YARNAppsAndLogs"></a>Log e applicazioni YARN

YARN supporta diversi modelli di programmazione, tra cui MapReduce, separando la gestione delle risorse dalla pianificazione e dal monitoraggio dell'applicazione. A tale scopo, vengono usati un oggetto *ResourceManager* (RM) globale, oggetti *NodeManagers* (NM) per ogni nodo di lavoro e oggetti *ApplicationMasters* (AM) per ogni applicazione. L'AM per applicazione negozia le risorse (CPU, memoria, disco e rete) per l'esecuzione dell'applicazione con l'RM. L'oggetto RM opera con gli oggetti NM per concedere queste risorse come *contenitori*. L'AM è responsabile del monitoraggio dello stato dei contenitori assegnati dall'RM. A seconda del tipo, un'applicazione può richiedere più contenitori.

Ogni applicazione, inoltre, può essere costituita da più *tentativi dell'applicazione* finalizzati al completamento in presenza di arresti anomali o di perdita della comunicazione tra gli oggetti AM e RM. Di conseguenza, i contenitori vengono concessi a uno specifico tentativo di un'applicazione. In un certo senso, un contenitore fornisce il contesto per l'unità di base del lavoro eseguito da un'applicazione YARN e tutto il lavoro svolto all'interno del contesto di un contenitore viene eseguito nel singolo nodo di lavoro in cui è stato allocato il contenitore. Per altri riferimenti, vedere [Concetti YARN][YARN-concepts].

I log applicazione (e i log contenitore associati) sono essenziali per il debug di applicazioni Hadoop problematiche. YARN fornisce un framework utile per la raccolta, l'aggregazione e l'archiviazione dei log applicazioni mediante la funzionalità di [aggregazione dei log][log-aggregation]. La funzionalità di aggregazione dei log rende più deterministico l'accesso ai log applicazioni poiché aggrega i log di tutti i contenitori in un nodo di lavoro e li archivia sotto forma di unico file di log aggregato per ogni nodo di lavoro nel file system predefinito dopo il completamento di un'applicazione. L'applicazione può usare centinaia o migliaia di contenitori, ma i log di tutti i contenitori eseguiti su un singolo nodo di lavoro verranno sempre aggregati in un unico file, dando luogo a un solo file di log per ogni nodo di lavoro usato dall'applicazione. La funzionalità di aggregazione dei log è abilitata per impostazione predefinita nei cluster HDInsight (versione 3.0 e successive). I log aggregati si trovano nel contenitore predefinito del cluster nel percorso seguente:

	wasb:///app-logs/<user>/logs/<applicationId>

In questo percorso, *user* è il nome dell'utente che ha avviato l'applicazione e *applicationId* è l'identificatore univoco di un'applicazione assegnato dall'oggetto RM YARN.

I log aggregati non sono leggibili direttamente, poiché vengono scritti in un oggetto [TFile][T-file], un [formato binario][binary-format] indicizzato dal contenitore. È necessario utilizzare i log del ResourceManager YARN o gli strumenti dell’interfaccia di riga di comando per visualizzare questi log come testo normale per le applicazioni o i contenitori di interesse.

##Strumenti dell’interfaccia di riga di comando YARN

Per utilizzare gli strumenti CLI YARN, è innanzitutto necessario connettersi al cluster HDInsight tramite SSH. Per informazioni sull'uso di SSH con HDInsight, vedere uno dei documenti seguenti:

- [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

- [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
	
È possibile visualizzare questi log come testo normale eseguendo uno dei seguenti comandi:

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
	
È necessario specificare le informazioni di &lt;applicationId>, &lt;utente-che-ha-avviato-l’applicazione>, &lt;containerId>, e &ltindirizzo-nodo-lavoro> quando si eseguono questi comandi.

##Interfaccia utente di ResourceManager YARN

L'interfaccia utente di ResourceManager YARN viene eseguita sul nodo head del cluster ed è accessibile tramite l’interfaccia utente Web Ambari; tuttavia, è innanzitutto necessario [creare un tunnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) prima di poter accedere all’interfaccia utente di ResourceManager.

Dopo aver creato un tunnel SSH, utilizzare la procedura seguente per visualizzare i log YARN:

1. Nel browser passare a https://CLUSTERNAME.azurehdinsight.net. Sostituire CLUSTERNAME con il nome del cluster HDInsight.

2. Nell'elenco dei servizi a sinistra, selezionare __YARN__.

	![Servizio Yarn selezionato](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)

3. Dall’elenco a discesa __Collegamenti rapidi__ selezionare uno dei nodi head del cluster e quindi __Log ResourceManager__.

	![Collegamenti rapidi Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
	
	Verrà visualizzato un elenco di collegamenti ai log YARN.

[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!---HONumber=AcomDC_0713_2016-->