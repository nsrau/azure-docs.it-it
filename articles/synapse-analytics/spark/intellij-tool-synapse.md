---
title: 'Esercitazione: Azure Toolkit for IntelliJ (applicazione Spark)'
description: 'Esercitazione: Usare Azure Toolkit for IntelliJ per sviluppare applicazioni Spark scritte in Scala e inviarle a un pool di Apache Spark (anteprima).'
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: be127d4214577e017522aec6a1b61b8f62638ed9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368741"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>Esercitazione: Creare un'applicazione Apache Spark con IntelliJ usando un'area di lavoro di Synapse

Questa esercitazione illustra come usare il plug-in Azure Toolkit for IntelliJ per sviluppare applicazioni Apache Spark scritte in [Scala](https://www.scala-lang.org/) e quindi inviarle a un pool di Spark (anteprima) direttamente dall'ambiente di sviluppo integrato (IDE) di IntelliJ. È possibile usare il plug-in in vari modi:

- Sviluppare e inviare un'applicazione Spark in Scala in un pool di Spark.
- Accedere alle risorse dei pool di Spark.
- Sviluppare ed eseguire un'applicazione Spark in Scala localmente.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> - Usare Azure Toolkit for IntelliJ
> - Sviluppare applicazioni Apache Spark
> - Inviare l'applicazione ai pool di Spark

## <a name="prerequisites"></a>Prerequisiti

- [IntelliJ IDEA versione Community](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC).
- Plug-in Azure Toolkit versione 3.27.0-2019.2 - Eseguire l'installazione dal [repository di plug-in IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [JDK (versione 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Plug-in Scala - Eseguire l'installazione dal [repository di plug-in IntelliJ](/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#install-scala-plugin-for-intellij-idea).
- Il prerequisito seguente si applica solo agli utenti di Windows:

  Quando si esegue l'applicazione Spark Scala locale in un computer Windows, potrebbe essere restituita un'eccezione, come spiegato in [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356), che si verifica a causa di un file WinUtils.exe mancante in Windows.
  Per risolvere questo errore, scaricare il file [eseguibile WinUtils](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) in un percorso come **C:\WinUtils\bin** . È quindi necessario aggiungere una variabile di ambiente **HADOOP_HOME** e impostare il valore della variabile su **C:\WinUtils** .

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Creare un'applicazione Spark in Scala per un pool di Spark

1. Avviare IntelliJ IDEA e selezionare **Crea nuovo progetto** per aprire la finestra **Nuovo progetto** .
2. Selezionare **Apache Spark/HDInsight** nel riquadro sinistro.
3. Selezionare **Spark Project with Samples(Scala)** (Progetto Spark con esempi (Scala)) dalla finestra principale.
4. Nell'elenco **Strumento di compilazione** selezionare uno dei tipi seguenti:

   - **Maven** , per ottenere supporto per la creazione guidata di un progetto Scala.
   - **SBT** , per la gestione delle dipendenze e la compilazione per il progetto Scala.

    ![IntelliJ IDEA: finestra di dialogo per un nuovo progetto](./media/intellij-tool-synapse/create-synapse-application01.png)

5. Selezionare **Avanti** .
6. Nella finestra **Nuovo progetto** specificare le informazioni seguenti:

    | Proprietà | Descrizione |
    | ----- | ----- |
    |Project name (Nome progetto)| Immettere un nome. In questa esercitazione viene usato `myApp`.|
    |Project&nbsp;location (Percorso progetto)| Immettere il percorso desiderato in cui salvare il progetto.|
    |Project SDK (SDK progetto)| Potrebbe essere vuoto al primo uso di IDEA. Selezionare **New** (Nuovo) e passare al proprio JDK.|
    |Versione Spark|La creazione guidata integra la versione corretta dell'SDK di Spark e Scala. Synapse supporta solo **Spark 2.4.0** .|
    |||

    ![Selezione dell'SDK Apache Spark](./media/intellij-tool-synapse/create-synapse-application02.png)

7. Selezionare **Fine** . Potrebbero occorrere alcuni minuti prima che il progetto diventi disponibile.
8. Il progetto Spark crea automaticamente un artefatto. Per visualizzare l'artefatto, eseguire questa operazione:

   a. Dalla barra dei menu passare a **File** > **Struttura del progetto...** .

   b. Dalla finestra **Struttura del progetto** , selezionare **Artefatti** .

   c. Selezionare **Annulla** dopo aver visualizzato l'elemento.

    ![Informazioni sull'artefatto nella finestra di dialogo](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. Trovare **LogQuery** in **myApp** > **src** > **main** > **scala**> **sample**> **LogQuery** . Questa esercitazione usa **LogQuery** per l'esecuzione.

   ![Comandi per la creazione di una classe Scala in Progetto](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Connettersi ai pool di Spark

Accedere alla sottoscrizione di Azure per connettersi ai pool di Spark.

### <a name="sign-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure.

1. Dalla barra dei menu, passare a **Visualizza** > **strumento Windows** > **Azure Explorer** .

   ![IntelliJ IDEA mostra Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Da Azure Explorer, fare clic con il pulsante destro del mouse sul nodo **Azure** e quindi scegliere **Sign In** (Accedi).

   ![IntelliJ IDEA: clic con il pulsante destro del mouse su Azure in Azure Explorer](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. Nella finestra di dialogo **Azure Sign In** (Accesso ad Azure) scegliere **Accesso dispositivo** e quindi selezionare **Accedi** .

    ![IntelliJ IDEA: Azure Sign In](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. Nella finestra di dialogo **Accesso dispositivo** selezionare **Copia e apri** .

   ![IntelliJ IDEA: Azure Device Login](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. Nell'interfaccia del browser incollare il codice e quindi selezionare **Avanti** .

   ![Microsoft: finestra di dialogo per l'immissione del codice per HDI](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. Immettere le credenziali di Azure e quindi chiudere il browser.

   ![Microsoft: finestra di dialogo per l'immissione dell'indirizzo di posta elettronica per HDI](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. Dopo l'accesso, la finestra di dialogo **Selezionare le sottoscrizioni** elenca tutte le sottoscrizioni di Azure associate alle credenziali. Selezionare la sottoscrizione e quindi **Seleziona** .

    ![Finestra di dialogo Seleziona sottoscrizioni](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. In **Azure Explorer** espandere **Apache Spark on Synapse** (Apache Spark in Synapse) per visualizzare le aree di lavoro disponibili nelle sottoscrizioni.

    ![IntelliJ IDEA: visualizzazione principale di Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Per visualizzare i pool di Spark, è possibile espandere ulteriormente un'area di lavoro.

    ![Account di archiviazione in Azure Explorer](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Eseguire in modalità remota un'applicazione Spark in Scala in un pool di Spark

Dopo aver creato un'applicazione Scala, è possibile eseguirla in modalità remota.

1. Aprire la finestra **Run/Debug Configurations** (Esecuzione/Debug configurazioni) selezionando l'icona.

    ![Comando di invio dell'applicazione Spark a HDInsight 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. Nella finestra di dialogo **Run/Debug Configurations** (Esecuzione/Debug configurazioni) selezionare **+** e quindi **Apache Spark on Synapse** .

    ![Comando di invio dell'applicazione Spark a HDInsight 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. Nella finestra **Run/Debug Configurations** (Esecuzione/Debug configurazioni) specificare i valori seguenti e quindi selezionare **OK** :

    |Proprietà |valore |
    |----|----|
    |Pool di Spark|Selezionare i pool di Spark in cui eseguire l'applicazione.|
    |Selezionare un artefatto da inviare|Lasciare l'impostazione predefinita.|
    |Nome della classe principale|Il valore predefinito corrisponde alla classe principale del file selezionato. È possibile modificare la classe selezionando i puntini di sospensione ( **...** ) e scegliendo una classe diversa.|
    |Configurazioni del processo|È possibile modificare i valori e la chiave predefiniti. Per altre informazioni, vedere [Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html) (API REST di Apache Livy).|
    |Argomenti della riga di comando|È possibile immettere gli argomenti divisi da uno spazio per la classe principale, se necessario.|
    |Referenced Jars (file JAR di riferimento) e Referenced Files (file di riferimento)|È possibile immettere i percorsi per file e jar di riferimento, se presenti. È anche possibile selezionare i file nel file system virtuale di Azure, che attualmente supporta solo cluster di Azure Data Lake Storage Gen2. Per altre informazioni, vedere [Configurazione di Apache Spark]https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) e [Come caricare risorse nel cluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).|
    |Archivio di caricamento del processo|Espandere per visualizzare le opzioni aggiuntive.|
    |Tipo di archiviazione|Nell'elenco a discesa selezionare **Use Azure Blob to upload** (Usa BLOB di Azure per caricare) o **Use cluster default storage account to upload** (Usa l'account di archiviazione predefinito del cluster per caricare).|
    |Account di archiviazione|Immettere l'account di archiviazione.|
    |Chiave di archiviazione|Immettere la chiave di archiviazione.|
    |Contenitore di archiviazione|Selezionare il contenitore di archiviazione dall'elenco a discesa una volta immessi **Account di archiviazione** e **chiave di archiviazione** .|

    ![Finestra di dialogo per l'invio di Spark 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. Selezionare l'icona **SparkJobRun** per inviare il progetto al pool di Spark selezionato. La scheda **Remote Spark Job in Cluster** (Processo Spark remoto nel cluster) visualizza lo stato dell'esecuzione del processo, nella parte inferiore. È possibile arrestare l'applicazione selezionando il rosso.

    ![Finestra per l'invio di Apache Spark](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![Finestra di dialogo per l'invio di Spark 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Esecuzione/Debug locale di applicazioni Apache Spark

È possibile seguire le istruzioni riportate di seguito per configurare l'esecuzione locale e il debug locale per il processo di Apache Spark.

### <a name="scenario-1-do-local-run"></a>Scenario 1: Esecuzione locale

1. Aprire la finestra di dialogo **Run/Debug Configurations** (Esecuzione/Debug configurazioni) e selezionare il segno più ( **+** ). Selezionare quindi l'opzione **Apache Spark on Synapse** (Apache Spark in Synapse). Per salvare, immettere le informazioni per **Nome** e **Nome della classe principale** .

    ![Esecuzione locale in Run/Debug Configurations di Intellij 1](./media/intellij-tool-synapse/local-run-synapse.png)

    - Le variabili di ambiente e il percorso di WinUtils.exe sono validi solo per gli utenti di Windows.
    - Variabili di ambiente: La variabile di ambiente del sistema può essere rilevata automaticamente se è stata impostata in precedenza e non è necessario aggiungerla manualmente.
    - [WinUtils.exe Location](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) (Posizione WinUtils.exe): È possibile specificare il percorso di WinUtils selezionando l'icona della cartella a destra.

2. Quindi selezionare il pulsante per la riproduzione locale.

    ![Esecuzione locale in Run/Debug Configurations di Intellij 2](./media/intellij-tool-synapse/local-run-synapse01.png)

3. Una volta completata l'esecuzione locale, se lo script include l'output, è possibile controllare il file di output da **data** > **__default__** (dati > predefinito).

    ![Risultato dell'esecuzione locale del progetto IntelliJ 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>Scenario 2: Debug locale

1. Aprire lo script **LogQuery** e impostare i punti di interruzione.
2. Selezionare l'icona **Local debug** (Debug locale) per eseguire il debug locale.

    ![Risultato dell'esecuzione locale del progetto IntelliJ 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Accedere e gestire l'area di lavoro Synapse

In Azure Explorer è possibile eseguire varie operazioni all'interno di Azure Toolkit for IntelliJ. Dalla barra dei menu, passare a **Visualizza** > **strumento Windows** > **Azure Explorer** .

### <a name="launch-workspace"></a>Avviare l'area di lavoro

1. Da Azure Explorer passare a **Apache Spark on Synapse** (Apache Spark in Synapse) ed espandere la voce.

    ![IntelliJ IDEA: visualizzazione principale di Azure Explorer](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. Fare clic con il pulsante destro del mouse su un'area di lavoro, scegliere **Launch workspace** per aprire il sito Web.

    ![Dettagli dell'applicazione nella visualizzazione di processi Spark 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Dettagli dell'applicazione nella visualizzazione di processi Spark 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Console Spark

È possibile eseguire la console locale Spark (Scala) o eseguire la console della sessione Spark Livy interattiva (Scala).

### <a name="spark-local-console-scala"></a>Console locale Spark (Scala)

Assicurarsi di aver soddisfatto il prerequisito relativo al file WINUTILS.EXE.

1. Dalla barra dei menu passare a **Run** > **Edit Configurations...** (Esegui > Modifica configurazioni).
2. Dalla finestra **Run/Debug Configurations** (Esecuzione/Debug configurazioni) nel riquadro a sinistra, passare a **Apache Spark on Synapse** >  **[Spark on Synapse] myApp** (Apache Spark in Synapse > [Spark in Synapse] myApp).
3. Nella finestra principale selezionare la scheda **Locally Run** (Esecuzione locale).
4. Specificare i valori seguenti e quindi selezionare **OK** :

    |Proprietà |valore |
    |----|----|
    |Variabili di ambiente|Verificare che il valore di HADOOP_HOME sia corretto.|
    |WINUTILS.exe location (Percorso di WINUTILS.exe)|Assicurarsi che il percorso sia corretto.|

    ![Impostazione della configurazione nella console locale](./media/intellij-tool-synapse/local-console-synapse01.png)

5. Da Project (Progetto) passare a **myApp** > **src** > **main** > **scala** > **myApp** .
6. Dalla barra dei menu passare a **Tools** > **Spark Console** > **Run Spark Local Console(Scala)** (Strumenti > Console Spark > Esegui console locale Spark - Scala).
7. Potrebbero venire visualizzate due finestre di dialogo in cui viene chiesto se si vuole correggere automaticamente le dipendenze. In caso affermativo, selezionare **Auto Fix** (Correggi automaticamente).

    ![IntelliJ IDEA: finestra di dialogo per correzione automatica Spark 1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA: finestra di dialogo per correzione automatica Spark 2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. La console dovrebbe essere simile all'immagine seguente. Nella finestra della console digitare `sc.appName` e quindi premere CTRL+INVIO. Verrà visualizzato il risultato. È possibile arrestare la console locale selezionando il pulsante rosso.

    ![IntelliJ IDEA: risultato nella console locale](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Console della sessione Spark Livy interattiva (Scala)

È supportata solo in IntelliJ 2018.2 e 2018.3.

1. Dalla barra dei menu passare a **Run** > **Edit Configurations...** (Esegui > Modifica configurazioni).

2. Dalla finestra **Run/Debug Configurations** (Esecuzione/Debug configurazioni) nel riquadro a sinistra, passare a **Apache Spark on Synapse** >  **[Spark on Synapse] myApp** (Apache Spark in Synapse > [Spark in Synapse] myApp).

3. Dalla finestra principale selezionare la scheda **Remotely Run in Cluster** (Esecuzione remota nel cluster).

4. Specificare i valori seguenti e quindi selezionare **OK** :

    |Proprietà |valore |
    |----|----|
    |Nome della classe principale| Selezionare il nome della classe principale.| 
    |Pool di Spark|Selezionare i pool di Spark in cui eseguire l'applicazione.|
    ||

    ![Impostazione della configurazione nella console interattiva](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. Da Project (Progetto) passare a **myApp** > **src** > **main** > **scala** > **myApp** .

6. Dalla barra dei menu passare a **Tools** > **Spark Console** > **Run Spark Livy Interactive Session Console(Scala)** (Strumenti > Console Spark > Esegui console della sessione Spark Livy interattiva -Scala).
7. La console dovrebbe essere simile all'immagine seguente. Nella finestra della console digitare `sc.appName` e quindi premere CTRL+INVIO. Verrà visualizzato il risultato. È possibile arrestare la console locale selezionando il pulsante rosso.

    ![IntelliJ IDEA: risultato nella console interattiva](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Inviare la selezione alla console Spark

È possibile scegliere di visualizzare il risultato dello script inviando codice alla console locale o alla console della sessione Livy interattiva (Scala). A tale scopo, è possibile evidenziare il codice nel file Scala e quindi fare clic con il pulsante destro del mouse su **Send Selection To Spark Console** (Invia selezione alla console Spark). Il codice selezionato verrà inviato alla console ed eseguito. Il risultato verrà visualizzato dopo il codice nella console. La console controllerà gli errori, se presenti.

   ![Inviare la selezione alla console Spark](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](../overview-what-is.md)
- [Creare un nuovo pool di Apache Spark per un'area di lavoro di Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
