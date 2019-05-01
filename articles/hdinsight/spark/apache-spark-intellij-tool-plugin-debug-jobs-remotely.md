---
title: 'Azure Toolkit for IntelliJ: debug delle applicazioni in modalità remota su HDInsight Spark '
description: Informazioni su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni Spark in esecuzione nei cluster HDInsight tramite VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 30d52f1ac6a68a3202de59a0b4cab8edfb7ed042
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701088"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Usare Azure Toolkit for IntelliJ per eseguire il debug remoto di applicazioni Apache Spark in HDInsight tramite VPN

È consigliabile usare la modalità di debug dell'applicazione [Apache Spark](https://spark.apache.org/) da remoto tramite SSH. Per istruzioni vedere [Eseguire il debug remoto delle applicazioni Apache Spark su un cluster HDInsight con Azure Toolkit per IntelliJ tramite SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Questo articolo offre indicazioni dettagliate su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per inviare un processo Spark nel cluster HDInsight Spark e quindi eseguirne il debug remoto dal computer desktop. Per completare queste attività, è necessario seguire la procedura di alto livello seguente:

1. Creare una rete virtuale di Azure da sito a sito o da punto a sito. Le procedure descritte in questo documento presuppongono che si usi una rete da sito a sito.
1. Creare un cluster Spark in HDInsight incluso nella rete virtuale da sito a sito.
1. Verificare la connettività tra il nodo head del cluster e il PC desktop.
1. Creare un'applicazione Scala in IntelliJ IDEA e configurarla per il debug remoto.
1. Eseguire l'applicazione ed effettuarne il debug.

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Per altre informazioni, vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un cluster Apache Spark in HDInsight**. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Kit di sviluppo di Oracle Java**. È possibile installarlo dal [sito Web di Oracle](https://aka.ms/azure-jdks).
* **IntelliJ IDEA**. In questo articolo viene usata la versione 2017.1. È possibile installarlo dal [sito Web di JetBrains](https://www.jetbrains.com/idea/download/).
* **Strumenti HDInsight in Azure Toolkit per IntelliJ**. Gli strumenti HDInsight per IntelliJ sono disponibili come parte di Azure Toolkit for IntelliJ. Per istruzioni su come installare Azure Toolkit, vedere [Installare Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Accedere alla propria sottoscrizione di Azure da IntelliJ IDEA**. Seguire le istruzioni in [Usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight](apache-spark-intellij-tool-plugin.md).
* **Soluzione alternativa di eccezione**. Quando si esegue l'applicazione Spark Scala per il debug remoto in un computer Windows, potrebbe essere restituita un'eccezione. Questa eccezione è illustrata in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) e si verifica a causa di un file WinUtils.exe mancante in Windows. Per risolvere questo errore, è necessario [scaricare il file eseguibile](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) in un percorso come **C:\WinUtils\bin**. Aggiungere quindi una variabile di ambiente **HADOOP_HOME** e impostare il valore della variabile su **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Passaggio 1: Creare una rete virtuale di Azure
Seguire le istruzioni riportate nei collegamenti seguenti per creare una rete virtuale di Azure e quindi verificare la connettività tra il PC desktop e la rete virtuale:

* [Creare una rete virtuale con una connessione VPN da sito a sito tramite il portale di Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Creare una rete virtuale con una connessione VPN da sito a sito usando PowerShell e Azure Resource Manager](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configurare una connessione da punto a sito a una rete virtuale con PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Passaggio 2: Creare un cluster HDInsight Spark
È consigliabile creare anche un cluster Apache Spark in Azure HDInsight incluso nella rete virtuale di Azure creata. Usare le informazioni disponibili in [Creare cluster basati su Linux in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Nell'ambito della configurazione facoltativa, selezionare la rete virtuale di Azure creata nel passaggio precedente.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Passaggio 3: Verificare la connettività tra il nodo head del cluster e il PC desktop
1. Ottenere l'indirizzo IP del nodo head. Aprire l'interfaccia utente di Ambari per il cluster. Dal pannello del cluster selezionare **Dashboard**.

    ![Selezionare Dashboard in Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
1. Nell'interfaccia utente di Ambari selezionare **Hosts** (Host).

    ![Selezionare Hosts (Host) in Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
1. Viene visualizzato un elenco di nodi head, nodi del ruolo di lavoro e nodi zookeeper. I nodi head hanno un prefisso **hn**\*. Selezionare il primo nodo head.

    ![Trovare il nodo head in Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
1. Nella parte inferiore della pagina visualizzata, nel riquadro **Summary** (Riepilogo) copiare l'**indirizzo IP** del nodo head e il **nome host**.

    ![Trovare l'indirizzo IP in Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
1. Aggiungere l'indirizzo IP e il nome host del nodo head nel file **host** nel computer in cui si vuole eseguire il processo Spark ed effettuarne il debug remoto. Sarà così possibile comunicare con il nodo head usando sia l'indirizzo IP che il nome host.

   a. Aprire un file del Blocco note con autorizzazioni elevate. Scegliere **Apri** dal menu **File** e quindi passare al percorso del file host. In un computer Windows il percorso è **C:\Windows\System32\Driver\etc\host**.

   b. Aggiungere al file **host** le informazioni seguenti:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
1. Nel computer che è stato connesso alla rete virtuale di Azure usata dal cluster HDInsight verificare che sia possibile effettuare il ping dei nodi head usando sia l'indirizzo IP che il nome host.
1. Usare SSH per connettersi al nodo head del cluster seguendo le istruzioni riportate in [Connettersi a un cluster HDInsight con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Dal nodo head del cluster effettuare il ping dell'indirizzo IP del computer desktop. Testare la connettività per entrambi gli indirizzi IP assegnati al computer:

    - uno per la connessione di rete
    - uno per la rete virtuale di Azure

1. Ripetere questi passaggi anche per l'altro nodo head.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Passaggio 4: Creare un'applicazione Apache Spark Scala usando gli strumenti HDInsight in Azure Toolkit for IntelliJ e configurarla per eseguire il debug remoto
1. Aprire IntelliJ IDEA e creare un nuovo progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) seguire questa procedura:

    ![Selezionare il modello del nuovo progetto in IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Selezionare **HDInsight** > **Spark on HDInsight (Scala)** (Spark in HDInsight - Scala).

    b. Selezionare **Avanti**.
1. Nella finestra di dialogo **New Project** (Nuovo progetto) successiva seguire questa procedura e quindi selezionare **Finish** (Fine):

    - Immettere un nome e un percorso per il progetto.

    - Nell'elenco a discesa **Project SDK** (SDK progetto) selezionare **Java 1.8** per il cluster Spark 2.x oppure **Java 1.7** per il cluster Spark 1.x.

    - Nell'elenco a discesa **Spark version** (Versione di Spark) la creazione guidata del progetto Scala inserisce la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo esempio viene usata la versione **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Selezionare l'SDK del progetto e la versione di Spark](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Il progetto Spark crea automaticamente un elemento. Per visualizzare l'elemento, seguire questa procedura:

    a. Dal menu **File** selezionare **Project Structure** (Struttura progetto).

    b. Nella finestra di dialogo **Project Structure** (Struttura progetto) selezionare **Artifacts** (Elementi) per visualizzare l'elemento predefinito creato. È anche possibile creare un elemento personalizzato selezionando il segno più (**+**).

   ![Creazione di un file con estensione jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


1. Aggiungere librerie al progetto. Per aggiungere una raccolta, eseguire le operazioni seguenti:

    a. Fare clic con il pulsante destro del mouse sul nome del progetto nell'albero del progetto e quindi scegliere **Open Module Settings**(Apri impostazioni modulo). 

    b. Nella finestra di dialogo **Project Structure** (Struttura progetto) selezionare **Libraries** (Librerie), scegliere il simbolo (**+**) e quindi **From Maven** (Da Maven).

    ![Aggiungere una libreria](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. Nella finestra di dialogo **Download Library from Maven Repository** (Scarica libreria da repository Maven) cercare e aggiungere le librerie seguenti:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
1. Copiare `yarn-site.xml` e `core-site.xml` dal nodo head del cluster e aggiungerli al progetto. Per copiare i file, usare i comandi riportati di seguito. Per copiare i file dai nodi head del cluster è possibile usare [Cygwin](https://cygwin.com/install.html) per eseguire i comandi `scp` seguenti:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Poiché gli indirizzi IP e i nomi host dei nodi head del cluster sono già stati aggiunti al file host nel PC desktop, si possono usare i comandi `scp` nel modo seguente:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Aggiungere questi file al progetto copiandoli nella cartella **/src** dell'albero del progetto, ad esempio `<your project directory>\src`.
1. Aggiornare il file `core-site.xml` per apportare le modifiche seguenti:

   a. Sostituire la chiave crittografata. Il file `core-site.xml` include la chiave crittografata per l'account di archiviazione associato al cluster. Nel file `core-site.xml` aggiunto al progetto sostituire la chiave crittografata con la chiave di archiviazione effettiva associata all'account di archiviazione predefinito. Per altre informazioni, vedere [Gestire le chiavi di accesso alle risorse di archiviazione](../../storage/common/storage-account-manage.md#access-keys).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Rimuovere le voci seguenti da `core-site.xml`:

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   c. Salvare il file.
1. Aggiungere la classe principale per l'applicazione. In **Project Explorer** (Esplora progetti) fare clic con il pulsante destro del mouse su **src**, puntare a **New** (Nuovo) e quindi selezionare **Scala class** (Classe Scala).

    ![Selezionare la classe principale](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
1. Nella finestra di dialogo **Create New Scala Class** (Crea nuova classe Scala) immettere un nome, selezionare **Object** (Oggetto) per il campo **Kind** (Tipologia) e quindi selezionare **OK**.

    ![Crea nuova classe Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
1. Incollare il codice seguente nel file `MyClusterAppMain.scala` . Questo codice crea il contesto Spark e apre un metodo `executeJob` dall'oggetto `SparkSample`.

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasb:///HVACOut")
          }
        }

1. Ripetere i passaggi 8 e 9 per aggiungere un nuovo oggetto Scala denominato `*SparkSample`. Aggiungere il codice seguente a questa classe. Questo codice legge i dati da HVAC.csv disponibile in tutti i cluster HDInsight Spark. Recupera le righe con una sola cifra nella settima colonna del file CSV e scrive l'output in **/HVACOut** nel contenitore di archiviazione predefinito per il cluster.

        import org.apache.spark.SparkContext

        object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
        }
1. Ripetere i passaggi 8 e 9 per aggiungere una nuova classe denominata `RemoteClusterDebugging`. Questa classe implementa il framework di test Spark usato per il debug delle applicazioni. Aggiungere il codice seguente alla classe `RemoteClusterDebugging`:

        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }

     Alcuni aspetti importanti da notare:

      * Per `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, verificare che il file JAR dell'assembly Spark sia disponibile nell'archiviazione cluster al percorso specificato.
      * Per `setJars`, specificare il percorso in cui verrà creato il file JAR dell'elemento. In genere è `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
1. Nella classe `*RemoteClusterDebugging` fare clic con il pulsante destro del mouse sulla parola chiave `test` e scegliere **Create RemoteClusterDebugging Configuration** (Crea configurazione RemoteClusterDebugging).

    ![Creare la configurazione remota](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. Nella finestra di dialogo **Create RemoteClusterDebugging Configuration** (Crea configurazione RemoteClusterDebugging), specificare un nome per la configurazione e quindi selezionare **Test kind** (Tipo di test) come **nome del test**. Lasciare tutte le altre impostazioni predefinite. Selezionare **Apply** (Applica) e quindi **OK**.

    ![Aggiungere i dettagli di configurazione](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
1. Nella barra dei menu verrà ora visualizzato un elenco a discesa della configurazione **Remote Run** (Esecuzione remota).

    ![Elenco a discesa Remote run (Esecuzione remota)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Passaggio 5: Eseguire l'applicazione in modalità di debug
1. Nel progetto IntelliJ IDEA aprire `SparkSample.scala` e creare un punto di interruzione accanto a `val rdd1`. Nel menu a comparsa **Create Breakpoint for** (Crea punto di interruzione) selezionare **line in function executeJob** (riga in funzione executeJob).

    ![Aggiunta di un punto di interruzione](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
1. Per eseguire l'applicazione selezionare il pulsante **Debug Run** (Esecuzione debug) accanto all'elenco a discesa della configurazione **Remote Run** (Esecuzione remota).

    ![Selezionare il pulsante Debug Run (Esecuzione debug)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
1. Quando l'esecuzione del programma raggiunge il punto di interruzione, nel riquadro inferiore verrà visualizzata una scheda **Debugger**.

    ![Visualizzare la scheda Debugger](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
1. Per aggiungere un'espressione di controllo, selezionare l'icona (**+**).

    ![Selezionare l'icona +](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    In questo esempio, l'applicazione si è interrotta prima che la variabile `rdd1` fosse creata. Tramite questa espressione di controllo, possiamo vedere le prime cinque righe nella variabile `rdd`. Selezionare **Enter** (Invio).

    ![Esecuzione del programma in modalità di debug](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    L'immagine precedente mostra che in fase di esecuzione è possibile eseguire query su terabyte di dati e il debug dell'avanzamento dell'applicazione. Nell'output illustrato nell'immagine precedente, ad esempio, si può osservare che la prima riga dell'output è un'intestazione. Sulla base di questo output, è possibile modificare il codice dell'applicazione per ignorare la riga di intestazione, se necessario.
1. È ora possibile fare clic sull'icona **Resume Program** (Riprendi programma) per continuare l'esecuzione dell'applicazione.

    ![Selezionare Resume Program (Riprendi programma)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
1. Se l'applicazione viene completata correttamente, verrà visualizzato un output simile al seguente:

    ![Output console](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Passaggi successivi
* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Creare il progetto Scala (video): [Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ) (Creare applicazioni Apache Spark in Scala)
* Debug remoto (video): [Use Azure Toolkit for IntelliJ to debug Apache Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark nel cluster HDInsight)

### <a name="scenarios"></a>Scenari
* [Apache Spark con BI: eseguire l’analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con apprendimento automatico: usare Spark in HDInsight per analizzare la temperatura di un edificio con dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con apprendimento automatico: usare Spark in HDInsight per stimare i risultati di controllo degli alimenti](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare Azure Toolkit for IntelliJ per creare applicazioni Apache Spark per un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Usare Azure Toolkit for IntelliJ per il debug remoto di applicazioni Apache Spark tramite SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Usare i notebook di Apache Zeppelin con un cluster Apache Spark in HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter in un cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
