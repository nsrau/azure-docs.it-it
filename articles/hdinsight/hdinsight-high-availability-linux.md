<properties
	pageTitle="Funzionalità a disponibilità elevata di HDInsight (Hadoop) basati su Linux | Microsoft Azure"
	description="Informazioni su come i cluster HDInsight basati su Linux migliorano l'affidabilità e la disponibilità tramite l’utilizzo di un nodo head aggiuntivo. Informazioni su come questo influisce sui servizi di Hadoop come Ambari e Hive, e anche su come connettersi singolarmente a ogni nodo head utilizzando SSH."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/18/2016"
	ms.author="larryfr"/>

#Disponibilità e affidabilità dei cluster Hadoop in HDInsight

Un secondo nodo head viene utilizzato dai cluster Hadoop basati su Linux distribuiti da Azure HDInsight. In questo modo aumenta la disponibilità e l’affidabilità dei servizi e dei processi Hadoop in esecuzione in Azure.

> [AZURE.NOTE] I passaggi descritti in questo documento sono specifici per i cluster HDInsight basati su Linux. Se si utilizza un cluster basato su Windows, vedere [Disponibilità e affidabilità dei cluster Hadoop basati su Windows in HDInsight](hdinsight-high-availability.md) per informazioni specifiche su Windows.

##Informazioni sui nodi head

Alcune implementazioni di Hadoop hanno un solo nodo head che ospita i servizi e i componenti in grado di gestire gli errori dei nodi dati (ruolo di lavoro) senza problemi. Tuttavia, eventuali interruzioni dei servizi master in esecuzione nel nodo head causerebbero l’interruzione del funzionamento del cluster.

I cluster HDInsight forniscono un nodo head secondario, che consente ai servizi master e ai componenti di continuare a funzionare nel nodo secondario in caso di errore nel server primario.

> [AZURE.IMPORTANT] Entrambi i nodi head sono contemporaneamente attivi e in esecuzione all'interno del cluster. Alcuni servizi, ad esempio HDFS o YARN, sono 'attivi' soltanto in un nodo head (e in ‘standby’ nell'altro nodo head). Altri servizi come HiveServer2 o Hive Metastore sono attivi su entrambi i nodi head allo tesso tempo.

I nodi [ZooKeeper](http://zookeeper.apache.org/) (ZKs) vengono utilizzati per l’elezione del leader dei servizi master nei nodi head e per garantire che i servizi, i nodi di dati (ruolo di lavoro) e i gateway sappiano su quale nodo head è attivo un servizio master.

## Accesso ai nodi head

In generale, tutti gli accessi al cluster tramite i gateway pubblici (la web di Ambari e le API REST,) non vengono effettuati con più nodi head. La richiesta è indirizzata al nodo head attivo e gestita nel modo appropriato.

Se l'accesso al cluster avviene tramite SSH e la connessione avviene tramite la porta 22 (impostazione predefinita per SSH,) si stabilirà una connessione con il nodo head 0; se la connessione avviene tramite la porta 23 si stabilirà, invece, una connessione con il nodo head 1.

### Nomi di dominio completo interni (FQDN)

I nodi in un cluster HDInsight dispongono di un indirizzo IP interno e di FQDN accessibili solo dal cluster (ad esempio, una sessione SSH per il nodo head o un processo in esecuzione nel cluster.) Quando si accede ai servizi del cluster utilizzando l'indirizzo IP o i FQDN interni, è necessario utilizzare Ambari per verificare l'IP o i FQDN da utilizzare per l’accesso al servizio.

Ad esempio, il servizio Oozie può essere eseguito solo su un nodo head e l’utilizzo del comando `oozie` da una sessione SSH richiede l'URL del servizio. Esso può essere recuperato da Ambari utilizzando il comando seguente:

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Verrà restituito un valore simile al seguente, che contiene l'URL interno da utilizzare con il comando `oozie`:

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

## Come controllare lo stato di un servizio

L'interfaccia utente Web Ambari o l'API REST Ambari possono essere utilizzati per controllare lo stato dei servizi in esecuzione nel nodo head.

###API REST Ambari

È possibile utilizzare il comando seguente per verificare lo stato di un servizio tramite l'API REST di Ambari:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Sostituire **PASSWORD** con la password dell’account dell'utente HTTP (admin)

* Sostituire **CLUSTERNAME** con il nome del cluster

* Sostituire **SERVICENAME** con il nome del servizio per controllare lo stato di

Ad esempio, per controllare lo stato del servizio **HDFS** in un cluster denominato **mycluster**, con la **password**, è necessario utilizzare quanto segue:

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

La risposta sarà simile a quanto riportato di seguito:

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

L'URL indica che il servizio è in esecuzione su **nodo head 0**.

Lo stato indica che il servizio è in esecuzione, o **AVVIATO**.

Se non si sa quali servizi sono installati nel cluster, è possibile utilizzare quanto segue per recuperarne un elenco:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Componenti del servizio

I servizi possono includere componenti di cui si desidera controllare lo stato singolarmente. HDFS, ad esempio, contiene il componente NameNode. Per visualizzare informazioni su un componente, il comando è:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Se non si sa quali servizi sono forniti dal cluster, è possibile utilizzare quanto segue per recuperarne un elenco:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

###Interfaccia utente Web Ambari

L'interfaccia utente Web Ambari è visualizzabile in https://CLUSTERNAME.azurehdinsight.net. Sostituire **CLUSTERNAME** con il nome del cluster. Se richiesto, immettere le credenziali dell’utente HTTP del cluster. Il nome utente HTTP predefinito è **admin** e la password è la password inserita durante la creazione del cluster.

Nella pagina Ambari, i servizi installati verranno elencati a sinistra.

![Servizi installati](./media/hdinsight-high-availability-linux/services.png)

Esistono molte icone che possono essere visualizzate accanto a un servizio per indicare lo stato. È possibile visualizzare eventuali avvisi relativi a un servizio utilizzando il collegamento **Avvisi** nella parte superiore della pagina. È possibile selezionare ogni servizio per visualizzare ulteriori informazioni su di esso.

La pagina del servizio fornisce informazioni sullo stato e sulla configurazione di ogni servizio, ma non indica su quale nodo head esso è in esecuzione. Per visualizzare questa informazione, utilizzare il collegamento**Host** nella parte superiore della pagina. Verranno visualizzati gli host del cluster, inclusi i nodi head.

![elenco di host](./media/hdinsight-high-availability-linux/hosts.png)

Selezionando il collegamento per uno dei nodi head verranno visualizzati i servizi e i componenti in esecuzione su tale nodo.

![Stato dei componenti](./media/hdinsight-high-availability-linux/nodeservices.png)

## Come accedere ai file di log sul nodo head secondario

###SSH

Durante la connessione a un nodo head tramite SSH, i file di registro sono disponibili su**/var/log**. Ad esempio, **/var/log/hadoop-yarn/yarn** contiene i registri per YARN.

Ogni nodo head può avere voci di registro univoche, perciò è consigliabile controllare i registri su entrambi.

###Ambari

> [AZURE.NOTE] L’accesso ai file di registro tramite Ambari richiede un tunnel SSH, poichè i siti web per i singoli servizi non sono esposti pubblicamente in Internet. Per informazioni sull'uso di un tunnel SSH, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

Dall'interfaccia utente Web Ambari, selezionare il servizio per il quale si desidera visualizzare i registri (ad esempio, YARN) e quindi utilizzare **Collegamenti rapidi** per selezionare il nodo head per il quale si vuole visualizzare i registri.

![Utilizzo dei collegamenti rapidi per visualizzare i registri](./media/hdinsight-high-availability-linux/viewlogs.png)

## Come configurare le dimensioni del nodo head ##

È possibile selezionare le dimensioni del nodo head solo durante la creazione del cluster. È possibile trovare un elenco delle varie dimensioni di VM disponibili per HDInsight, inclusi core, memoria e archiviazione locale per ognuna, sulla [pagina dei prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Quando si crea un nuovo cluster, è possibile specificare le dimensioni dei nodi. Di seguito viene spiegato come specificare le dimensioni utilizzando il [portale di Azure][preview-portal], [Azure PowerShell][azure-powershell], e [l’interfaccia della riga di comando di Azure][azure-cli]\:

* **Portale di Azure**: quando si crea un nuovo cluster, è possibile impostare le dimensioni (piano tariffario) dei nodi head e dei dati (ruolo di lavoro) del cluster:

	![Immagine della creazione guidata di cluster con la selezione delle dimensioni del nodo](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Interfaccia della riga di comando di Azure**: quando si utilizza il comando `azure hdinsight cluster create`, è possibile impostare le dimensioni del nodo head utilizzando il parametro `--headNodeSize`.

* **Azure PowerShell**: quando si utilizza il cmdlet `New-AzureRmHDInsightCluster`, è possibile impostare le dimensioni del nodo head utilizzando il parametro `-HeadNodeVMSize`.

##Passaggi successivi

In questo documento si è appreso il modo in cui Azure HDInsight fornisce disponibilità elevata per Hadoop. Per ulteriori informazioni sugli aspetti menzionati in questo documento, utilizzare quanto segue.

- [Riferimento REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)

- [Installare e configurare Azure PowerShell](../powershell-install-configure.md)

- [Gestire HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md)

- [provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0323_2016-->