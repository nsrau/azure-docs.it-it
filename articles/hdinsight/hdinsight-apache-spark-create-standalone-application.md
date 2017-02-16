---
title: Creare applicazioni Scala autonome da eseguire nei cluster Azure Spark | Documentazione Microsoft
description: Informazioni su come creare un&quot;applicazione Spark autonoma da eseguire nei cluster HDInsight Spark.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 153b1ea4ec3d326fb533817cdb74d3489135f7d9


---
# <a name="create-a-standalone-scala-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Creare un'applicazione Scala autonoma da eseguire nel cluster Apache Spark in HDInsight

In questo articolo vengono fornite istruzioni dettagliate sullo sviluppo di applicazioni Spark autonome scritte in Scala usando Maven con IntelliJ IDEA. L'articolo usa Apache Maven come sistema di compilazione e inizia con un archetipo Maven esistente per Scala fornito da IntelliJ IDEA.  In generale, la creazione di un'applicazione Scala in IntelliJ IDEA comporta i passaggi seguenti:

* Usare Maven come sistema di compilazione.
* Aggiornare il file del modello a oggetti dei progetti (POM) per risolvere le dipendenze del modulo Spark.
* Scrivere l'applicazione in Scala.
* Generare un file con estensione jar che può essere inviato ai cluster HDInsight Spark.
* Eseguire l'applicazione in un cluster Spark usando Livy.

> [!NOTE]
> HDInsight offre inoltre un plug-in IntelliJ IDEA per semplificare il processo di creazione e di invio di applicazioni a un cluster HDInsight Spark basato su Linux. Per altre informazioni, vedere [Utilizzare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark](hdinsight-apache-spark-intellij-tool-plugin.md).
> 
> 

**Prerequisiti**

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit. Per installarlo, fare clic [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Ambiente IDE Java. Questo articolo usa IntelliJ IDEA 15.0.1. Per installarlo, fare clic [qui](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installare il plug-in Scala per IntelliJ IDEA
Se durante l'installazione di IntelliJ IDEA non è stata richiesta l'abilitazione del plug-in Scala, avviare IntelliJ IDEA e completare i passaggi seguenti per installare il plug-in:

1. Avviare IntelliJ IDEA e dalla schermata iniziale fare clic su **Configure** (Configura) e quindi fare clic su **Plugins** (Plugin).
   
    ![Abilitare i plug-in Scala](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)
2. Nella schermata successiva fare clic su **Install JetBrains plugin** nell'angolo inferiore sinistro. Nella finestra di dialogo **Browse JetBrains Plugins** (Esplora plugin JetBrains) che si apre cercare Scala e quindi fare clic su **Install** (Installa).
   
    ![Installare i plug-in Scala](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)
3. Quando il plug-in è stato installato correttamente, fare clic sul pulsante **Restart IntelliJ IDEA** per riavviare l'IDE.

## <a name="create-a-standalone-scala-project"></a>Creare un progetto Scala autonomo
1. Avviare IntelliJ IDEA e creare un nuovo progetto. Nella finestra di dialogo del nuovo progetto selezionare le opzioni seguenti e quindi fare clic su **Next**.
   
    ![Creare un progetto Maven](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)
   
   * Selezionare **Maven** come tipo di progetto.
   * Specificare un valore per **Project SDK**. Fare clic su New e passare alla directory di installazione Java, in genere `C:\Program Files\Java\jdk1.8.0_66`.
   * Selezionare l’opzione **Create from archetype** .
   * Nell'elenco di archetipi selezionare **org.scala-tools.archetypes:scala-archetype-simple**. Verrà creata la struttura di directory appropriata e verranno scaricate le dipendenze predefinite necessarie per scrivere un programma Scala.
2. Specificare i valori pertinenti per **GroupId** (ID gruppo), **ArtifactId** (ID elemento) e **Version** (Versione). Fare clic su **Avanti**.
3. Nella finestra di dialogo successiva, in cui si specifica la home directory Maven e altre impostazioni utente, accettare le impostazioni predefinite e fare clic su **Next**.
4. Nell'ultima finestra di dialogo specificare un nome di progetto e il percorso, quindi fare clic su **Finish**.
5. Eliminare il file **MySpec.Scala** nel percorso **src\test\scala\com\microsoft\spark\example**, poiché non è necessario per l'applicazione.
6. Se necessario, rinominare i file di origine e di test predefiniti. Nel riquadro sinistro di IntelliJ IDEA passare a **src\main\scala\com.microsoft.spark.example**. Fare clic con il pulsante destro del mouse su **App.scala**, scegliere **Refactor** (Refactoring), fare clic su Rename file e nella finestra di dialogo specificare il nuovo nome per l'applicazione, quindi fare clic su **Refactor** (Refactoring).
   
    ![Rinominare file](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)  
7. Nei passaggi successivi si aggiornerà il file pom.xml per definire le dipendenze per l'applicazione Spark in Scala. Affinché tali dipendenze vengano scaricate e risolte automaticamente, è necessario configurare Maven di conseguenza.
   
    ![Configurare Maven per i download automatici](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)
   
   1. Scegliere **Settings** (Impostazioni) dal menu **File**.
   2. Nella finestra di dialogo **Settings** (Impostazioni) passare a **Build, Execution, Deployment** (Compilazione, esecuzione, distribuzione)  > **Build Tools** (Strumenti di compilazione)  > **Maven** > **Importing** (Importazione).
   3. Selezionare l'opzione **Import Maven projects automatically**.
   4. Fare clic su **Apply** (Applica) e quindi su **OK**.
8. Aggiornare il file di origine Scala per includere il codice dell'applicazione. Aprire e sostituire il codice di esempio esistente con il codice seguente e salvare le modifiche. Questo codice legge i dati del file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe che hanno solo una cifra nella sesta colonna e scrive l'output in **/HVACOut** nel contenitore di archiviazione predefinito per il cluster.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasbs:///HVACout")
          }
        }
9. Aggiornare il file pom.xml.
   
   1. Aggiungere il codice seguente in `<project>\<properties>`
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Aggiungere il codice seguente in `<project>\<dependencies>`
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Salvare le modifiche apportate a pom.xml.
10. Creare il file con estensione jar. IntelliJ IDEA consente di creare un file con estensione jar come elemento di un progetto. Eseguire i passaggi seguenti.
    
    1. Scegliere **Project Structure** (Struttura progetto) dal menu **File**.
    2. Nella finestra di dialogo **Project Structure** (Struttura progetto) fare clic su **Artifacts** (Elementi) e quindi sul segno più. Nella finestra di dialogo popup fare clic su **JAR**, quindi fare clic su **From modules with dependencies** (Da moduli con dipendenze).
       
        ![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)
    3. Nella finestra di dialogo **Create JAR from Modules** (Crea JAR da moduli) fare clic sui puntini di sospensione (![puntini di sospensione](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png)) relativi a **Main Class** (Classe principale).
    4. Nella finestra di dialogo **Select Main Class** (Seleziona classe principale) selezionare la classe visualizzata per impostazione predefinita e quindi fare clic su **OK**.
       
        ![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)
    5. Nella finestra di dialogo **Create JAR from Modules** (Crea JAR da moduli) verificare che l'opzione per **estrarre nel file JAR di destinazione** sia selezionata e quindi fare clic su **OK**. Verrà creato un singolo file con estensione jar con tutte le dipendenze.
       
        ![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)
    6. Nella scheda del layout dell'output sono elencati tutti i file con estensione jar inclusi nel progetto Maven. È possibile selezionare ed eliminare quelli in cui l'applicazione Scala non ha dipendenze dirette. Per l'applicazione creata in questo caso, è possibile rimuovere tutti i file tranne l'ultimo (**output di compilazione di SparkSimpleApp**). Selezionare i file JAR da eliminare e quindi fare clic sull’icona **Delete** .
       
        ![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)
       
        Assicurarsi che la casella **Build on make** sia selezionata per garantire che il file jar venga creato ogni volta che il progetto viene creato o aggiornato. Fare clic su **Apply** (Applica) e quindi su **OK**.
    7. Sulla barra dei menu fare clic su **Build** (Compila) e quindi su **Make Project** (Crea progetto). È anche possibile fare clic su **Build Artifacts** per creare il file JAR. Il file JAR di output viene creato in **\out\artifacts**.
       
        ![Creazione di un file con estensione jar](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Eseguire l'applicazione nel cluster Spark
Per eseguire l'applicazione nel cluster, è necessario eseguire le operazioni seguenti:

* **Copiare il file jar dell’applicazione nel BLOB di archiviazione Azure** associato al cluster. A tale scopo è possibile usare [**AzCopy**](../storage/storage-use-azcopy.md), un'utilità della riga di comando. È possibile usare molti altri client per caricare i dati. Ulteriori informazioni in merito sono disponibili in [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md).
* **Usare Livy per inviare un processo dell’applicazione in modalità remota** al cluster Spark. I cluster Spark in HDInsight includono Livy che espone gli endpoint REST per inviare in modalità remota i processi Spark. Per ulteriori informazioni, vedere [Inviare processi Spark in modalità remota utilizzando Livy con cluster Spark in HDInsight](hdinsight-apache-spark-livy-rest-interface.md).

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con Machine Learning: utilizzare Spark in HDInsight per l'analisi della temperatura di compilazione utilizzando dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usare Spark in HDInsight per la creazione di applicazioni di streaming in tempo reale](hdinsight-apache-spark-eventhub-streaming.md)
* [Analisi dei log del sito Web mediante Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creare ed eseguire applicazioni
* [Eseguire processi in modalità remota in un cluster Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Strumenti ed estensioni
* [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark in Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Usare il plug-in Strumenti HDInsight per IntelliJ IDEA per eseguire il debug di applicazioni Spark in remoto)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usare i notebook di Zeppelin con un cluster Spark in HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usare pacchetti esterni con i notebook Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installare Jupyter Notebook nel computer e connetterlo a un cluster HDInsight Spark](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)




<!--HONumber=Jan17_HO4-->


