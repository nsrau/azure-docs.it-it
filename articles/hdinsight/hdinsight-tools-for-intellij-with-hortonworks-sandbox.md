---
title: Usare Azure Toolkit for IntelliJ con Hortonworks Sandbox | Documentazione Microsoft
description: Informazioni su come usare gli strumenti HDInsight in Azure Toolkit for IntelliJ con Hortonworks Sandbox.
keywords: strumenti hadoop,query hive,intellij,hortonworks sandbox,azure toolkit for intellij
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: b52e5bcf443afd05bf8cc37ce436cb80ab41412f
ms.lasthandoff: 01/24/2017


---
#<a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Usare gli strumenti HDInsight per IntelliJ con Hortonworks Sandbox

Informazioni su come usare gli strumenti HDInsight per IntelliJ per sviluppare applicazioni Apache Scala e testare le applicazioni in [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) in esecuzione sulla workstation. [IntelliJ IDEA](https://www.jetbrains.com/idea/) è un ambiente di sviluppo integrato (IDE) Java per lo sviluppo di software per computer. Dopo aver sviluppato e testato le applicazioni in Hortonworks Sandbox, è possibile trasferirle in [Azure HDInsight](hdinsight-hadoop-introduction.md).

##<a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario avere:

- HDP 2.4 in Hortonworks Sandbox in esecuzione nell'ambiente locale. Per la configurazione, vedere [Introduzione all'ecosistema Hadoop con un ambiente sandbox Hadoop su una macchina virtuale](hdinsight-hadoop-emulator-get-started.md). Si noti che gli strumenti HDInsight per IntelliJ sono stati testati solo con HDP 2.4. Dal [sito di download di Hortonworks Sandbox](http://hortonworks.com/downloads/#sandbox) espandere **Hortonworks Sandbox Archive**.
- [Java Developer Kit (JDK) versione 1.8 o successiva](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). JDK è richiesto da Azure Toolkit for IntelliJ.
- [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download) con plug-in [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) e plug-in [Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij.md). Gli strumenti HDInsight per IntelliJ sono disponibili come parte di Azure Toolkit for IntelliJ. 

  **Per installare i plug-in:**

  1. Aprire IntelliJ IDEA.
  2. Nella schermata iniziale fare clic su **Configure** (Configura) e quindi su **Plugins** (Plug-in).
  3. Fare clic su **Install JetBrains plugin** (Installa plug-in JetBrains) nell'angolo inferiore sinistro.
  4. Usare la funzione di ricerca per cercare **Scala**, quindi fare clic su **Install** (Installa).
  5. Fare clic su **Restart IntelliJ IDEA** (Riavvia IntelliJ IDEA) per completare l'installazione.
  6. Ripetere i passaggi 4 e 5 per installare **Azure Toolkit for IntelliJ**. Per altre informazioni, vedere [Installare Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="create-a-spark-scala-application"></a>Creare un'applicazione Spark Scala

In questa sezione verrà creato un progetto Scala di esempio usando IntelliJ IDEA. Nella sezione successiva, IntelliJ IDEA verrà collegato a Hortonworks Sandbox (emulatore) prima di inviare il progetto.

1. Aprire IntelliJ IDEA dalla workstation.
2. Fare clic su **Create New Project** (Crea nuovo progetto).
3. Fare clic su **HDInsight** nel pannello sinistro, fare clic su **Spark on HDInsight (Scala)** (Spark in HDInsight - Scala) nel pannello destro, quindi fare clic su **Next** (Avanti).

    ![Creare il progetto IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)
4. Immettere le seguenti informazioni:

    ![Proprietà della creazione del progetto IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **Project name** (Nome progetto): specificare un nome per il progetto.
    - **Project location** (Percorso progetto): specificare un percorso per il progetto.
    - **Project SDK** (SDK progetto): fare clic su **New** (Nuovo), su **JDK** e quindi specificare la cartella di Java JDK versione 7 o successiva.  Il percorso predefinito è C:\Programmi\Java\jdk1.8.x_xxx.
    - **Scala SDK**: fare clic su **Select** (Seleziona), selezionare la versione **2.10.6** e quindi fare clic su **OK**. Se la versione non è elencata, fare clic su **Download**, selezionare **Scala version**, quindi fare clic su **OK**. Assicurarsi di non usare la versione 2.11.x. In questo articolo viene usata la versione 2.10.6.
    - **Spark SDK**: scaricare l'[SDK](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). È anche possibile ignorare questo passaggio e usare invece il repository Maven per Spark, verificando tuttavia di aver installato il repository Maven corretto per sviluppare le applicazioni Spark. Ad esempio, è necessario verificare di aver installato la parte Spark Streaming, se si usa Spark Streaming e di usare il repository contrassegnato come Scala 2.10. Non usare il repository Scala 2.11.
5. Fare clic su **Fine**.
6. Premere **[ALT]+1** per aprire la visualizzazione progetto, se non è già aperta.
7. Da **Project Explorer** espandere il progetto e quindi fare clic su **src**.
8. Fare clic con il pulsante destro del mouse su **src**, scegliere **New** (Nuovo) e quindi fare clic su **Scala class** (Classe Scala).
9. Specificare un nome, selezionare **Object** (Oggetto) in **Kind** (Tipo), quindi fare clic su **OK**.

    ![Creare una nuova classe IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)
10. Incollare il codice seguente nel file con estensione scala:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

11. Nel menu **Build** (Compila) fare clic su **Build project** (Compila progetto). Accertarsi che la compilazione venga completata correttamente.


## <a name="link-to-the-hortonworks-sandbox"></a>Eseguire il collegamento a Hortonworks Sandbox

È necessario avere un'applicazione IntelliJ esistente prima di eseguire il collegamento a Hortonworks Sandbox (emulatore).

**Per eseguire il collegamento a un emulatore**

1. Aprire il progetto in IntelliJ, se non è già aperto.
2. Nel menu **View** (Visualizza) selezionare **Tool Windows** (Finestre degli strumenti) e quindi fare clic su **Azure Explorer**.
3. Espandere **Azure**, fare clic con il pulsante destro del mouse su **HDInsight** e quindi scegliere **Link an Emulator** (Collega un emulatore).
4. Immettere la password configurata per l'account radice di Hortonworks Sandbox e il resto dei valori in modo simile allo screenshot seguente, quindi fare clic su **OK**. 

  ![IntelliJ - Collegare un emulatore](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Fare clic su **Yes** per configurare l'emulatore.

  Quando è connesso correttamente, è possibile visualizzare l'emulatore (Hortonworks Sandbox) elencato nel nodo HDInsight.

## <a name="submit-the-spark-scala-application-to-the-sandbox"></a>Inviare l'applicazione Spark Scala alla sandbox

Dopo aver collegato IntelliJ IDEA all'emulatore è possibile inviare il progetto.

**Per inviare un progetto a un emulatore**

1. In **Project Explorer** (Esplora progetti) fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Submit Spark Application to HDInsight** (Invia applicazione Spark a HDInsight).
2. Specificare i campi seguenti:

    - **Spark cluster (Linux only)** ( Cluster Spark - solo Linux): selezionare l'ambiente Hortonworks Sandbox locale.
    - **Main class name** (Nome classe principale): scegliere o immettere il nome della classe principale.  Per questa esercitazione è **GroupByTest**.
3. Fare clic su **Submit**. I log di invio dei processi vengono visualizzati nella finestra degli strumenti di invio Spark.

##<a name="next-steps"></a>Passaggi successivi:

- Per informazioni su come creare applicazioni Spark per HDInsight con gli strumenti HDInsight per IntelliJ, vedere [Usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per creare applicazioni Spark per il cluster HDInsight Spark Linux](hdinsight-apache-spark-intellij-tool-plugin.md).
- Per un video sugli strumenti HDInsight per IntelliJ, vedere [Introducing HDInsight Tools for IntelliJ for Spark Development](https://mix.office.com/watch/1nqkqjt5xonza) (Introduzione agli strumenti HDInsight per IntelliJ per lo sviluppo Spark).
- Per informazioni su come eseguire il debug di applicazioni Spark usando il toolkit in remoto in HDInsight, vedere [Usare gli strumenti HDInsight in Azure Toolkit for IntelliJ per eseguire il debug di applicazioni Spark in remoto in cluster HDInsight Spark Linux](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).
- Per informazioni su come usare gli strumenti HDInsight per Eclipse per creare un'applicazione Spark, vedere [Usare gli strumenti HDInsight in Azure Toolkit for Eclipse per creare applicazioni Spark](hdinsight-apache-spark-eclipse-tool-plugin.md).
- Per un video sugli strumenti HDInsight per Eclipse, vedere [Use HDInsight Tool for Eclipse to create Spark applications](https://mix.office.com/watch/1rau2mopb6fha) (Usare gli strumenti HDInsight per Eclipse per creare applicazioni Spark).
