---
title: Distribuire e gestire topologie Apache Storm in HDInsight basato su Linux | Documentazione Microsoft
description: Informazioni su come distribuire, monitorare e gestire le topologie Apache Storm mediante Storm Dashboard in HDInsight basato su Linux. Utilizzare gli strumenti Hadoop per Visual Studio
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/16/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: b9e82463030807d2674594e73f762fe93515d423
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017


---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>Distribuire e gestire topologie Apache Storm in HDInsight

In questo documento sono illustrati i concetti di gestione e monitoraggio delle topologie Storm in esecuzione su Storm in cluster HDInsight.

> [!IMPORTANT]
> I passaggi descritti in questo articolo richiedono una versione di Storm basata su Linux nel cluster HDInsight. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>
> Per informazioni sulla distribuzione e sul monitoraggio di topologie in HDInsight basato su Windows, vedere [Distribuire e gestire le topologie di Apache Storm in HDInsight basato su Windows](hdinsight-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Prerequisiti

* **Storm basato su Linux in cluster HDInsight**: per i passaggi relativi alla creazione di un cluster, vedere [Introduzione ad Apache Storm in HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) .

* (Facoltativo) **Familiarità con SSH e SCP**: per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* (Facoltativo) **Visual Studio**: Azure SDK 2.5.1 o versione successiva e Data Lake Tools per Visual Studio. Per altre informazioni, vedere [Introduzione all'uso di Data Lake Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

    Una delle seguenti versioni di Visual Studio:

  * Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015, qualsiasi edizione

  * Visual Studio 2017, qualsiasi edizione. Data Lake Tools per Visual Studio 2017 viene installato come parte del carico di lavoro di Azure.

## <a name="submit-a-topology-visual-studio"></a>Inviare una topologia: Visual Studio

HDInsight Tools consente di inviare topologie C# o ibride al cluster Storm. Nei seguenti passaggi viene usata un'applicazione di esempio. Per informazioni sulla creazione delle proprie tipologie, vedere [Sviluppare topologie C# per Apache Storm in HDInsight tramite Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

1. Se la versione più recente di Data Lake Tools per Visual Studio non è ancora installata, vedere [Introduzione all'uso di Data Lake Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > Data Lake Tools per Visual Studio veniva precedentemente chiamato HDInsight Tools per Visual Studio.
    >
    > Data Lake Tools per Visual Studio è incluso nel __carico di lavoro di Azure__ per Visual Studio 2017.

2. Aprire Visual Studio e selezionare **File** > **Nuovo** > **Progetto**.

3. Nella finestra di dialogo **Nuovo progetto** espandere **Installato** > **Modelli** e quindi selezionare **HDInsight**. Dall'elenco dei modelli selezionare **Storm Sample**. Nella parte inferiore della finestra di dialogo digitare un nome per l'applicazione.

    ![immagine](./media/hdinsight-storm-deploy-monitor-topology-linux/sample.png)

4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto e selezionare **Submit to Storm on HDInsight**.

   > [!NOTE]
   > Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

5. Selezionare il cluster Storm in HDInsight dall'elenco a discesa **Storm Cluster** e quindi selezionare **Submit**. È possibile verificare se l'invio è riuscito o meno usando la finestra **Output** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Inviare una topologia: SSH e il comando Storm

1. Connettersi al cluster HDInsight usando SSH. Sostituire **USERNAME** con il nome di accesso a SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Per altre informazioni sull'uso di SSH per connettersi a HDInsight, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare il comando seguente per avviare una topologia di esempio:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Questo comando consente di avviare la topologia di esempio WordCount nel cluster. Questa topologia genera in modo casuale le frasi e conteggia le occorrenze di ogni parola nelle frasi.

   > [!NOTE]
   > Durante l'invio di una topologia al cluster, è prima di tutto necessario copiare il file con estensione JAR contenente il cluster prima di usare il comando `storm`. Usare il comando `scp` per copiare il file nel cluster. Ad esempio: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > L'esempio WordCount e altri esempi di avvio dell'utilità storm sono già inclusi nel cluster in `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Inviare una topologia: a livello di programmazione

È possibile distribuire a livello di codice una topologia in Storm su HDInsight comunicando con il servizio Nimbus ospitato nel cluster. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) è disponibile un'applicazione Java di esempio che illustra come distribuire e avviare una topologia tramite il servizio Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorare e gestire: Visual Studio

Dopo che la topologia è stata inviata correttamente con Visual Studio, verrà visualizza la vista **Topologie Storm** relativa al cluster. Per visualizzare informazioni sulla topologia in esecuzione, selezionarla dall'elenco.

![monitor Visual Studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

> [!NOTE]
> È possibile visualizzare **Storm Topologies** anche da **Esplora server**, espandendo **Azure** > **HDInsight** e quindi facendo clic su un cluster Storm in HDInsight e selezionando **View Storm Topologies**.

Selezionare la forma degli spout o dei bolt per visualizzare informazioni su questi componenti. Viene aperta una nuova finestra per ogni elemento selezionato.

### <a name="deactivate-and-reactivate"></a>Disattivare e riattivare

La disattivazione di una topologia comporta la sua sospensione finché non viene terminata o riattivata. Per eseguire queste operazioni, usare i pulsanti __Disattiva__ e __Riattiva__ nella parte superiore del __Topology Summary__ (Riepilogo topologia).

### <a name="rebalance"></a>Ribilanciare

Il ribilanciamento di una topologia consente al sistema di analizzare il parallelismo della topologia. Ad esempio, se il cluster è stato ridimensionato per aggiungere altre note, il ribilanciamento consente a una topologia di visualizzare i nuovi nodi.

Per ribilanciare una topologia, usare il pulsante __Ribilancia__ nella parte superiore di __Topology Summary__ (Riepilogo topologia).

> [!WARNING]
> Il ribilanciamento di una topologia disattiva innanzitutto la topologia, ridistribuisce i processi di lavoro in modo uniforme nel cluster, quindi restituisce la topologia allo stato in cui si trovava prima che venisse eseguito il ribilanciamento. Se la topologia era attiva, tornerà a essere attiva di nuovo. Se invece era disattivata, rimarrà disattivata.

### <a name="kill-a-topology"></a>Terminare una topologia

Le topologie Storm continuano l'esecuzione fino a quando non vengono arrestate o fino a quando il cluster non viene eliminato. Per arrestare una topologia, usare il pulsante __Termina__ nella parte superiore di __Topology Summary__ (Riepilogo topologia).

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorare e gestire: SSH e il comando Storm

L'utilità `storm` consente di usare le topologie in esecuzione dalla riga di comando. Per visualizzare l'elenco completo dei comandi, usare `storm -h` .

### <a name="list-topologies"></a>Visualizzare l'elenco delle topologie

Usare il comando seguente per ottenere un elenco delle topologie in esecuzione:

    storm list

Questo comando restituisce informazioni simili al testo seguente:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Disattivare e riattivare

La disattivazione di una topologia comporta la sua sospensione finché non viene terminata o riattivata. Per disattivare e riattivare, usare i comandi seguenti:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Terminare una topologia in esecuzione

Una volta avviate, le topologie Storm continueranno a rimanere in esecuzione finché non vengono arrestate. Per arrestare una topologia, usare il comando seguente:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Ribilanciare

Il ribilanciamento di una topologia consente al sistema di analizzare il parallelismo della topologia. Ad esempio, se il cluster è stato ridimensionato per aggiungere altre note, il ribilanciamento consente a una topologia di visualizzare i nuovi nodi.

> [!WARNING]
> Il ribilanciamento di una topologia disattiva innanzitutto la topologia, ridistribuisce i processi di lavoro in modo uniforme nel cluster, quindi restituisce la topologia allo stato in cui si trovava prima che venisse eseguito il ribilanciamento. Se la topologia era attiva, tornerà a essere attiva di nuovo. Se invece era disattivata, rimarrà disattivata.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorare e gestire: interfaccia utente di Storm

L'interfaccia utente di Storm è inclusa nel cluster HDInsight e fornisce un'interfaccia Web da usare con le topologie in esecuzione. Per visualizzare l'interfaccia utente di Storm, usare un Web browser per aprire **https://CLUSTERNAME.azurehdinsight.net/stormui**, dove **CLUSTERNAME** corrisponde al nome del cluster.

> [!NOTE]
> Se viene richiesto di fornire un nome utente e una password, immettere l'amministratore del cluster e la password usati durante la creazione del cluster.

### <a name="main-page"></a>Pagina principale

Nella pagina principale dell'interfaccia utente di Storm sono disponibili le informazioni seguenti.

* **Cluster summary**: informazioni di base sul cluster Storm.
* **Topology summary**: elenco delle topologie in esecuzione. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su topologie specifiche.
* **Supervisor summary**: informazioni su Storm Supervisor.
* **Nimbus configuration**: configurazione Nimbus per il cluster.

### <a name="topology-summary"></a>Topology summary

Se si seleziona un collegamento nella sezione **Topology summary** , verranno visualizzate le informazioni seguenti sulla topologia.

* **Topology summary**: informazioni di base sulla topologia.
* **Topology actions**: azioni di gestione che è possibile eseguire per la topologia.

  * **Activate**: riprende l'elaborazione di una topologia disattivata.
  * **Deactivate**: sospende una topologia in esecuzione.
  * **Rebalance**: regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi del cluster. Questa operazione consente alla topologia di regolare il parallelismo per compensare l'aumento o la diminuzione del numero di nodi del cluster.

    Per altre informazioni, vedere l'articolo relativo al <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">parallelismo di una topologia Storm</a>.
  * **Kill**: termina una topologia Storm dopo il timeout specificato.
* **Topology stats**: statistiche sulla topologia. Usare i collegamenti disponibili nella colonna **Finestra** per impostare l'intervallo di tempo per le rimanenti voci della pagina.
* **Spouts**: spout usati dalla topologia. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su spout specifici.
* **Bolts**: bolt usati nella topologia. Usare i collegamenti disponibili in questa sezione per visualizzare ulteriori informazioni su bolt specifici.
* **Topology configuration**: configurazione della topologia selezionata.

### <a name="spout-and-bolt-summary"></a>Riepilogo di spout e bolt

Se si seleziona un elemento nella sezione **Spouts** o **Bolts**, verranno visualizzate le informazioni seguenti:

* **Component summary**: informazioni di base sullo spout o sul bolt.
* **Spout/Bolt stats**: statistiche relative allo spout o al bolt. Usare i collegamenti disponibili nella colonna **Finestra** per impostare l'intervallo di tempo per le rimanenti voci della pagina.
* **Input stats** (solo bolt): informazioni sui flussi di input usati dal bolt.
* **Output stats**: informazioni sui flussi generati dallo spout o dal bolt.
* **Executors**: informazioni sulle istanze dello spout o del bolt. Selezionare la voce **Port** relativa a un esecutore specifico per visualizzare il log delle informazioni di diagnostica generate per questa istanza.
* **Errors**: informazioni su eventuali errori dello spout o del bolt.

## <a name="monitor-and-manage-rest-api"></a>Monitorare e gestire: API REST

L'interfaccia utente di Storm si basa sull'API REST. È pertanto possibile eseguire funzionalità di gestione e monitoraggio simili usando l'API REST. L'API REST consente di creare strumenti personalizzati per la gestione e il monitoraggio di topologie Storm.

Per altre informazioni, vedere l'articolo relativo all'[API REST dell'interfaccia utente di Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Le seguenti informazioni sono specifiche per l'uso dell'API REST con Apache Storm in HDInsight.

> [!IMPORTANT]
> L'API REST di Storm non è disponibile pubblicamente su Internet ed è necessario accedervi usando un tunnel SSH al nodo head del cluster HDInsight. Per informazioni sulla creazione e sull'uso di un tunnel SSH, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI di base

L'URI di base per l'API REST nei cluster HDInsight basati su Linux sono disponibili nel nodo head in **https://HEADNODEFQDN:8744/api/v1/**. Il nome di dominio del nodo head viene generato durante la creazione del cluster e non è statico.

È possibile trovare il nome di dominio completo (FQDN) per il nodo head del cluster in diversi modi:

* **Da una sessione SSH**: usare il comando `headnode -f` da una sessione SSH al cluster.
* **Da Ambari Web**: selezionare **Services** (Servizi) nella parte superiore della pagina, quindi selezionare **Storm**. Dalla scheda **Riepilogo** selezionare **Storm UI Server** (Server dell'interfaccia utente di Storm). Il nome FQDN del nodo su cui sono in esecuzione l'interfaccia utente di Storm e le API REST sarà nella parte superiore della pagina.
* **Dall'API REST Ambari**: usare il comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` per recuperare informazioni sul nodo in cui sono in esecuzione l'interfaccia utente di Storm e l'API REST. Sostituire **PASSWORD** con la password di amministratore per il cluster. Sostituire **CLUSTERNAME** con il nome del cluster. Nella risposta, la voce "host_name" contiene il nome di dominio completo del nodo.

### <a name="authentication"></a>Autenticazione

Le richieste all'API REST devono usare l' **autenticazione di base**con il nome e la password amministratore del cluster HDInsight.

> [!NOTE]
> Poiché l'autenticazione di base viene inviata in testo non crittografato, è necessario usare **sempre** HTTPS per proteggere le comunicazioni con il cluster.

### <a name="return-values"></a>Valori restituiti

Le informazioni restituite dall'API REST possono essere usate solo nel cluster o nei computer che si trovano nella stessa rete virtuale di Azure del cluster. Ad esempio, il nome di dominio completo (FQDN) restituito per i server Zookeeper non è accessibile da Internet.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come distribuire e monitorare le topologie usando Storm Dashboard, è possibile passare all'argomento [Sviluppare topologie basate su Java usando Maven](hdinsight-storm-develop-java-topology.md).

Per un elenco di altre topologie di esempio, vedere [Esempi di topologie Storm per Apache Storm in HDInsight](hdinsight-storm-example-topology.md).

