---
title: "Esercitazione: Creare un'applicazione Scala Maven per Spark in Azure HDInsight usando IntelliJ"
description: Creare un'applicazione Spark scritta in Scala con Apache Maven come sistema di compilazione e un archetipo Maven esistente per Scala fornito da IntelliJ IDEA.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 01/30/2019
ms.openlocfilehash: a969c026d702c423bee4871651c8b4fa26b3d37a
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700945"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Esercitazione: Creare un'applicazione Scala Maven per Apache Spark in HDInsight usando IntelliJ

In questa esercitazione si apprenderà come creare un'applicazione [Apache Spark](https://spark.apache.org/) scritta in [Scala](https://www.scala-lang.org/) usando [Apache Maven](https://maven.apache.org/) con IntelliJ IDEA. L'articolo usa Apache Maven come sistema di compilazione e inizia con un archetipo Maven esistente per Scala fornito da IntelliJ IDEA.  La creazione di un'applicazione Scala in IntelliJ IDEA comporta i passaggi seguenti:

* Usare Maven come sistema di compilazione.
* Aggiornare il file del modello a oggetti dei progetti (POM) per risolvere le dipendenze del modulo Spark.
* Scrivere l'applicazione in Scala.
* Generare un file con estensione jar che può essere inviato ai cluster HDInsight Spark.
* Eseguire l'applicazione in un cluster Spark usando Livy.

> [!NOTE]  
> HDInsight offre inoltre un plug-in IntelliJ IDEA per semplificare il processo di creazione e di invio di applicazioni a un cluster HDInsight Spark basato su Linux. Per altre informazioni, vedere [Usare il plug-in degli strumenti HDInsight per IntelliJ IDEA per creare e inviare applicazioni Apache Spark](apache-spark-intellij-tool-plugin.md).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Usare IntelliJ per sviluppare un'applicazione in Scala Maven

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* [Kit di sviluppo di Oracle Java](https://www.azul.com/downloads/azure-only/zulu/).  Questa esercitazione usa Java versione 8.0.202.
* Ambiente IDE Java. Questo articolo usa [IntelliJ IDEA Community versione  2018.3.4](https://www.jetbrains.com/idea/download/).
* Azure Toolkit for IntelliJ.  Vedere [Installazione di Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installare il plug-in Scala per IntelliJ IDEA
Per installare il plug-in di Scala, procedere come segue:

1. Aprire IntelliJ IDEA.

2. Nella schermata iniziale, passare a **Configure** (Configura) > **Plugin** per aprire la finestra **Plugin**.
   
    ![Abilitare i plug-in Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)

3. Selezionare **Installa** per il plug-in Scala che è disponibile nella nuova finestra.  
 
    ![Installare i plug-in Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Dopo che il plug-in è stato installato correttamente, è necessario riavviare l'IDE.


## <a name="use-intellij-to-create-application"></a>Usare IntelliJ per creare un'applicazione

1. Avviare IntelliJ IDEA e selezionare **Crea nuovo progetto** per aprire la finestra **Nuovo progetto**.

2. Selezionare **Azure Spark/HDInsight** nel riquadro sinistro.

3. Selezionare **Progetto Spark (Scala)** dalla finestra principale.

4. Nell'elenco **Strumento di compilazione** selezionare uno degli strumenti seguenti:
      * **Maven**, per ottenere supporto per la creazione guidata di un progetto Scala.
      * **SBT**, per la gestione delle dipendenze e la compilazione per il progetto Scala.

   ![Finestra di dialogo del nuovo progetto](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. Selezionare **Avanti**.

6. Nella finestra **Nuovo progetto** specificare le informazioni seguenti:  

  	|  Proprietà   | DESCRIZIONE   |  
  	| ----- | ----- |  
  	|Project name (Nome progetto)| Immettere un nome.|  
  	|Project&nbsp;location (Percorso progetto)| Immettere il percorso desiderato in cui salvare il progetto.|
  	|Project SDK (SDK progetto)| Sarà vuoto al primo uso di IDEA.  Selezionare **New** (Nuovo) e passare al proprio JDK.|
  	|Versione Spark|La creazione guidata integra la versione corretta dell'SDK di Spark e Scala. Se la versione del cluster Spark è precedente alla 2.0, selezionare **Spark 1.x**. In caso contrario, selezionare **Spark 2.x**. In questo esempio viene usata la versione **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Selezione di Spark SDK](./media/apache-spark-create-standalone-application/hdi-new-project.png)

7. Selezionare **Fine**.

## <a name="create-a-standalone-scala-project"></a>Creare un progetto Scala autonomo

1. Avviare IntelliJ IDEA e selezionare **Crea nuovo progetto** per aprire la finestra **Nuovo progetto**.

2. Selezionare **Maven** nel riquadro sinistro.

3. Specificare un valore per **Project SDK**. Se vuoto, selezionare **New** (Nuovo) e passare alla directory di installazione Java.

4. Selezionare la casella di controllo **Create from archetype** (Crea da archetipo).  

5. Nell'elenco di archetipi selezionare **org.scala-tools.archetypes:scala-archetype-simple**. Questo archetipo crea la struttura di directory appropriata e scarica le dipendenze predefinite necessarie per scrivere un programma Scala.

    ![Creare un progetto Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. Selezionare **Avanti**.

7. Specificare i valori pertinenti per **GroupId** (ID gruppo), **ArtifactId** (ID elemento) e **Version** (Versione). In questa esercitazione vengono usati i valori seguenti:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

8. Selezionare **Avanti**.

9. Rivedere le impostazioni e selezionare **Next** (Avanti).

10. Verificare il nome e la posizione del progetto e quindi scegliere **Finish** (Fine).  L'importazione del progetto richiederà alcuni minuti.

11. Dopo aver importato il progetto, nel riquadro sinistro passare a **SparkSimpleApp** > **src** > **test**  >  **scala** > **com** > **microsoft** > **spark** > **example**.  Fare clic con il pulsante destro del mouse su **MySpec** e quindi scegliere **Elimina**. Per l'applicazione non è necessario questo file.  Selezionare **OK** nella finestra di dialogo.
  
12. Nei passaggi successivi si aggiornerà il file **pom.xml** per definire le dipendenze per l'applicazione Spark in Scala. Affinché tali dipendenze vengano scaricate e risolte automaticamente, è necessario configurare Maven di conseguenza.

13. Nel menu **File** selezionare **Settings** per aprire la finestra **Settings** (Impostazioni).

14. Nella finestra **Settings** (Impostazioni) passare a **Build, Execution, Deployment** (Compilazione, esecuzione, distribuzione)  > **Build Tools** (Strumenti di compilazione)  > **Maven** > **Importing** (Importazione).

15. Selezionare la casella di controllo **Import Maven projects automatically** (Importa automaticamente progetti Maven).

16. Selezionare **Apply** (Applica) e quindi **OK**.  Verrà visualizzata nuovamente la finestra del progetto.
   
    ![Configurare Maven per i download automatici](./media/apache-spark-create-standalone-application/configure-maven.png)
   

17. Nel riquadro sinistro, passare a **src** > **main** > **scala** > **com.microsoft.spark.example**, quindi fare doppio clic su **App** per aprire App.scala.

18. Aprire e sostituire il codice di esempio esistente con il codice seguente e salvare le modifiche. Questo codice legge i dati del file HVAC.csv, disponibile in tutti i cluster HDInsight Spark, recupera le righe che hanno solo una cifra nella sesta colonna e scrive l'output in **/HVACOut** nel contenitore di archiviazione predefinito per il cluster.

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
19. Nel riquadro sinistro, fare doppio clic su **pom.xml**.  
   
20. Aggiungere i segmenti seguenti in `<project>\<properties>`:
      
          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. Aggiungere i segmenti seguenti in `<project>\<dependencies>`:
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>
      
    Salvare le modifiche apportate a pom.xml.

22. Creare il file con estensione jar. IntelliJ IDEA consente di creare un file con estensione jar come elemento di un progetto. Eseguire i passaggi seguenti.
    
    1. Dal menu **File** selezionare **Project Structure...** (Struttura progetto).

    2. Dalla finestra **Project Structure** (Struttura progetto) passare ad **Artifacts** > **il simbolo più +** > **JAR** > **From modules with dependencies...**.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/create-jar-1.png)

    3. Nella finestra **Create JAR from Modules** (Crea file con estensione jar da moduli) selezionare l'icona della cartella nella casella di testo **Main Class** (Classe principale).

    4. Nella finestra **Select Main Class** (Seleziona classe principale) selezionare la classe visualizzata per impostazione predefinita e quindi scegliere **OK**.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/create-jar-2.png)

    5. Nella finestra **Create JAR from Modules** (Crea file con estensione jar da moduli) verificare che l'opzione **extract to the target JAR** (Estrai nel file con estensione jar di destinazione) sia selezionata e quindi scegliere **OK**.  Questa impostazione crea un singolo file con estensione jar con tutte le dipendenze.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/create-jar-3.png)

    6. Nella scheda **Output Layout** sono elencati tutti i file JAR inclusi nel progetto Maven. È possibile selezionare ed eliminare quelli in cui l'applicazione Scala non ha dipendenze dirette. Per l'applicazione creata in questo caso, è possibile rimuovere tutti i file tranne l'ultimo (**output di compilazione di SparkSimpleApp**). Selezionare i file con estensione jar da eliminare e quindi selezionare il simbolo negativo **-**.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Assicurarsi che la casella di controllo **Include in project build** (Includi nella compilazione del progetto) sia selezionata per garantire che il file con estensione jar venga creato ogni volta che si compila o aggiorna il progetto. Selezionare **Apply** (Applica) e quindi **OK**.

    7. Per creare il file jar, passare a **Build** > **Build Artifacts** > **Build**. Il progetto verrà compilato in circa 30 secondi.  Il file JAR di output viene creato in **\out\artifacts**.
       
        ![Creazione di un file con estensione jar](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Eseguire l'applicazione nel cluster Apache Spark
Per eseguire l'applicazione nel cluster, è possibile usare gli approcci seguenti:

* **Copiare il file jar dell’applicazione nel BLOB di archiviazione Azure** associato al cluster. A tale scopo è possibile usare [**AzCopy**](../../storage/common/storage-use-azcopy.md), un'utilità della riga di comando. È possibile usare molti altri client per caricare i dati. Altre informazioni in merito sono disponibili in [Caricare dati per processi Apache Hadoop in HDInsight](../hdinsight-upload-data.md).

* **Usare Apache Livy per inviare un processo dell'applicazione in modalità remota** al cluster Spark. I cluster Spark in HDInsight includono Livy che espone gli endpoint REST per inviare in modalità remota i processi Spark. Per altre informazioni, vedere [Inviare processi Apache Spark in modalità remota usando Apache Livy con cluster Spark in HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Passaggio successivo

In questo articolo è stato descritto come creare un'applicazione Scala di Apache Spark. Passare all'articolo successivo per informazioni su come eseguire questa applicazione in un cluster HDInsight Spark tramite Livy.

> [!div class="nextstepaction"]
>[Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](./apache-spark-livy-rest-interface.md)
