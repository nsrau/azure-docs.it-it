---
title: Distribuire e gestire le topologie Apache Storm su Azure HDInsight
description: Informazioni su come distribuire, monitorare e gestire Apache Storm topologie con Storm dashboard in HDInsight basato su Linux. Utilizzare gli strumenti Hadoop per Visual Studio
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 82c5db4f75f131ebdc2434955108e7d50237d9ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228882"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Distribuire e gestire le topologie Apache Storm su Azure HDInsight 

In questo documento sono illustrati i concetti di gestione e monitoraggio delle topologie [Apache Storm](https://storm.apache.org/) in esecuzione su Storm in cluster HDInsight.

## <a name="prerequisites"></a>prerequisiti

* Un cluster Apache Storm in HDInsight. Consultare [Creare cluster di Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selezionare **Storm** per **Tipo di cluster**.

* Opzionale Familiarità con Secure Shell (SSH) e Secure Copy (SCP). Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Opzionale Visual Studio, Azure SDK 2.5.1 o versione successiva e gli strumenti Data Lake per Visual Studio. Per altre informazioni, vedere [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Inviare una topologia con Visual Studio

È possibile usare gli strumenti di Data Lake per Visual Studio per C# inviare o topologie ibride al cluster Storm. Nei seguenti passaggi viene usata un'applicazione di esempio. Per informazioni sulla creazione di topologie mediante gli strumenti di Data Lake, vedere [Apache Storm topologie con C#Visual Studio e ](apache-storm-develop-csharp-visual-studio-topology.md).

1. Se non è già stata installata la versione più recente di Data Lake Tools per Visual Studio, vedere [usare Data Lake Tools per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Data Lake Tools per Visual Studio veniva precedentemente chiamato HDInsight Tools per Visual Studio.
    >
    > Data Lake Tools per Visual Studio sono inclusi nel **carico di lavoro di Azure** per visual studio 2019.

2. Aprire Visual Studio.

3. Nella finestra **Start** selezionare **Crea un nuovo progetto**.

4. Nella finestra **Crea un nuovo progetto** selezionare la casella di ricerca e immettere *Storm*. Quindi scegliere **Storm Sample** dall'elenco dei risultati e fare clic su **Next (avanti**).

5. Nella finestra **Configura nuovo progetto** immettere un **nome di progetto**e passare a o creare un **percorso** in cui salvare il nuovo progetto. Selezionare quindi **Crea**.

    ![Configurare la finestra del nuovo progetto, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

6. In **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Invia a Storm in HDInsight**.

    > [!NOTE]  
    > Se richiesto, immettere le credenziali di accesso per la sottoscrizione di Azure. Se si dispone di più di una sottoscrizione, accedere a quella che contiene il cluster Storm in HDInsight.

7. Nella finestra di dialogo **Invia topologia** , nell'elenco a discesa **cluster Storm** , scegliere il cluster Storm in HDInsight e quindi selezionare **Invia**. È possibile verificare se l'invio ha esito positivo visualizzando il riquadro di **output** .

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Inviare una topologia usando SSH e il comando Storm

Per inviare una topologia a Storm usando SSH:

1. Connettersi al cluster HDInsight usando SSH. Sostituire `USERNAME` con il nome del nome utente SSH (ad esempio *sshuser*). Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.

    ```shell
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Per altre informazioni sull'uso di SSH per connettersi al cluster HDInsight, vedere [connettersi a HDInsight (Apache Hadoop) tramite SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare il comando seguente per avviare la topologia di esempio *WordCount* :

    ```ssh
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Questo comando consente di avviare la topologia di esempio WordCount nel cluster. Questa topologia genera in modo casuale le frasi e quindi conta le occorrenze di ogni parola nelle frasi.

    > [!NOTE]  
    > Quando si invia la topologia al cluster, è prima necessario copiare il file con estensione jar contenente il cluster prima di usare il comando `storm`. Usare il comando `scp` per copiare il file nel cluster. Ad esempio, immettere `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > L'esempio *WordCount* e altri esempi di Storm Starter sono già inclusi nel cluster in `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Inviare una topologia a livello di codice

È possibile distribuire a livello di codice una topologia usando il servizio Nimbus. In [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) è disponibile un'applicazione Java di esempio che illustra come distribuire e avviare una topologia tramite il servizio Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitorare e gestire una topologia in Visual Studio

Quando si invia una topologia con Visual Studio, viene visualizzata la finestra **visualizzazione topologie Storm** . Per visualizzare informazioni sulla topologia in esecuzione, selezionarla dall'elenco.

![Topologia di monitoraggio, finestra visualizzazione topologie Storm, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> È inoltre possibile visualizzare le **topologie Storm**  da **Esplora Server**. Espandere **Azure** > **HDInsight**, fare clic con il pulsante destro del mouse sul cluster Storm in HDInsight e quindi selezionare **Visualizza topologie Storm**.

Selezionare la forma degli spout o dei bolt per visualizzare informazioni su questi componenti. Viene visualizzata una descrizione comando con informazioni sul componente per l'elemento selezionato.

### <a name="deactivate-and-reactivate-a-topology"></a>Disattivare e riattivare una topologia

La disattivazione di una topologia lo sospende fino a quando la topologia non viene terminata o riattivata. Per eseguire queste operazioni, usare i pulsanti Disattiva e **riattiva** **nell'area** **azioni** nella parte superiore della finestra **visualizzazione topologie Storm** .

### <a name="rebalance-a-topology"></a>Ribilanciare una topologia

Il ribilanciamento di una topologia consente al sistema di analizzare il parallelismo della topologia. Se, ad esempio, il cluster è stato ridimensionato per aggiungere altre note, il ribilanciamento consente a una topologia di visualizzare i nuovi nodi.

Per ribilanciare una topologia, usare il pulsante **ribilancia** nell'area **azioni** della finestra **visualizzazione topologie Storm** .

> [!WARNING]  
> Il ribilanciamento di una topologia disattiva la topologia, ridistribuisce i ruoli di lavoro in modo uniforme nel cluster e quindi restituisce la topologia allo stato in cui si trovava prima del ribilanciamento. Se la topologia era attiva, diventerà nuovamente attiva. Se la topologia è stata disattivata, rimarrà disattivata.

### <a name="kill-a-running-topology"></a>Terminare una topologia in esecuzione

Le topologie Storm continuano a essere eseguite fino a quando non vengono arrestate o il cluster non viene eliminato. Per arrestare una topologia, usare il pulsante **Kill** nell'area **Actions** .

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitorare e gestire una topologia usando SSH e il comando Storm

L'utilità `storm` consente di usare le topologie in esecuzione dalla riga di comando. Per visualizzare l'elenco completo dei comandi, usare `storm -h` .

### <a name="list-topologies"></a>Visualizzare l'elenco delle topologie

Usare il comando seguente per ottenere un elenco delle topologie in esecuzione:

```shell
storm list
```

Questo comando restituisce informazioni simili al testo seguente:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Disattivare e riattivare una topologia

La disattivazione di una topologia lo sospende fino a quando la topologia non viene terminata o riattivata. Usare i comandi seguenti per disattivare o riattivare:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Terminare una topologia in esecuzione

Una volta avviate, le topologie Storm continueranno a rimanere in esecuzione finché non vengono arrestate. Per arrestare una topologia, usare il comando seguente:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Ribilanciare una topologia

Il ribilanciamento di una topologia consente al sistema di analizzare il parallelismo della topologia. Se, ad esempio, il cluster è stato ridimensionato per aggiungere altre note, il ribilanciamento consente a una topologia di visualizzare i nuovi nodi.

> [!WARNING]  
> Il ribilanciamento di una topologia disattiva la topologia, ridistribuisce i ruoli di lavoro in modo uniforme nel cluster e quindi restituisce la topologia allo stato in cui si trovava prima del ribilanciamento. Se la topologia era attiva, diventerà nuovamente attiva. Se invece era disattivata, rimarrà disattivata.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitorare e gestire una topologia usando l'interfaccia utente di Storm

L'interfaccia utente di Storm fornisce un'interfaccia Web per lavorare con le topologie in esecuzione ed è inclusa nel cluster HDInsight. Per visualizzare l'interfaccia utente di Storm, usare un Web browser per aprire `https://CLUSTERNAME.azurehdinsight.net/stormui`, dove *clustername* è il nome del cluster.

> [!NOTE]  
> Se viene richiesto di specificare un nome utente e una password, immettere il nome utente e la password dell'amministratore del cluster usati durante la creazione del cluster.

### <a name="storm-ui-main-page"></a>Pagina principale dell'interfaccia utente di Storm

Nella pagina principale dell'interfaccia utente di Storm sono disponibili le informazioni seguenti.

| Sezione | DESCRIZIONE |
| --- | --- |
| **Riepilogo cluster** | informazioni di base sul cluster Storm. |
| **Riepilogo di Nimbus** | Elenco di informazioni Nimbus di base. |
| **Riepilogo topologia** | elenco delle topologie in esecuzione. Per visualizzare ulteriori informazioni su una topologia specifica, selezionare il relativo collegamento nella colonna **nome** . |
| **Riepilogo supervisore** | informazioni su Storm Supervisor. Per visualizzare le risorse di lavoro associate a uno specifico supervisore, selezionare il relativo collegamento nella colonna **host** o **ID** . |
| **Configurazione Nimbus** | configurazione Nimbus per il cluster. |

La pagina principale dell'interfaccia utente di Storm ha un aspetto simile a questa pagina Web:

![Pagina principale, interfaccia utente di Storm, topologie di Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Topology summary

Se si seleziona un collegamento nella sezione **Topology summary** , verranno visualizzate le informazioni seguenti sulla topologia.

| Sezione | DESCRIZIONE |
| --- | --- |
| **Riepilogo topologia** | informazioni di base sulla topologia. |
| **Azioni della topologia** | Azioni di gestione che è possibile eseguire per la topologia. Le azioni disponibili sono descritte più avanti in questa sezione. |
| **Statistiche topologia** | statistiche sulla topologia. Per impostare l'intervallo di tempo per una voce in questa sezione, selezionare il relativo collegamento nella colonna **finestra** . |
| **Beccucci** *(intervallo di tempo)* | spout usati dalla topologia. Per visualizzare altre informazioni su uno specifico beccuccio, selezionarne il collegamento nella colonna **ID** . |
| **Bolt** *(intervallo di tempo)* | bolt usati nella topologia. Per visualizzare altre informazioni su un Bolt specifico, selezionare il relativo collegamento nella colonna **ID** . |
| **Risorse di lavoro** | Elenco di risorse del ruolo di lavoro. Per visualizzare ulteriori informazioni su una risorsa di lavoro specifica, selezionare il relativo collegamento nella colonna **host** . |
| **Visualizzazione topologia** | Pulsante **Mostra visualizzazione** che consente di visualizzare una visualizzazione della topologia. |
| **Configurazione topologia** | configurazione della topologia selezionata. |

La pagina di riepilogo della topologia Storm è simile a questa pagina Web:

![Pagina di riepilogo della topologia, interfaccia utente di Storm, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

Nella sezione **azioni topologia** è possibile selezionare i pulsanti seguenti per eseguire un'azione:

| Pulsante | DESCRIZIONE |
| --- | --- |
| **Attiva** | riprende l'elaborazione di una topologia disattivata. |
| **Disattivare** | sospende una topologia in esecuzione. |
| **Ribilanciare** | regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi nel cluster. Questa operazione consente alla topologia di regolare il parallelismo per compensare il numero aggiuntivo o ridotto dei nodi nel cluster.<br/><br/>Per altre informazioni, vedere <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of an Apache Storm topology</a> (Informazioni sul parallelismo di una topologia Apache Storm).
| **Kill** | arresta una topologia Storm dopo il timeout specificato. |
| **Eseguire il debug** | Avvia una sessione di debug per la topologia in esecuzione. |
| **Arresta debug** | Termina la sessione di debug per la topologia in esecuzione. |
| **Modificare il livello di registrazione** | Modifica il livello del log di debug. |

##### <a name="spout-and-bolt-summary"></a>Riepilogo beccuccio e Bolt

Se si seleziona un elemento nella sezione **Spouts** o **Bolts**, verranno visualizzate le informazioni seguenti:

| Sezione | DESCRIZIONE |
| --- | --- |
| **Riepilogo componenti** | informazioni di base sullo spout o sul bolt. |
| **Azioni componente** | Pulsanti **debug e** **Interrompi debug** . |
| Statistiche di **beccuccio** o **statistiche Bolt** | statistiche relative allo spout o al bolt. Per impostare l'intervallo di tempo per una voce in questa sezione, selezionare il relativo collegamento nella colonna **finestra** . |
| (Solo Bolt)<br/>**Statistiche di input** *(intervallo di tempo)* | informazioni sui flussi di input usati dal bolt. |
| **Statistiche di output** *(intervallo di tempo)* | informazioni sui flussi generati dallo spout o dal bolt. |
| **Profilatura e debug** | Controlli per la profilatura e il debug dei componenti in questa pagina. È possibile impostare il valore **stato/timeout (minuti)** ed è possibile selezionare i pulsanti per **JStack**, **riavvio**del ruolo di lavoro e **heap**. |
| **Esecutori** *(intervallo di tempo)* | informazioni sulle istanze dello spout o del bolt. Per visualizzare un log delle informazioni di diagnostica generate per questa istanza, selezionare la voce della **porta** per un esecutore specifico. È anche possibile visualizzare le risorse di lavoro associate a un esecutore specifico selezionando il relativo collegamento nella colonna **host** . |
| **Errori** | informazioni su eventuali errori dello spout o del bolt. |

La pagina di riepilogo di Storm Bolt è simile a questa pagina Web:

![Pagina Riepilogo Bolt, interfaccia utente di Storm, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitorare e gestire la topologia tramite l'API REST

L'interfaccia utente di Storm è basata sull'API REST, quindi è possibile eseguire attività di gestione e monitoraggio simili usando l'API REST. L'API REST consente di creare strumenti personalizzati per la gestione e il monitoraggio di topologie Storm.

Per altre informazioni, vedere l'articolo relativo all'[API REST dell'interfaccia utente di Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Le seguenti informazioni sono specifiche per l'uso dell'API REST con Apache Storm in HDInsight.

> [!IMPORTANT]  
> L'API REST Storm non è disponibile pubblicamente su Internet. È necessario accedervi tramite un tunnel SSH al nodo head del cluster HDInsight. Per informazioni sulla creazione e sull'uso di un tunnel SSH, vedere [usare il tunneling SSH per accedere ad Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI di base

L'URI di base per l'API REST nei cluster HDInsight basati su Linux è disponibile all'indirizzo URL `https://HEADNODEFQDN:8744/api/v1/`, dove si sostituisce *HEADNODEFQDN* con il nodo head. Il nome di dominio del nodo head viene generato durante la creazione del cluster e non è statico.

È possibile trovare il nome di dominio completo (FQDN) per il nodo head del cluster in diversi modi:

| FQDN (metodo di individuazione) | DESCRIZIONE |
| --- | --- |
| Sessione SSH | usare il comando `headnode -f` da una sessione SSH al cluster. |
| Ambari Web | Nella pagina Web del cluster Ambari (`https://CLUSTERNAME.azurehdinsight.net`) selezionare **Servizi** nella parte superiore della pagina, quindi selezionare **Storm**. Dalla scheda **Riepilogo** selezionare **Storm UI Server** (Server dell'interfaccia utente di Storm). Il nome FQDN del nodo che ospita l'API REST e l'interfaccia utente di Storm è visualizzato nella parte superiore della pagina. |
| API REST Ambari | usare il comando `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` per recuperare informazioni sul nodo in cui sono in esecuzione l'interfaccia utente di Storm e l'API REST. Sostituire le due istanze di *clustername* con il nome del cluster. Quando richiesto, immettere la password per l'account utente (amministratore). Nella risposta, la voce "host_name" dell'output JSON contiene il nome di dominio completo (FQDN) del nodo. |

### <a name="authentication"></a>Autenticazione

Le richieste all'API REST devono usare l' *autenticazione di base*, quindi è necessario usare il nome e la password dell'amministratore per il cluster HDInsight.

> [!NOTE]  
> Poiché l'autenticazione di base viene inviata in testo non crittografato, è necessario usare *sempre* HTTPS per proteggere le comunicazioni con il cluster.

### <a name="return-values"></a>Valori restituiti

Le informazioni restituite dall'API REST possono essere usate solo nel cluster. Ad esempio, il nome di dominio completo (FQDN) restituito per i server [Apache ZooKeeper](https://zookeeper.apache.org/) non è accessibile da Internet.

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni su come [sviluppare topologie basate su Java con Apache Maven](apache-storm-develop-java-topology.md).

Per un elenco di altre topologie di esempio, vedere [esempio Apache Storm topologie in Azure HDInsight](apache-storm-example-topology.md).
