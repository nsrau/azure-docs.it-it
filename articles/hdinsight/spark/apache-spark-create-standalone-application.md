---
title: "Esercitazione: Creare un'applicazione di Scala Maven per Spark in HDInsight usando IntelliJ | Documenti Microsoft"
description: Creare un'applicazione Spark scritta in Scala con Apache Maven come sistema di compilazione e un archetipo Maven esistente per Scala fornito da IntelliJ IDEA.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: ed90e50167f7e86c464b1571b91dc27435437e9b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627417"
---
# <a name="tutorial-create-a-scala-maven-application-for-spark-in-hdinsight-using-intellij"></a>Esercitazione: Creare un'applicazione di Scala Maven per Spark in HDInsight usando IntelliJ

In questa esercitazione si apprenderà come creare un'applicazione Spark scritta in Scala usando Maven con IntelliJ IDEA. L'articolo usa Apache Maven come sistema di compilazione e inizia con un archetipo Maven esistente per Scala fornito da IntelliJ IDEA.  La creazione di un'applicazione Scala in IntelliJ IDEA comporta i passaggi seguenti:

* Usare Maven come sistema di compilazione.
* Aggiornare il file del modello a oggetti dei progetti (POM) per risolvere le dipendenze del modulo Spark.
* Scrivere l'applicazione in Scala.
* Generare un file con estensione jar che può essere inviato ai cluster HDInsight Spark.
* Eseguire l'applicazione in un cluster Spark usando Livy.

> [!NOTE]
> HDInsight offre inoltre un plug-in IntelliJ IDEA per semplificare il processo di creazione e di invio di applicazioni a un cluster HDInsight Spark basato su Linux. Per altre informazioni, vedere [Utilizzare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Spark](apache-spark-intellij-tool-plugin.md).
> 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Usare IntelliJ per sviluppare un'applicazione in Scala Maven

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.


## <a name="prerequisites"></a>prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit. Per installarlo, fare clic [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Ambiente IDE Java. Questo articolo usa IntelliJ IDEA 18.1.1. Per installarlo, fare clic [qui](https://www.jetbrains.com/idea/download/).

## <a name="use-intellij-to-create-application"></a>Usare IntelliJ per creare un'applicazione

1. Avviare IntelliJ IDEA e creare un progetto. Nella finestra di dialogo **New Project** (Nuovo progetto) seguire questa procedura: 

   a. Selezionare **HDInsight** > **Spark on HDInsight (Scala)** (Spark in HDInsight - Scala).

   b. Nell'elenco **Build tool** (Strumento di compilazione) selezionare uno degli strumenti seguenti, in base alla necessità:

      * **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala
      * **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala

   ![Finestra di dialogo del nuovo progetto](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

2. Selezionare **Avanti**.

3. La creazione guidata del progetto Scala rileva automaticamente se è installato il plug-in Scala. Selezionare **Installa**.

   ![Verifica del plug-in Scala](./media/apache-spark-create-standalone-application/Scala-Plugin-check-Reminder.PNG) 

4. Per scaricare il plug-in Scala, selezionare **OK**. Seguire le istruzioni per riavviare IntelliJ. 

   ![Finestra di dialogo di installazione del plug-in Scala](./media/apache-spark-create-standalone-application/Choose-Scala-Plugin.PNG)

5. Nella finestra **New Project** (Nuovo progetto) seguire questa procedura:  

    ![Selezione di Spark SDK](./media/apache-spark-create-standalone-application/hdi-new-project.png)

   a. Immettere un nome e un percorso per il progetto.

   b. Nell'elenco a discesa **Project SDK** (SDK progetto) selezionare **Java 1.8** per il cluster Spark 2.x oppure **Java 1.7** per il cluster Spark 1.x.

   c. Nell'elenco a discesa **Spark version** (Versione di Spark) la creazione guidata del progetto Scala inserisce la versione corretta per Spark SDK e Scala SDK. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo esempio viene usata la versione **Spark 2.0.2 (Scala 2.11.8)**.

6. Selezionare **Fine**.

## <a name="install-scala-plugin-for-intellij-idea"></a>Installare il plug-in Scala per IntelliJ IDEA
Per installare il plug-in di Scala, procedere come segue:

1. Aprire IntelliJ IDEA.
2. Nella schermata iniziale selezionare **Configure** (Configura) e quindi **Plugins** (Plug-in).
   
    ![Abilitare i plug-in Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
3. Selezionare **Install JetBrains plugin** (Installa plug-in JetBrains) nell'angolo inferiore sinistro. 
4. Nella finestra di dialogo **Browse JetBrains Plugins** (Esplora plugin JetBrains) che si apre cercare **Scala**, quindi fare clic su **Install** (Installa).
   
    ![Installare i plug-in Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
5. Dopo che il plug-in è stato installato correttamente, è necessario riavviare l'IDE.

## <a name="create-a-standalone-scala-project"></a>Creare un progetto Scala autonomo
1. Aprire IntelliJ IDEA.
2. Dal menu **File** selezionare **Nuovo > Progetto** per creare un nuovo progetto.
3. Nella finestra di dialogo New Project (Nuovo progetto) selezionare le opzioni seguenti:
   
    ![Creare un progetto Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Selezionare **Maven** come tipo di progetto.
   * Specificare un valore per **Project SDK**. Scegliere **New** (Nuovo) e passare alla directory di installazione Java, in genere `C:\Program Files\Java\jdk1.8.0_66`.
   * Selezionare l’opzione **Create from archetype** .
   * Nell'elenco di archetipi selezionare **org.scala-tools.archetypes:scala-archetype-simple**. Questo archetipo crea la struttura di directory appropriata e scarica le dipendenze predefinite necessarie per scrivere un programma Scala.
4. Selezionare **Avanti**.
5. Specificare i valori pertinenti per **GroupId** (ID gruppo), **ArtifactId** (ID elemento) e **Version** (Versione). In questa esercitazione vengono usati i valori seguenti:

    - GroupId: com.microsoft.spark.example
    - ArtifactId: SparkSimpleApp
6. Selezionare **Avanti**.
7. Rivedere le impostazioni e selezionare **Next** (Avanti).
8. Verificare il nome e la posizione del progetto e quindi scegliere **Finish** (Fine).
9. Nel riquadro sinistro, selezionare **src > test > scala > com > microsoft > spark > example**, fare clic con il pulsante destro del mouse su **MySpec**, quindi selezionare **Elimina**. Per l'applicazione non è necessario questo file.
  
10. Nei passaggi successivi si aggiornerà il file pom.xml per definire le dipendenze per l'applicazione Spark in Scala. Affinché tali dipendenze vengano scaricate e risolte automaticamente, è necessario configurare Maven di conseguenza.
   
    ![Configurare Maven per i download automatici](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. Nel menu **File** selezionare **Impostazioni**.
   2. Nella finestra di dialogo **Settings** (Impostazioni) passare a **Build, Execution, Deployment** (Compilazione, esecuzione, distribuzione)  > **Build Tools** (Strumenti di compilazione)  > **Maven** > **Importing** (Importazione).
   3. Selezionare l'opzione **Import Maven projects automatically**.
   4. Selezionare **Apply** (Applica) e quindi **OK**.
11. Nel riquadro sinistro, selezionare **src > main > scala > com.microsoft.spark.example**, quindi fare doppio clic su **App** per aprire App.scala.

12. Aprire e sostituire il codice di esempio esistente con il codice seguente e salvare le modifiche. Questo codice legge i dati del file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe che hanno solo una cifra nella sesta colonna e scrive l'output in **/HVACOut** nel contenitore di archiviazione predefinito per il cluster.

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
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
13. Nel riquadro sinistro, fare doppio clic su **pom.xml**.
   
   1. Aggiungere i segmenti seguenti in `<project>\<properties>`:
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Aggiungere i segmenti seguenti in `<project>\<dependencies>`:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Salvare le modifiche apportate a pom.xml.
10. Creare il file con estensione jar. IntelliJ IDEA consente di creare un file con estensione jar come elemento di un progetto. Eseguire i passaggi seguenti.
    
    1. Dal menu **File** selezionare **Project Structure** (Struttura progetto).
    2. Nella finestra di dialogo **Project Structure** (Struttura progetto) fare clic su **Artifacts** (Elementi) e selezionare il segno più. Nella finestra di dialogo popup selezionare **JAR**, quindi fare clic su **From modules with dependencies** (Da moduli con dipendenze).
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. Nella finestra di dialogo **Create JAR from Modules** (Crea file con estensione jar da moduli) selezionare i puntini di sospensione (![puntini di sospensione](./media/apache-spark-create-standalone-application/ellipsis.png)) relativi a **Main Class** (Classe principale).
    4. Nella finestra di dialogo **Select Main Class** (Seleziona classe principale) selezionare la classe visualizzata per impostazione predefinita e quindi scegliere **OK**.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. Nella finestra di dialogo **Create JAR from Modules** (Crea file con estensione jar da moduli) verificare che l'opzione **extract to the target JAR** (Estrai nel file con estensione jar di destinazione) sia selezionata e quindi scegliere **OK**.  Questa impostazione crea un singolo file con estensione jar con tutte le dipendenze.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. Nella scheda del layout dell'output sono elencati tutti i file con estensione jar inclusi nel progetto Maven. È possibile selezionare ed eliminare quelli in cui l'applicazione Scala non ha dipendenze dirette. Per l'applicazione creata in questo caso, è possibile rimuovere tutti i file tranne l'ultimo (**output di compilazione di SparkSimpleApp**). Selezionare i file con estensione jar da eliminare e quindi fare clic sull'icona **Elimina**.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Assicurarsi che la casella **Include in project build** (Includi nella compilazione del progetto) sia selezionata per garantire che il file con estensione jar venga creato ogni volta che il progetto viene compilato o aggiornato. selezionare **Apply** (Applica) e quindi **OK**.
    7. Dal menu **Build** (Compila) scegliere **Build Artifacts** (Compila elementi) per creare il file con estensione jar. Il file JAR di output viene creato in **\out\artifacts**.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Eseguire l'applicazione nel cluster Spark
Per eseguire l'applicazione nel cluster, è possibile usare gli approcci seguenti:

* **Copiare il file jar dell’applicazione nel BLOB di archiviazione Azure** associato al cluster. A tale scopo è possibile usare [**AzCopy**](../../storage/common/storage-use-azcopy.md), un'utilità della riga di comando. È possibile usare molti altri client per caricare i dati. Altre informazioni in merito sono disponibili in [Caricare dati per processi Hadoop in HDInsight](../hdinsight-upload-data.md).
* **Usare Livy per inviare un processo dell’applicazione in modalità remota** al cluster Spark. I cluster Spark in HDInsight includono Livy che espone gli endpoint REST per inviare in modalità remota i processi Spark. Per ulteriori informazioni, vedere [Inviare processi Spark in modalità remota utilizzando Livy con cluster Spark in HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Passaggio successivo

In questo articolo è stato descritto come creare un'applicazione Scala di Spark. Passare all'articolo successivo per informazioni su come eseguire questa applicazione in un cluster HDInsight Spark tramite Livy.

> [!div class="nextstepaction"]
>[Eseguire processi in modalità remota in un cluster Spark usando Livy](./apache-spark-livy-rest-interface.md)

