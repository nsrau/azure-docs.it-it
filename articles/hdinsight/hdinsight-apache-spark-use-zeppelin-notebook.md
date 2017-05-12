---
title: Usare le azioni script per installare notebook Zeppelin per cluster Spark in Azure HDInsight | Documentazione Microsoft
description: Istruzioni dettagliate su come installare e usare i notebook di Zeppelin con cluster Spark in HDInsight per Linux.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 9e6e0a696ba27c7758c21fa46754a8539ae2255b
ms.contentlocale: it-it
ms.lasthandoff: 03/29/2017


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight"></a>Installare notebook Zeppelin per cluster Apache Spark in HDInsight

Informazioni su come installare i notebook di Zeppelin in cluster Apache Spark e su come utilizzare i notebook di Zeppelin per eseguire i processi Spark.

> [!IMPORTANT]
> Se è stato eseguito il provisioning di un cluster Spark 1.6 in HDInsight 3.5, è possibile accedere ai notebook Zeppelin per impostazione predefinita con le istruzioni illustrate in [Usare notebook Zeppelin con cluster Apache Spark in HDInsight Linux](hdinsight-apache-spark-zeppelin-notebook.md). Se si vuole usare Zeppelin nelle versioni dei cluster HDInsight 3.3 o 3.4, è necessario seguire le istruzioni in questo articolo per installare Zeppelin.
>
> Non è possibile usare gli script in questo articolo per installare Zeppelin in cluster Spark 2.0.

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="install-zeppelin-on-a-spark-cluster"></a>Installare Zeppelin in un cluster Spark
È possibile installare Zeppelin in un cluster Spark tramite l'azione script. L'azione script usa script personalizzati per installare nel cluster i componenti che non sono disponibili per impostazione predefinita. È possibile usare lo script personalizzato per installare Zeppelin dal portale di Azure tramite HDInsight .NET SDK o Azure PowerShell. È possibile utilizzare lo script per installare Zeppelin sia nell’ambito della creazione del cluster che quando il cluster è in esecuzione. I collegamenti nelle sezioni seguenti forniscono le istruzioni.

### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Per istruzioni su come usare il portale di Azure per eseguire azioni di script per installare Zeppelin, leggere l'articolo [Personalizzare cluster HDInsight mediante Azione script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). È necessario apportare alcune modifiche alle istruzioni riportate in questo articolo.

* È necessario usare lo script per installare Zeppelin. Lo script personalizzato per installare Zeppelin in un cluster Spark su HDInsight è disponibile ai collegamenti seguenti.

  * Per i cluster Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Per i cluster Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* È necessario eseguire l'azione script solo sul nodo head.
* Per lo script non sono necessari parametri.

### <a name="using-hdinsight-net-sdk"></a>Uso di HDInsight .NET SDK
Per istruzioni su come usare HDInsight .NET SDK per eseguire azioni di script per installare Zeppelin, leggere l'articolo [Personalizzare cluster HDInsight mediante Azione script](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). È necessario apportare alcune modifiche alle istruzioni riportate in questo articolo.

* È necessario usare lo script per installare Zeppelin. Lo script personalizzato per installare Zeppelin in un cluster Spark su HDInsight è disponibile ai collegamenti seguenti.

  * Per i cluster Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Per i cluster Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* Per lo script non sono necessari parametri.
* Impostare il tipo di cluster che si sta creando per Spark.

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Usare il frammento di codice PowerShell seguente per creare un cluster Spark in HDInsight Linux con Zeppelin installato. A seconda della versione del cluster Spark in uso, è necessario aggiornare il frammento di codice di PowerShell seguente per includere il collegamento allo script personalizzato corrispondente.

* Per i cluster Spark 1.6.0 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Per i cluster Spark 1.5.2 - `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="access-the-zeppelin-notebook"></a>Accedere al notebook di Zeppelin

Dopo aver installato Zeppelin con l'azione script, è possibile usare la procedura seguente per accedere al notebook di Zeppelin nel cluster Spark. In questa sezione esamineremo come eseguire le istruzioni %sql e %hive.

1. Dal Web browser, aprire l'endpoint seguente:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin

   
2. Creare un nuovo notebook. Dal riquadro intestazione fare clic su **Notebook** e quindi su **Create New Note** (Crea una nuova nota).

    ![Creare un nuovo notebook Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Creare un nuovo notebook Zeppelin")

    Nella stessa pagina, sotto l'intestazione **Notebook**, verrà visualizzato un nuovo notebook con il nome che inizia con **Note XXXXXXXXX**. Fare clic su nuovo notebook.
3. Nella pagina web per il nuovo notebook, fare clic sull'intestazione e se si vuole modificare il nome del notebook. Premere INVIO per salvare la modifica del nome. Verificare anche che l'intestazione del notebook mostri lo stato **Connesso** nell'angolo in alto a destra.

    ![Stato del notebook Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Stato del notebook Zeppelin")

### <a name="run-sql-statements"></a>Eseguire le istruzioni SQL
1. Caricare i dati di esempio in una tabella temporanea. Quando si crea un cluster Spark in HDInsight, il file di dati di esempio, **hvac.csv**, viene copiato nell'account di archiviazione associato in **\HdiSamples\SensorSampleData\hvac**.

    Nel paragrafo vuoto creato per impostazione predefinita del nuovo notebook, incollare il frammento di codice riportato di seguito.

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    Premere **MAIUSC+INVIO** oppure fare clic sul pulsante **Play** (Riproduci) in modo che il paragrafo esegua il frammento di codice. Lo stato nell'angolo destro del paragrafo deve passare da PRONTO, IN ATTESA, IN ESECUZIONE, a COMPLETATO. L'output viene visualizzato nella parte inferiore dello stesso paragrafo. Nella schermata è simile al seguente:

    ![Creare una tabella temporanea dai dati non elaborati](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Creare una tabella temporanea dai dati non elaborati")

    È inoltre possibile fornire un titolo a ogni paragrafo. Nell'angolo superiore destro fare clic sull'icona **Settings** (Impostazioni) e quindi su **Show title** (Mostra titolo).
2. È ora possibile eseguire istruzioni SQL Spark su tabella **hvac** . Incollare la query seguente in un nuovo paragrafo. La query recupera l'ID dell'edificio e la differenza tra le temperature effettive e quelle di destinazione per ogni edificio in una determinata data. Premere **MAIUSC + INVIO**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    L'istruzione **%sql** all'inizio indica al notebook di usare l'interprete SQL Spark. È possibile esaminare gli interpreti definiti dalla scheda **Interprete** nell'intestazione del notebook.

    Nella schermata riportata di seguito sono illustrate questo output.

    ![Eseguire un'istruzione SQL Spark usando il notebook](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Eseguire un'istruzione SQL Spark usando il notebook")

     Scegliere le opzioni di visualizzazione (evidenziate nel rettangolo) per passare tra diverse rappresentazioni per lo stesso output. Fare clic su **Impostazioni** per scegliere la chiave e i valori nell'output. La schermata precedente usa **buildingID** come chiave e la media di **temp_diff** come valore.
3. È inoltre possibile eseguire istruzioni SQL Spark tramite le variabili nella query. Il seguente frammento di codice illustra come definire una variabile **Temp**nella query con i valori possibili che si vuole eseguire. Quando si esegue la query per la prima volta, un elenco a tendina viene popolato automaticamente con i valori specificati per la variabile.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    Incollare questo frammento di codice in un nuovo paragrafo e premere **MAIUSC+INVIO**. Nella schermata riportata di seguito sono illustrate questo output.

    ![Eseguire un'istruzione SQL Spark usando il notebook](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Eseguire un'istruzione SQL Spark usando il notebook")

    Per le query successive, è possibile selezionare un nuovo valore dall'elenco a tendina e quindi eseguire nuovamente la query. Fare clic su **Impostazioni** per scegliere la chiave e i valori nell'output. La schermata precedente usa **buildingID** come chiave, la media di **temp_diff** come valore e **targettemp** come gruppo.
4. Riavviare l'interprete Spark SQL per uscire dall'applicazione. Scegliere la scheda **Interpreter** (Interprete) nella parte superiore e per l'interprete Spark fare clic su **Restart** (Riavvia).

    ![Riavviare l'interprete Zeppelin](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Riavviare l'interprete Zeppelin")

### <a name="run-hive-statements"></a>Eseguire le istruzioni hive
1. Dal notebook Zeppelin fare clic sul pulsante **Interpreter** .

    ![Aggiornare l'interprete Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Aggiornare l'interprete Hive")
2. Per l'interprete **hive** fare clic su **edit** (modifica).

    ![Aggiornare l'interprete Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Aggiornare l'interprete Hive")

    Aggiornare le seguenti proprietà.

   * Impostare **default.password** sulla password specificata per l'utente amministratore durante la creazione del cluster HDInsight Spark.
   * Impostare **default.url** su `jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`. Sostituire **\<spark_cluster_name>** con il nome del cluster Spark.
   * Impostare **default.user** sul nome dell'utente amministratore specificato durante la creazione del cluster. Ad esempio, *admin*.
3. Fare clic su **Save** (Salva) quando viene richiesto di riavviare l'interprete hive e quindi su **OK**.
4. Creare un nuovo notebook ed eseguire l'istruzione seguente per elencare tutte le tabelle hive nel cluster.

        %hive
        SHOW TABLES

    Per impostazione predefinita, un cluster HDInsight contiene una tabella di esempio denominata **hivesampletable** e quindi si dovrebbe ottenere il seguente output.

    ![Output di Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Output di Hive")
5. Eseguire l'istruzione seguente per elencare i record nella tabella.

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    Verrà visualizzato un output simile al seguente.

    ![Output di Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Output di Hive")

## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Utilizzare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

