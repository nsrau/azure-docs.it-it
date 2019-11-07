---
title: Alta disponibilità per Hadoop - Azure HDInsight
description: Informazioni su come i cluster HDInsight migliorano l'affidabilità e la disponibilità tramite l'uso di un nodo head aggiuntivo. Informazioni su come questo influisce sui servizi di Hadoop come Ambari e Hive, e anche su come connettersi singolarmente a ogni nodo head tramite SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: alta disponibilità di hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 8b914b8ffe995cf31f8a22b6f80250431facc770
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682242"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Disponibilità e affidabilità dei cluster Apache Hadoop in HDInsight

I cluster HDInsight offrono due nodi head per aumentare la disponibilità e l'affidabilità dei servizi e dei processi di Apache Hadoop in esecuzione.

Hadoop ottiene alta disponibilità e affidabilità replicando i servizi e i dati su più nodi di un cluster. Tuttavia le distribuzioni standard di Hadoop hanno in genere un singolo nodo head. Eventuali interruzioni del singolo nodo head possono causare l'interruzione del funzionamento del cluster. HDInsight fornisce due nodi head per migliorare la disponibilità e affidabilità di Hadoop.

## <a name="availability-and-reliability-of-nodes"></a>Disponibilità e affidabilità dei nodi

I nodi in un cluster HDInsight vengono implementati con macchine virtuali di Azure. Le sezioni seguenti illustrano i singoli tipi di nodo usati con HDInsight.

> [!NOTE]  
> Non tutti i tipi di nodo vengono usati per un tipo di cluster. Ad esempio, un tipo di cluster Hadoop non ha alcun nodo Nimbus. Per altre informazioni sui nodi usati dai tipi di cluster HDInsight, vedere la sezione Tipi di cluster del documento [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

### <a name="head-nodes"></a>Nodi head

Per garantire un'alta disponibilità dei servizi Hadoop, HDInsight fornisce due nodi head. Entrambi i nodi head sono contemporaneamente attivi e in esecuzione all'interno del cluster HDInsight. Alcuni servizi, ad esempio Apache HDFS o Apache Hadoop YARN, sono "attivi" in qualsiasi momento soltanto in un nodo head. Altri servizi come HiveServer2 o Hive Metastore sono attivi su entrambi i nodi head allo tesso tempo.

I nodi head, e altri nodi in HDInsight, hanno un valore numerico all'interno del nome host del nodo. Ad esempio, `hn0-CLUSTERNAME` o `hn4-CLUSTERNAME`.

> [!IMPORTANT]  
> Non associare il valore numerico con il fatto che un nodo sia primario o secondario. Il valore numerico serve solo per specificare un nome univoco per ogni nodo.

### <a name="nimbus-nodes"></a>Nodi Nimbus

I nodi nimbus sono disponibili con i cluster Apache Storm. I nodi Nimbus forniscono una funzionalità simile a JobTracker di Hadoop mediante la distribuzione e il monitoraggio dell'elaborazione tra nodi del ruolo di lavoro. HDInsight offre due nodi Nimbus per i cluster Storm

### <a name="apache-zookeeper-nodes"></a>Nodi di Apache ZooKeeper

I nodi [ZooKeeper](https://zookeeper.apache.org/) vengono usati per la designazione di leader dei servizi master nei nodi head. Sono inoltre usati per garantire che i servizi, i nodi di dati (ruolo di lavoro) e i gateway sappiano quale nodo Head è attivo un servizio master. Per impostazione predefinita, HDInsight specifica tre nodi ZooKeeper.

### <a name="worker-nodes"></a>Nodi di lavoro

I nodi di lavoro eseguono l'analisi dei dati effettivi quando un processo viene inviato al cluster. In caso di errore di un nodo del ruolo di lavoro, l'attività che il nodo stava eseguendo viene inviata a un altro nodo del ruolo di lavoro. Per impostazione predefinita, HDInsight crea quattro nodi del ruolo di lavoro. È possibile modificare questo numero in base alle proprie esigenze, durante e dopo la creazione del cluster.

### <a name="edge-node"></a>nodo perimetrale

Un nodo perimetrale non partecipa attivamente all'analisi dei dati all'interno del cluster. Viene usato dagli sviluppatori o dai data scientist quando si lavora con Hadoop. Il nodo perimetrale si trova nella stessa rete virtuale di Azure come gli altri nodi del cluster e può accedere direttamente a tutti gli altri nodi. Il nodo perimetrale può essere usato senza sottrarre risorse ai servizi critici di Hadoop o ai processi di analisi.

Attualmente, ML Services in HDInsight è l'unico tipo di cluster che fornisce un nodo perimetrale per impostazione predefinita. Per ML Services in HDInsight, il nodo perimetrale viene usato per testare il codice R in locale nel nodo prima dell'invio al cluster per l'elaborazione distribuita.

Per informazioni sull'uso di un nodo perimetrale con tipi di cluster diversi, vedere il documento [Usare nodi perimetrali in HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="accessing-the-nodes"></a>Accesso ai nodi head

L'accesso al cluster tramite Internet avviene mediante un gateway pubblico. L'accesso è limitato alla connessione ai nodi head e, se ne esiste uno, al nodo perimetrale. L'accesso ai servizi in esecuzione nei nodi head non è influenzato dalla presenza di più nodi head. Il gateway pubblico instrada le richieste al nodo head che ospita il servizio richiesto. Ad esempio, se Apache Ambari è attualmente ospitato nel nodo head secondario, il gateway instrada le richieste in ingresso per Ambari a quel nodo.

L'accesso tramite il gateway pubblico è limitato alle porte 443 (HTTPS), 22 e 23.

|Port |Descrizione |
|---|---|
|443|Usato per accedere a Ambari e ad altre interfacce utente Web o API REST ospitate nei nodi head.|
|22|Usato per accedere al nodo head primario o al nodo perimetrale con SSH.|
|23|Usato per accedere al nodo head secondario con SSH. Ad esempio, `ssh username@mycluster-ssh.azurehdinsight.net` si connette al nodo head primario del cluster denominato **mycluster**.|

Per altre informazioni sull'uso di SSH, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Nomi di dominio completo interni (FQDN)

I nodi di un cluster HDInsight hanno un indirizzo IP interno e un FQDN accessibili solo dal cluster. Quando si accede ai servizi del cluster utilizzando l'indirizzo IP o i FQDN interni, è necessario utilizzare Ambari per verificare l'IP o i FQDN da utilizzare per l’accesso al servizio.

Ad esempio, il servizio Apache Oozie può essere eseguito solo su un nodo head e l'utilizzo del comando `oozie` da una sessione SSH richiede l'URL del servizio. Tale URL può essere recuperato da Ambari tramite il comando seguente:

```bash
export password='PASSWORD'
export clusterName="CLUSTERNAME"

curl -u admin:$password "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url
```

Questo comando restituisce un valore simile al seguente, che contiene l'URL interno da usare con il comando `oozie`:

```output
"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"
```

Per altre informazioni sull'uso dell'API REST Ambari, vedere [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Accesso ad altri tipi di nodo

È possibile connettersi a nodi che non sono direttamente accessibili tramite Internet usando i metodi seguenti:

|Metodo |Descrizione |
|---|---|
|SSH|Dopo avere stabilito la connessione a un nodo head usando SSH, è possibile usare SSH dal nodo head per connettersi ad altri nodi del cluster. Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
|Tunnel SSH|Se è necessario accedere a un servizio Web ospitato in uno dei nodi non esposti a Internet, è necessario usare un tunnel SSH. Per altre informazioni, vedere il documento [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).|
|Rete virtuale di Azure|Se il cluster HDInsight fa parte di una rete virtuale di Azure, tutte le risorse della stessa rete virtuale possono accedere direttamente a tutti i nodi del cluster. Per ulteriori informazioni, vedere il documento [pianificare una rete virtuale per HDInsight](hdinsight-plan-virtual-network-deployment.md) .|

## <a name="how-to-check-on-a-service-status"></a>Come controllare lo stato di un servizio

Per controllare lo stato dei servizi in esecuzione nei nodi head, usare l'interfaccia utente Web di Ambari o l'API REST Ambari.

### <a name="ambari-web-ui"></a>Interfaccia utente Web Ambari

L'interfaccia Web utente di Ambari può essere visualizzata all'indirizzo `https://CLUSTERNAME.azurehdinsight.net`. Sostituire **CLUSTERNAME** con il nome del cluster. Se richiesto, immettere le credenziali dell’utente HTTP del cluster. Il nome utente HTTP predefinito è **admin** e la password è la password inserita durante la creazione del cluster.

Nella pagina di Ambari i servizi installati sono elencati a sinistra.

![Servizi installati di Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-installed-services.png)

Esistono molte icone che possono essere visualizzate accanto a un servizio per indicare lo stato. È possibile visualizzare eventuali avvisi relativi a un servizio utilizzando il collegamento **Avvisi** nella parte superiore della pagina.  Ambari offre diversi avvisi predefiniti.

Gli avvisi seguenti consentono di monitorare la disponibilità di un cluster:

| Nome avviso                               | Descrizione                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stato monitoraggio metrica                    | Questo avviso indica lo stato del processo di monitoraggio delle metriche in base a quanto determinato dallo script di stato del monitoraggio.                                                                                   |
| Heartbeat agente Ambari                   | Questo avviso viene generato se il server ha perso il contatto con un agente.                                                                                                                        |
| Processo del server ZooKeeper                 | Questo avviso a livello di host viene attivato se il processo del server ZooKeeper non può essere determinato come attivo e in ascolto sulla rete.                                                               |
| Stato server metadati IOCache           | Questo avviso a livello di host viene attivato se non è possibile determinare il server di metadati IOCache e rispondere alle richieste client                                                            |
| Interfaccia utente Web di JournalNoderi                       | Questo avviso a livello di host viene attivato se l'interfaccia utente Web di JournalNode non è raggiungibile.                                                                                                                 |
| Server Spark2 Thrifty                     | Questo avviso a livello di host viene attivato se non è possibile determinare il server di risparmio Spark2.                                                                                                |
| Processo server cronologia                   | Questo avviso a livello di host viene attivato se non è possibile stabilire il processo del server di cronologia in modo che sia attivo e in ascolto sulla rete.                                                                |
| Interfaccia utente Web del server di cronologia                    | Questo avviso a livello di host viene attivato se l'interfaccia utente Web del server di cronologia non è raggiungibile.                                                                                                              |
| interfaccia utente Web di `ResourceManager`                   | Questo avviso a livello di host viene attivato se l'interfaccia utente Web di `ResourceManager` non è raggiungibile.                                                                                                             |
| Riepilogo integrità NodeManager               | Questo avviso a livello di servizio viene attivato in caso di NodeManagers non integro                                                                                                                    |
| Interfaccia utente Web della sequenza temporale dell'app                      | Questo avviso a livello di host viene attivato se l'interfaccia utente Web del server della sequenza temporale dell'app non è raggiungibile.                                                                                                         |
| Riepilogo integrità dataNode                  | Questo avviso a livello di servizio viene attivato se sono presenti nodi di tipo non integro                                                                                                                       |
| Interfaccia utente Web di NameNoderi                          | Questo avviso a livello di host viene attivato se l'interfaccia utente Web di NameNode non è raggiungibile.                                                                                                                    |
| Processo del controller di failover ZooKeeper    | Questo avviso a livello di host viene attivato se il processo del controller di failover di ZooKeeper non può essere confermato come attivo e in ascolto sulla rete.                                                   |
| Interfaccia utente Web del server OOZIE                      | Questo avviso a livello di host viene attivato se l'interfaccia utente Web del server oozie non è raggiungibile.                                                                                                                |
| Stato del server OOZIE                      | Questo avviso a livello di host viene attivato se non è possibile determinare il server oozie e rispondere alle richieste client.                                                                      |
| Processo del Metastore hive                   | Questo avviso a livello di host viene attivato se il processo del Metastore hive non può essere determinato come attivo e in ascolto sulla rete.                                                                 |
| Processo HiveServer2                      | Questo avviso a livello di host viene attivato se non è possibile determinare il del e rispondere alle richieste client.                                                                        |
| Stato del server WebHCat                    | Questo avviso a livello di host viene attivato se lo stato del server `templeton` non è integro.                                                                                                            |
| Percentuale di server ZooKeeper disponibili      | Questo avviso viene generato se il numero di server ZooKeeper inattivi nel cluster è superiore alla soglia critica configurata. Aggrega i risultati dei controlli del processo ZooKeeper.     |
| Server Spark2 Livio                       | Questo avviso a livello di host viene attivato se non è possibile determinare il server Livy2.                                                                                                        |
| Server della cronologia Spark2                    | Questo avviso a livello di host viene attivato se non è possibile determinare il server della cronologia Spark2.                                                                                               |
| Processo di raccolta metriche                | Questo avviso viene generato se l'agente di raccolta delle metriche non può essere confermato come attivo e in ascolto sulla porta configurata per il numero di secondi uguale alla soglia.                                 |
| Agente di raccolta metriche-processo HBase Master | Questo avviso viene generato se i processi master HBase dell'agente di raccolta metriche non possono essere confermati per essere in ascolto sulla rete per la soglia critica configurata, espresso in secondi. |
| Percentuale di monitoraggi metriche disponibili       | Questo avviso viene generato se una percentuale di metriche di monitoraggio dei processi non è in ascolto sulla rete per le soglie di avviso e critici configurate.                             |
| Percentuale di NodeManagers disponibili           | Questo avviso viene generato se il numero di NodeManagers inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo NodeManager.        |
| Stato NodeManager                       | Questo avviso a livello di host controlla la proprietà integrità nodo disponibile dal componente NodeManager.                                                                                              |
| Interfaccia utente Web di NodeManagerri                       | Questo avviso a livello di host viene attivato se l'interfaccia utente Web di NodeManager non è raggiungibile.                                                                                                                 |
| Stato disponibilità elevata NameNode        | Questo avviso a livello di servizio viene attivato se il NameNode attivo NameNode o standby non è in esecuzione.                                                                                     |
| Processo dataNode                         | Questo avviso a livello di host viene attivato se i singoli processi dataNode non possono essere stabiliti per essere in ascolto sulla rete.                                                         |
| Interfaccia utente Web di dataNode                          | Questo avviso a livello di host viene attivato se l'interfaccia utente Web di dataNode non è raggiungibile.                                                                                                                    |
| Percentuale di JournalNodes disponibili           | Questo avviso viene generato se il numero di JournalNodes inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo JournalNode.        |
| Percentuale di dataNode disponibili              | Questo avviso viene generato se il numero di nodi inattivi nel cluster è maggiore della soglia critica configurata. Aggrega i risultati dei controlli del processo dataNode.              |
| Stato server Zeppelin                   | Questo avviso a livello di host viene attivato se non è possibile determinare se il server Zeppelin è attivo e risponde alle richieste dei client.                                                                   |
| Processo interattivo HiveServer2          | Questo avviso a livello di host viene attivato se non è possibile determinare il HiveServerInteractive e rispondere alle richieste client.                                                             |
| Applicazione LLAP                         | Questo avviso viene generato se non è possibile determinare l'applicazione LLAP e rispondere alle richieste.                                                                                    |

È possibile selezionare ogni servizio per visualizzare ulteriori informazioni su di esso.

Mentre nella pagina del servizio vengono fornite informazioni sullo stato e sulla configurazione di ogni servizio, non vengono fornite informazioni sul nodo head in cui è in esecuzione il servizio. Per visualizzare questa informazione,  utilizzare il collegamento **Host** nella parte superiore della pagina. La pagina visualizza gli host del cluster, inclusi i nodi head.

![Elenco degli host nodo head di Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-hosts-list.png)

Quando si seleziona il collegamento per uno dei nodi head, vengono visualizzati i servizi e i componenti in esecuzione su tale nodo.

![Stato del componente Apache Ambari](./media/hdinsight-high-availability-linux/hdinsight-node-services.png)

Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight mediante l'uso dell'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>API REST Ambari

L'API REST Ambari è disponibile su Internet. Il gateway pubblico HDInsight gestisce il routing delle richieste per il nodo head che attualmente ospita l'API REST.

È possibile utilizzare il comando seguente per verificare lo stato di un servizio tramite l'API REST di Ambari:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state
```

* Sostituire **PASSWORD** con la password dell'account dell'utente HTTP (admin).
* Sostituire **CLUSTERNAME** con il nome del cluster.
* Sostituire **SERVICENAME** con il nome del servizio di cui controllare lo stato.

Ad esempio, per controllare lo stato del servizio **HDFS** in un cluster denominato **mycluster**, con la password **password**, è necessario usare il comando seguente:

```bash
curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state
```

La risposta restituita è simile al codice JSON seguente:

```json
{
    "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
    "ServiceInfo" : {
    "cluster_name" : "mycluster",
    "service_name" : "HDFS",
    "state" : "STARTED"
    }
}
```

L'URL indica che il servizio è attualmente in esecuzione sul nodo head denominato **hn0-CLUSTERNAME**.

Lo stato indica che il servizio è in esecuzione, o **AVVIATO**.

Se non si conoscono i servizi installati nel cluster, è possibile usare il comando seguente per recuperare un elenco:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services
```

Per altre informazioni sull'uso dell'API REST Ambari, vedere [Gestire i cluster HDInsight mediante l'API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Componenti del servizio

I servizi possono includere componenti di cui si desidera controllare lo stato singolarmente. HDFS, ad esempio, contiene il componente NameNode. Per visualizzare informazioni su un componente, il comando è:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

Se non si conoscono i componenti forniti da un servizio, è possibile usare il comando seguente per recuperare un elenco:

```bash
curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
```

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Come accedere ai file di log nel nodo head

### <a name="ssh"></a>SSH

Durante la connessione a un nodo head tramite SSH, i file di registro sono disponibili su **/var/log**. Ad esempio, **/var/log/hadoop-yarn/yarn** contiene i registri per YARN.

Ogni nodo head può avere voci di log univoche, perciò è consigliabile controllare i log su entrambi.

### <a name="sftp"></a>SFTP

È anche possibile connettersi al nodo head usando SSH File Transfer Protocol o Secure File Transfer Protocol (SFTP) e scaricare direttamente i file di log.

In modo analogo all'uso di un client SSH, quando si stabilisce la connessione al cluster è necessario specificare il nome dell'account utente SSH e l'indirizzo SSH del cluster. Ad esempio, `sftp username@mycluster-ssh.azurehdinsight.net`. Specificare la password per l'account quando richiesto oppure specificare una chiave pubblica tramite il parametro `-i`.

Una volta stabilita la connessione, viene visualizzato un messaggio di `sftp>`. Da questo prompt è possibile modificare le directory nonché caricare e scaricare i file. Ad esempio, i comandi seguenti consentono di passare alla directory **/var/log/hadoop/hdfs** directory e quindi scaricare tutti i file nella directory.

    cd /var/log/hadoop/hdfs
    get *

Per un elenco di comandi disponibili, immettere `help` al prompt `sftp>`.

> [!NOTE]  
> Esistono anche interfacce grafiche che consentono di visualizzare il file system quando si è connessi tramite SFTP. Ad esempio, [MobaXTerm](https://mobaxterm.mobatek.net/) consente di sfogliare il file system con un'interfaccia simile a Esplora risorse.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Per accedere ai file di log tramite Ambari, è necessario usare un tunnel SSH. Le interfacce Web per i singoli servizi non sono esposte pubblicamente su Internet. Per informazioni sull'uso del tunnel SSH, vedere il documento [Usare il tunneling SSH](hdinsight-linux-ambari-ssh-tunnel.md).

Dall'interfaccia utente Web di Ambari, selezionare il servizio di cui si vogliono visualizzare i log, ad esempio, YARN. Usare quindi **Collegamenti rapidi** per selezionare il nodo head di cui visualizzare i log.

![Utilizzo dei collegamenti rapidi per visualizzare i log](./media/hdinsight-high-availability-linux/quick-links-view-logs.png)

## <a name="how-to-configure-the-node-size"></a>Come configurare le dimensioni del nodo

È possibile selezionare le dimensioni di un nodo solo durante la creazione del cluster. È possibile trovare un elenco delle varie dimensioni di VM disponibili per HDInsight sulla [pagina dei prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Quando si crea un cluster, è possibile specificare le dimensioni dei nodi. Le informazioni seguenti forniscono indicazioni su come specificare le dimensioni usando il [portale di Azure](https://portal.azure.com/), il [modulo di Azure PowerShell AZ](/powershell/azureps-cmdlets-docs)e l' [interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)della riga di comando di Azure:

* **Portale di Azure**: quando si crea un cluster, è possibile impostare le dimensioni dei nodi usati dal cluster:

    ![Immagine della creazione guidata di cluster con la selezione delle dimensioni del nodo](./media/hdinsight-high-availability-linux/hdinsight-headnodesize.png)

* **Interfaccia**della riga di comando di Azure: quando si usa il comando [`az hdinsight create`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) , è possibile impostare le dimensioni dei nodi head, Worker e ZooKeeper usando i parametri `--headnode-size`, `--workernode-size`e `--zookeepernode-size`.

* **Azure PowerShell**: quando si usa il cmdlet [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) , è possibile impostare le dimensioni dei nodi head, Worker e ZooKeeper usando i parametri `-HeadNodeSize`, `-WorkerNodeSize`e `-ZookeeperNodeSize`.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sugli elementi trattati in questo articolo, vedere:

* [Riferimento REST Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installare e configurare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com//cli/azure/install-azure-cli?view=azure-cli-latest)
* [Installare e configurare Azure PowerShell modulo AZ](/powershell/azure/overview)
* [Gestire HDInsight tramite Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)
