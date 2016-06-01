<properties
   pageTitle="Distribuzione e gestione di topologie Apache Storm in HDInsight basato su Linux | Microsoft Azure"
   description="Informazioni su come distribuire, monitorare e gestire le topologie Apache Storm mediante Storm Dashboard in HDInsight basato su Linux. Utilizzare gli strumenti Hadoop per Visual Studio"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/17/2016"
   ms.author="larryfr"/>

# Distribuzione e gestione di topologie Apache Storm in HDInsight basato su Linux

In questo documento sono illustrati i concetti di gestione e monitoraggio delle topologie Storm in esecuzione su Storm basato su Linux in cluster HDInsight.

> [AZURE.IMPORTANT] I passaggi descritti in questo articolo richiedono una versione di Storm basata su Linux nel cluster HDInsight. Per informazioni sulla distribuzione e sul monitoraggio di topologie in HDInsight basato su Windows, vedere [Distribuire e gestire le topologie di Apache Storm in HDInsight basato su Windows](hdinsight-storm-deploy-monitor-topology.md)

## Prerequisiti

- **Storm basato su Linux in cluster HDInsight**: per i passaggi relativi alla creazione di un cluster, vedere [Introduzione ad Apache Storm in HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

- **Familiarità con SSH e SCP**: per altre informazioni sull'uso di SSH e SCP con HDInsight, vedere gli articoli seguenti:
    - **Client Linux, Unix o OS X**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    - **Client Windows**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- **Client SCP**: viene fornito con tutti i sistemi Linux, Unix e OS X. Per i client Windows, è consigliabile PSCP, disponibile tramite la [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## Avviare una topologia Storm

1. Connettersi al cluster HDInsight usando SSH. Sostituire **USERNAME** con il nome di accesso a SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Per altre informazioni sull'uso di SSH per connettersi a HDInsight, vedere i seguenti documenti:
    
        - **Linux, Unix or OS X clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Linux, OS X or Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        - **Windows clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Usare il comando seguente per avviare una topologia di esempio:

        storm jar storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Verrà avviata la topologia di esempio WordCount nel cluster. Verranno generate in modo casuale le frasi e verranno conteggiate le occorrenze di ogni parola nelle frasi.

    > [AZURE.NOTE] Durante l'invio di una topologia al cluster, è prima di tutto necessario copiare il file con estensione JAR contenente il cluster prima di usare il comando `storm`. A tale scopo, usare il comando `scp` dal client in cui è presente il file. Ad esempio: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > L'esempio WordCount e altri esempi di avvio dell'utilità storm sono già inclusi nel cluster in `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Monitorare e gestire usando il comando storm

L'utilità `storm` consente di usare le topologie in esecuzione dalla riga di comando. Di seguito è riportato un elenco di comandi di uso comune. Per visualizzare l'elenco completo dei comandi, usare `storm -h`.

###Visualizzare l'elenco delle topologie

Usare il comando seguente per ottenere un elenco delle topologie in esecuzione:

    storm list
    
Verranno restituite informazioni simili alle seguenti:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

###Disattivare e riattivare

La disattivazione di una topologia comporta la sua sospensione finché non viene terminata o riattivata. Per disattivare e riattivare, usare i seguenti comandi:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

###Terminare una topologia in esecuzione

Una volta avviate, le topologie di Storm continueranno a rimanere in esecuzione finché non vengono arrestate. Per arrestare una topologia, usare il comando seguente:

    storm stop TOPOLOGYNAME

###Ribilanciare

Il ribilanciamento di una topologia consente al sistema di analizzare il parallelismo della topologia. Ad esempio, se il cluster è stato ridimensionato per aggiungere altre note, il ribilanciamento consentirà una topologia in esecuzione di usare i nuovi nodi.

> [AZURE.WARNING] Il ribilanciamento di una topologia disattiva innanzitutto la topologia, ridistribuisce i processi di lavoro in modo uniforme nel cluster, quindi restituisce la topologia allo stato in cui si trovava prima che venisse eseguito il ribilanciamento. Se la topologia era attiva, tornerà a essere attiva di nuovo. Se invece era disattivata, rimarrà disattivata.

    storm rebalance TOPOLOGYNAME

##Monitorare e gestire usando l'interfaccia utente di Storm

L'interfaccia utente di Storm è inclusa nel cluster HDInsight e fornisce un'interfaccia Web da usare con le topologie in esecuzione. Per visualizzare l'interfaccia utente di Storm, usare un Web browser per aprire \_\___https://CLUSTERNAME.azurehdinsight.net/stormui__, dove __CLUSTERNAME__ è il nome del cluster.

> [AZURE.NOTE] Se viene richiesto di fornire un nome utente e una password, immettere l'amministratore del cluster e la password usati durante la creazione del cluster.


### Pagina principale

Nella pagina principale dell'interfaccia utente di Storm sono disponibili le informazioni seguenti.
- **Cluster summary**: informazioni di base sul cluster Storm.
- **Topology summary**: elenco delle topologie in esecuzione. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su topologie specifiche.
- **Supervisor summary**: informazioni su Storm Supervisor.
- **Nimbus configuration**: configurazione Nimbus per il cluster.

### Topology summary

Se si seleziona un collegamento nella sezione **Topology summary**, verranno visualizzate le informazioni seguenti sulla topologia.
- **Topology summary**: informazioni di base sulla topologia.
- **Topology actions**: azioni di gestione che è possibile eseguire per la topologia.
  - **Activate**: riprende l'elaborazione di una topologia disattivata.
  - **Deactivate**: sospende una topologia in esecuzione.
  - **Rebalance**: regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi del cluster. Questo consente alla topologia di regolare il parallelismo per compensare l'aumento o la diminuzione del numero di nodi del cluster.

      Per altre informazioni, vedere l'articolo relativo al <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">parallelismo di una topologia Storm</a>.

  - **Kill**: termina una topologia Storm dopo il timeout specificato.

- **Topology stats**: statistiche sulla topologia. Usare i collegamenti disponibili nella colonna **Window** per impostare l'intervallo di tempo per le rimanenti voci della pagina.
- **Spouts**: spout usati dalla topologia. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su spout specifici.
- **Bolts**: bolt usati nella topologia. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su bolt specifici.
- **Topology configuration**: configurazione della topologia selezionata.

### Riepilogo di spout e bolt

Se si seleziona un elemento nella sezione **Spouts** o **Bolts**, verranno visualizzate le informazioni seguenti:
- **Component summary**: informazioni di base sullo spout o sul bolt.
- **Spout/Bolt stats**: statistiche relative allo spout o al bolt. Usare i collegamenti disponibili nella colonna **Window** per impostare l'intervallo di tempo per le rimanenti voci della pagina.
- **Input stats** (solo bolt): informazioni sui flussi di input usati dal bolt.
- **Output stats**: informazioni sui flussi generati dallo spout o dal bolt.
- **Executors**: informazioni sulle istanze dello spout o del bolt. Selezionare la voce **Port** relativa a un esecutore specifico per visualizzare il log delle informazioni di diagnostica generate per questa istanza.
- **Errors**: informazioni su eventuali errori dello spout o del bolt.

## API REST

L'interfaccia utente di Storm si basa sull'API REST. È pertanto possibile eseguire funzionalità di gestione e monitoraggio simili usando l'API REST. L'API REST consente di creare strumenti personalizzati per la gestione e il monitoraggio di topologie Storm.

Per altre informazioni, vedere l'articolo relativo all'[API REST dell'interfaccia utente di Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Le seguenti informazioni sono specifiche per l'uso dell'API REST con Apache Storm in HDInsight.

> [AZURE.IMPORTANT] L'API REST di Storm non è disponibile pubblicamente su Internet ed è necessario accedervi usando un tunnel SSH al nodo head del cluster HDInsight. Per informazioni sulla creazione e sull'uso di un tunnel SSH, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

### URI di base

L'URI di base per l'API REST nei cluster HDInsight basati su Linux è disponibile nel nodo head in * ***https://HEADNODEFQDN:8744/api/v1/**; tuttavia, il nome di dominio del nodo head viene generato durante la creazione del cluster e non è statico.

È possibile trovare il nome di dominio completo (FQDN) per il nodo head del cluster in diversi modi:

* __Da una sessione SSH__: utilizzare il comando `headnode -f` da una sessione SSH al cluster.
* __Dal Web Ambari__: selezionare __Servizi__ dalla parte superiore della pagina, quindi selezionare __Storm__. Dalla scheda __Riepilogo__ selezionare __Server dell'interfaccia utente di Storm__. Il nome FQDN del nodo su cui sono in esecuzione l'interfaccia utente di Storm e le API REST sarà nella parte superiore della pagina.
* __Dall'API REST Ambari__: utilizzare il comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME
.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` per recuperare informazioni sul nodo su cui sono in esecuzione l'interfaccia utente di Storm e le API REST. Sostituire __PASSWORD__ con la password di amministratore per il cluster. Sostituire __CLUSTERNAME__ con il nome del cluster Nella risposta, la voce "host\_name" contiene il nome di dominio completo del nodo.

### Autenticazione

Le richieste all'API REST devono usare l'**autenticazione di base** con il nome e la password amministratore del cluster HDInsight.

> [AZURE.NOTE] Poiché l'autenticazione di base viene inviata in testo non crittografato, è necessario usare **sempre** HTTPS per proteggere le comunicazioni con il cluster.

### Valori restituiti

Le informazioni restituite dall'API REST possono essere usate solo nel cluster o nei computer che si trovano nella stessa rete virtuale di Azure del cluster. Ad esempio, il nome di dominio completo (FQDN) restituito per i server Zookeeper non sarà accessibile da Internet.

## Passaggi successivi

A questo punto, dopo aver appreso come distribuire e monitorare le topologie usando Storm Dashboard, è possibile passare all'argomento [Sviluppare topologie basate su Java usando Maven](hdinsight-storm-develop-java-topology.md).

Per un elenco di altre topologie di esempio, vedere [Esempi di topologie Storm per Apache Storm in HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0518_2016-->