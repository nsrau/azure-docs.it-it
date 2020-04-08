---
title: Blocchi appunti di .eppelin & cluster Apache Spark - Azure HDInsight
description: Istruzioni dettagliate su come usare i notebook di Zeppelin con cluster Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 3c1369e813ba6518f6cd4b27082020ae36a24c82
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811197"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Usare i notebook di Apache Zeppelin con cluster Apache Spark in Azure HDInsight

I cluster HDInsight Spark includono notebook [Apache zeppelin.](https://zeppelin.apache.org/) Usare i blocchi appunti per eseguire i processi [Apache Spark.](https://spark.apache.org/) Questo articolo illustra come usare il notebook Zeppelin in un cluster HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Lo schema URI per l'archiviazione primaria dei cluster. Lo schema `wasb://` è per Archiviazione `abfs://` BLOB di Azure, `adl://` per Azure Data Lake Storage Gen2 o per Azure Data Lake Storage Gen1. Se il trasferimento sicuro è abilitato per `wasbs://`l'archiviazione BLOB, l'URI sarà .  Per altre informazioni, vedere Richiedere il trasferimento sicuro in Archiviazione di Azure.For more information, see [Require secure transfer in Azure Storage.](../../storage/common/storage-require-secure-transfer.md)

## <a name="launch-an-apache-zeppelin-notebook"></a>Avviare un notebook di Apache Zeppelin

1. Dal cluster Spark Panoramica , selezionare **Il blocco appunti di ** da Dashboard **cluster.** **Overview** Immettere le credenziali di amministratore per il cluster.  

   > [!NOTE]  
   > È anche possibile raggiungere il notebook di Zeppelin per il cluster aprendo l'URL seguente nel browser. **Sostituire CLUSTERNAME** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Creare un nuovo notebook. Nel riquadro dell'intestazione passare a **Blocco appunti** > **Crea nuova nota**.

    ![Creare un nuovo notebook Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Creare un nuovo notebook Zeppelin")

    Immettere un nome per il blocco appunti, quindi selezionare **Crea nota**.

3. Verificare che l'intestazione del blocco appunti mostri uno stato connesso. È indicato da un punto verde nell'angolo in alto a destra.

    ![Stato di notebook Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Stato di notebook Zeppelin")

4. Caricare i dati di esempio in una tabella temporanea. Quando si crea un cluster Spark in HDInsight, il file di dati di esempio, `hvac.csv`, viene copiato nell'account di archiviazione associato in `\HdiSamples\SensorSampleData\hvac`.

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

    Premere **MAIUSC e INVIO** o selezionare il pulsante **Riproduci** per il paragrafo per eseguire il frammento di codice. Lo stato nell'angolo destro del paragrafo deve passare da PRONTO, IN ATTESA, IN ESECUZIONE, a COMPLETATO. L'output viene visualizzato nella parte inferiore dello stesso paragrafo. La schermata è simile all'immagine seguente:The screenshot looks like the following image:

    ![Crea una tabella temporanea dai dati non elaborati](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Crea una tabella temporanea dai dati non elaborati")

    È inoltre possibile fornire un titolo a ogni paragrafo. Nell'angolo destro del paragrafo, selezionate l'icona **Impostazioni** (sprocket), quindi selezionate **Mostra titolo**.  

    > [!NOTE]  
    > L'interprete %spark2 non è supportato nei notebook Zeppelin in tutte le versioni di HDInsight e l'interprete %sh non sarà supportato a partire da HDInsight 4.0.

5. È ora possibile eseguire istruzioni Spark SQL nella `hvac` tabella. Incollare la query seguente in un nuovo paragrafo. La query recupera l'ID edificio. Anche la differenza tra le temperature target e effettive per ogni edificio in una data specifica. Premere **MAIUSC e INVIO**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    L'istruzione **%sql** all'inizio indica al notebook di usare l'interprete Livy Scala.

6. Selezionare l'icona Grafico a **barre** per modificare la visualizzazione.  **impostazioni**, vengono visualizzate dopo aver selezionato **Grafico a barre**, consente di scegliere **Tasti**e **Valori**.  Nella schermata riportata di seguito sono illustrate questo output.

    ![Eseguire un'istruzione Sql Spark usando il notebook1Run a Spark SQL statement using the notebook1](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Eseguire un'istruzione Sql Spark usando il notebook1Run a Spark SQL statement using the notebook1")

7. È inoltre possibile eseguire istruzioni SQL Spark tramite le variabili nella query. Nel frammento di codice successivo `Temp`viene illustrato come definire una variabile, , nella query con i valori possibili con cui si desidera eseguire una query. Quando si esegue la query per la prima volta, un elenco a tendina viene popolato automaticamente con i valori specificati per la variabile.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Incollare questo frammento di codice in un nuovo paragrafo e premere **MAIUSC+INVIO**. Selezionare **quindi 65** dall'elenco a discesa **Temp.**

8. Selezionare l'icona Grafico a **barre** per modificare la visualizzazione.  Quindi selezionare **le impostazioni** e apportare le seguenti modifiche:

   * **Gruppi:**  Aggiungere **targettemp**.  
   * **Valori:** 1. Rimuovere **la data**.  2. Aggiungere **temp_diff**file .  3.  Modificare l'aggregatore da **SUM** a **AVG**.  

     Nella schermata riportata di seguito sono illustrate questo output.

     ![Eseguire un'istruzione Sql Spark usando il blocco appunti2Run a Spark SQL statement using the notebook2](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Eseguire un'istruzione Sql Spark usando il blocco appunti2Run a Spark SQL statement using the notebook2")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Come usare pacchetti esterni con il notebook

Il notebook zeppelin nel cluster Apache Spark su HDInsight può usare pacchetti esterni forniti dalla community che non sono inclusi nel cluster. Cercare nel [repository Maven](https://search.maven.org/) l'elenco completo dei pacchetti disponibili. È anche possibile ottenere un elenco dei pacchetti disponibili da altre origini. Ad esempio, un elenco completo dei pacchetti creati dalla community è disponibile nel sito Web [spark-packages.org](https://spark-packages.org/).

In questo articolo verrà illustrato come usare il pacchetto [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con il blocco appunti Jupyter.

1. Aprire le impostazioni dell'interprete. Nell'angolo in alto a destra, seleziona il nome utente connesso, quindi seleziona **Interprete**.

    ![Avviare l'interprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Output di Hive")

2. Scorrere fino a **livy2**, quindi selezionare **modifica**.

    ![Modificare le impostazioni dell'interprete1](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Modificare le impostazioni dell'interprete1")

3. Passare al `livy.spark.jars.packages`tasto e impostarne `group:id:version`il valore nel formato . Se si vuole usare il pacchetto [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), è quindi necessario impostare il valore della chiave su `com.databricks:spark-csv_2.10:1.4.0`.

    ![Modificare le impostazioni dell'interprete2](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Modificare le impostazioni dell'interprete2")

    Selezionare **Salva** e quindi **OK** per riavviare l'interprete Livy.

4. il valore della chiave immesso sopra si determina come illustrato di seguito.

    a. Individuare un pacchetto nel repository Maven. Per questo articolo, abbiamo usato [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).

    b. Recuperare dal repository i valori per **GroupId**, **ArtifactId** e **Version**.

    ![Usare pacchetti esterni con Jupyter Notebook](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Usare pacchetti esterni con Jupyter Notebook")

    c. Concatenare i tre valori, separati da due punti (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Posizione di salvataggio dei notebook Zeppelin

I notebook Zeppelin vengono salvati nei nodi head del cluster. Se si elimina il cluster, verranno quindi eliminati anche i notebook. Se si vogliono mantenere i notebook per usarli successivamente in altri cluster, è necessario esportarli al termine dell'esecuzione dei processi. Per esportare un blocco appunti, selezionare l'icona **Esporta** come illustrato nell'immagine seguente.

![Scaricare un notebook](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Scarica il taccuino")

Questa azione salva il blocco appunti come file JSON nel percorso di download.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Utilizzo `Shiro` della configurazione dell'accesso agli interpreti zeppelin nei cluster ESP (Enterprise Security Package)

Come indicato in `%sh` precedenza, l'interprete non è supportato da HDInsight 4.0 in poi. Inoltre, `%sh` poiché l'interprete introduce potenziali problemi di sicurezza, ad esempio le schede comandi di accesso utilizzando i comandi della shell, è stato rimosso anche dai cluster ESP HDInsight 3.6. Significa `%sh` che l'interprete non è disponibile quando si fa clic su **Crea nuova nota** o nell'interfaccia utente dell'interprete per impostazione predefinita.

Gli utenti di dominio `Shiro.ini` con privilegi possono utilizzare il file per controllare l'accesso all'interfaccia utente dell'interprete. Solo questi utenti `%sh` possono creare nuovi interpreti `%sh` e impostare le autorizzazioni per ogni nuovo interprete. Per controllare l'accesso utilizzando il `shiro.ini` file, attenersi alla seguente procedura:

1. Definire un nuovo ruolo utilizzando un nome di gruppo di dominio esistente. Nell'esempio seguente, `adminGroupName` è un gruppo di utenti con privilegi in AAD. Non utilizzare caratteri speciali o spazi vuoti nel nome del gruppo. I caratteri `=` dopo concedono le autorizzazioni per questo ruolo. `*`significa che il gruppo dispone di autorizzazioni complete.

    ```
    [roles]
    adminGroupName = *
    ```

2. Aggiungi il nuovo ruolo per l'accesso agli interpreti zeppelin. Nell'esempio seguente, tutti `adminGroupName` gli utenti in hanno accesso agli interpreti zeppelin e possono creare nuovi interpreti. È possibile inserire più ruoli `roles[]`tra le parentesi in , separati da virgole. Quindi, gli utenti che dispongono delle autorizzazioni necessarie, possono accedere agli interpreti di .eppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Gestione delle sessioni di Livy

Il primo paragrafo di codice nel tuo taccuino zeppelin crea una nuova sessione Livy nel tuo cluster. Questa sessione viene condivisa tra tutti i notebook di zeppelin creati in un secondo momento. Se la sessione di Livy viene uccisa per qualsiasi motivo, i lavori non verranno eseguiti dal taccuino .

In tal caso, è necessario effettuare le seguenti operazioni prima di poter avviare l'esecuzione di processi da un notebook .  

1. Riavviare l'interprete Livy dal notebook Zeppelin. A tale scopo, aprire le impostazioni dell'interprete selezionando il nome utente connesso nell'angolo in alto a destra, quindi selezionare **Interprete**.

    ![Avviare l'interprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Output di Hive")

2. Scorrere fino a **livy2**, quindi selezionare **riavvia**.

    ![Riavvia l'interprete di Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Riavviare l'interprete zeppelin")

3. Eseguire una cella di codice da un notebook Zeppelin esistente. Questo codice crea una nuova sessione Livy nel cluster HDInsight.This code creates a new Livy session in the HDInsight cluster.

## <a name="general-information"></a>Informazioni generali

### <a name="validate-service"></a>Convalida servizio

Per convalidare il servizio da `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` Ambari, passare a CLUSTERNAME è il nome del cluster.

Per convalidare il servizio da una riga di comando, SSH al nodo head. Impostare l'utente su `sudo su zeppelin`zeppelin utilizzando il comando . Comandi di stato:

|Comando |Descrizione |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Stato del servizio.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Versione del servizio.|
|`ps -aux | grep zeppelin`|Identificare pid.|

### <a name="log-locations"></a>Posizioni dei registri

|Service |Path |
|---|---|
|zeppelin-server|/usr/hdp/current/zeppelin-server/|
|Log del server|/var/log/zeppelin|
|Interprete `Shiro`di configurazione, , site.xml, log4j|/usr/hdp/current/zeppelin-server/conf o /etc/zeppelin/conf|
|Directory PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Abilitazione della registrazione di debug

1. Passare `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` a dove CLUSTERNAME è il nome del cluster.

1. Passare a **CONFIGS** > **Advanced zeppelin-log4j-properties** > **log4j_properties_content**.

1. Modifica `log4j.appender.dailyfile.Threshold = INFO` `log4j.appender.dailyfile.Threshold = DEBUG`in .

1. Aggiungere `log4j.logger.org.apache.zeppelin.realm=DEBUG`.

1. Salvare le modifiche e riavviare il servizio.

## <a name="next-steps"></a>Passaggi successivi

[Panoramica: Apache Spark su Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenari

* [Apache Spark con BI: analisi interattiva dei dati tramite Spark in HDInsight con strumenti bi](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](apache-spark-machine-learning-mllib-ipython.md)
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

### <a name="manage-resources"></a>Gestione di risorse

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)
