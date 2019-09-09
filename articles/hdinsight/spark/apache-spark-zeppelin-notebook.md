---
title: Usare i notebook di Zeppelin con cluster Apache Spark in Azure HDInsight
description: Istruzioni dettagliate su come usare i notebook di Zeppelin con cluster Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 6801f2b3bca1fbfa221ec2eba07f51b76712b4ff
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813983"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Usare i notebook di Apache Zeppelin con cluster Apache Spark in Azure HDInsight

I cluster HDInsight Spark includono notebook [Apache Zeppelin](https://zeppelin.apache.org/) che possono essere usati per eseguire processi [Apache Spark](https://spark.apache.org/). Questo articolo illustra come usare il notebook Zeppelin in un cluster HDInsight.

**Prerequisiti:**

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Schema URI per l'archiviazione primaria del cluster. Si tratta `abfs://` `adl://` di un archivio BLOB di Azure, per Azure Data Lake storage Gen2 o Azure Data Lake storage Gen1. `wasb://` Se il trasferimento sicuro è abilitato per l'archiviazione BLOB o data Lake storage Gen2, l'URI `wasbs://` è `abfss://`rispettivamente o.  Per ulteriori informazioni, vedere anche [richiedere il trasferimento sicuro in archiviazione di Azure](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Avviare un notebook di Apache Zeppelin

1. Nella **Panoramica**del cluster Spark selezionare **Zeppelin notebook** da **Dashboard cluster**. Immettere le credenziali di amministratore per il cluster.  

   > [!NOTE]  
   > È anche possibile raggiungere il notebook di Zeppelin per il cluster aprendo l'URL seguente nel browser. Sostituire **CLUSTERNAME** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Creare un nuovo notebook. Dal riquadro intestazione passare a **notebook** > **Crea nuova nota**.

    ![Creare un nuovo notebook Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Creare un nuovo notebook Zeppelin")

    Immettere un nome per il notebook, quindi selezionare **Crea nota**.

3. Verificare che l'intestazione del notebook mostri uno stato connesso. indicato da un punto verde nell'angolo superiore destro.

    ![Stato del notebook Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Stato del notebook Zeppelin")

4. Caricare i dati di esempio in una tabella temporanea. Quando si crea un cluster Spark in HDInsight, il file di dati di `hvac.csv`esempio,, viene copiato nell'account di archiviazione `\HdiSamples\SensorSampleData\hvac`associato in.

    Nel paragrafo vuoto creato per impostazione predefinita del nuovo notebook, incollare il frammento di codice riportato di seguito.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

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
    ```

    Premere **MAIUSC+INVIO** oppure fare clic sul pulsante **Play** (Riproduci) in modo che il paragrafo esegua il frammento di codice. Lo stato nell'angolo destro del paragrafo deve passare da PRONTO, IN ATTESA, IN ESECUZIONE, a COMPLETATO. L'output viene visualizzato nella parte inferiore dello stesso paragrafo. Nella schermata è simile al seguente:

    ![Creare una tabella temporanea dai dati non elaborati](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Creare una tabella temporanea dai dati non elaborati")

    È inoltre possibile fornire un titolo a ogni paragrafo. Nell'angolo destro del paragrafo selezionare l'icona **delle impostazioni** (pignone) e quindi selezionare **Mostra titolo**.  

    > [!NOTE]  
    > L'interprete %spark2 non è supportato nei notebook Zeppelin in tutte le versioni di HDInsight e l'interprete %sh non sarà supportato a partire da HDInsight 4.0.

5. È ora possibile eseguire istruzioni Spark SQL sulla `hvac` tabella. Incollare la query seguente in un nuovo paragrafo. La query recupera l'ID dell'edificio e la differenza tra le temperature effettive e quelle di destinazione per ogni edificio in una determinata data. Premere **MAIUSC + INVIO**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    L'istruzione **%sql** all'inizio indica al notebook di usare l'interprete Livy Scala.

6. Selezionare l'icona del **grafico a barre** per modificare la visualizzazione.  **Impostazioni**, che vengono visualizzate dopo aver selezionato il **grafico a barre**, consentono di scegliere le **chiavi**e **i valori**.  Nella schermata riportata di seguito sono illustrate questo output.

    ![Eseguire un'istruzione SQL Spark usando notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Eseguire un'istruzione SQL Spark usando notebook1")

7. È inoltre possibile eseguire istruzioni SQL Spark tramite le variabili nella query. Il frammento di codice successivo Mostra come definire una `Temp`variabile,, nella query con i valori possibili con cui si vuole eseguire una query. Quando si esegue la query per la prima volta, un elenco a tendina viene popolato automaticamente con i valori specificati per la variabile.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Incollare questo frammento di codice in un nuovo paragrafo e premere **MAIUSC+INVIO**. Quindi selezionare **65** dall'elenco a discesa **Temp** . 

8. Selezionare l'icona del **grafico a barre** per modificare la visualizzazione.  Quindi selezionare **Impostazioni** e apportare le modifiche seguenti:

   * **Gruppi**  Aggiungere **targettemp**.  
   * **Valori** 1. Rimuovere la **Data**.  2. Aggiungere **temp_diff**.  3.  Modificare l'aggregatore da **Sum** a **AVG**.  

     Nella schermata riportata di seguito sono illustrate questo output.

     ![Eseguire un'istruzione SQL Spark usando notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Eseguire un'istruzione SQL Spark usando notebook2")

9. Riavviare l'interprete Livy per uscire dall'applicazione. A tale scopo, aprire le impostazioni dell'interprete selezionando il nome dell'utente connesso nell'angolo in alto a destra, quindi selezionare **interprete**.  

    ![Avviare l'interprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Output Hive")

10. Scorrere fino a **Tito**, quindi selezionare **Riavvia**.  Selezionare **OK** al prompt.

    ![Riavviare l'interprete Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Riavviare l'interprete Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Come usare pacchetti esterni con il notebook
È possibile configurare il notebook di Zeppelin nel cluster Apache Spark in HDInsight per l'uso di pacchetti esterni creati dalla community che non sono inclusi nel cluster. Per un elenco completo dei pacchetti disponibili, è possibile eseguire ricerche nel [repository Maven](https://search.maven.org/) . È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Ad esempio, un elenco completo dei pacchetti creati dalla community è disponibile nel sito Web [spark-packages.org](https://spark-packages.org/).

Questo articolo illustra come usare il pacchetto [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con il notebook Jupyter.

1. Aprire le impostazioni dell'interprete. Nell'angolo in alto a destra selezionare il nome utente connesso, quindi selezionare **interprete**.

    ![Avviare l'interprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Output Hive")

2. Scorrere fino a **Livio**, quindi selezionare **modifica**.

    ![Modifica Settings1 dell'interprete](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Modifica Settings1 dell'interprete")

3. Aggiungere una nuova chiave denominata `livy.spark.jars.packages`e impostarne il valore nel formato. `group:id:version` Se si vuole usare il pacchetto [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), è quindi necessario impostare il valore della chiave su `com.databricks:spark-csv_2.10:1.4.0`.

    ![Modifica settings2 dell'interprete](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Modifica settings2 dell'interprete")

    Selezionare **Save (Salva** ) e quindi riavviare l'interprete Livio.

4. il valore della chiave immesso sopra si determina come illustrato di seguito.
   
    a. Individuare un pacchetto nel repository Maven. Per questo articolo è stato usato [Spark-CSV](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Recuperare dal repository i valori per **GroupId**, **ArtifactId** e **Version**.
   
    ![Usare pacchetti esterni con notebook di Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Usare pacchetti esterni con notebook di Jupyter")
   
    c. Concatenare i tre valori, separati da due punti ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Posizione di salvataggio dei notebook Zeppelin
I notebook Zeppelin vengono salvati nei nodi head del cluster. Se si elimina il cluster, verranno quindi eliminati anche i notebook. Se si vogliono mantenere i notebook per usarli successivamente in altri cluster, è necessario esportarli al termine dell'esecuzione dei processi. Per esportare un notebook, selezionare l'icona **Esporta** , come illustrato nell'immagine seguente.

![Scaricare notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Scaricare il notebook")

Il notebook verrà così salvato come file JSON nel percorso di download dell'utente.

## <a name="livy-session-management"></a>Gestione delle sessioni di Livy
Quando si esegue il primo paragrafo di codice nel notebook Zeppelin, nel cluster HDInsight Spark viene creata una nuova sessione di Livy. Tale sessione sarà condivisa da tutti i notebook Zeppelin creati successivamente. Se la sessione di Livy viene terminata per qualche motivo (riavvio del cluster e così via), non sarà possibile eseguire processi dal notebook Zeppelin.

In tal caso, per poter eseguire processi dal notebook Zeppelin è prima necessario seguire questa procedura.  

1. Riavviare l'interprete Livy dal notebook Zeppelin. A tale scopo, aprire le impostazioni dell'interprete selezionando il nome dell'utente connesso nell'angolo in alto a destra, quindi selezionare **interprete**.

    ![Avviare l'interprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Output Hive")

2. Scorrere fino a **Tito**, quindi selezionare **Riavvia**.

    ![Riavviare l'interprete Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Riavviare l'interprete Zeppelin")

3. Eseguire una cella di codice da un notebook Zeppelin esistente. Verrà così creata una nuova sessione di Livy nel cluster HDInsight.

## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Apache Spark con BI: Eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: usare Spark in HDInsight per analizzare la temperatura di un edificio con dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con apprendimento automatico: usare Spark in HDInsight per stimare i risultati di controllo degli alimenti](apache-spark-machine-learning-mllib-ipython.md)
* [Analisi dei log del sito Web con Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Creare un'applicazione autonoma con Scala](apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Apache Spark in Scala](apache-spark-intellij-tool-plugin.md)
* [Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Apache Spark in remoto](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Apache Spark per HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
