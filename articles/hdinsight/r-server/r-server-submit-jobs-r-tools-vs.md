---
title: Inviare processi da R Tools per Visual Studio - Azure HDInsight
description: Inviare processi R dal computer Visual Studio locale a un cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 1323e46d734d047f68652d0b21902e03182e6b62
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130246"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Inviare processi da R Tools per Visual Studio

[R Tools per Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) è un'estensione gratuita e open source per le edizioni Community (gratuita), Professional ed Enterprise sia di [Visual Studio 2017](https://www.visualstudio.com/downloads/) che di [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129) o versioni successive. RTVS non è disponibile per [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

RTVS migliora il flusso di lavoro R offrendo strumenti come la [finestra R interattivo](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (completamento del codice), [visualizzazione tracciato](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) tramite librerie R come ggplot2 e ggviz, [debug del codice R](https://docs.microsoft.com/visualstudio/rtvs/debugging)e altro ancora.

## <a name="set-up-your-environment"></a>Configurazione dell'ambiente

1. Installare [R Tools per Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Installazione di RTVS in Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selezionare il carico di lavoro *Analisi scientifica dei dati e applicazioni analitiche*, quindi selezionare le opzioni **Supporto del linguaggio R**, **Supporto runtime per lo sviluppo R** e  **Microsoft R Client**.

3. È necessario disporre di chiavi pubbliche e private per l'autenticazione SSH.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installare [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) nel computer. ML Server offre le funzioni [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) e `RxSpark`.

5. Installare [PuTTY](https://www.putty.org/) per fornire un contesto di calcolo per eseguire le funzioni `RevoScaleR` dal client locale al cluster HDInsight.

6. È possibile applicare le Impostazioni di Data Science all'ambiente di Visual Studio, ottenendo un nuovo layout per l'area di lavoro per gli strumenti R.
   1. Per salvare le impostazioni correnti di Visual Studio, usare il comando **Strumenti > Importa/Esporta impostazioni**, quindi selezionare **Esporta le impostazioni di ambiente selezionate** e specificare un nome file. Per ripristinare tali impostazioni, usare lo stesso comando e selezionare **Importa le impostazioni di ambiente selezionate**.

   2. Passare alla voce di menu **Strumenti R**, quindi selezionare **Impostazioni di Data Science**.

       ![Impostazioni di Data Science di Visual Studio](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > Usando l'approccio del passaggio 1 è possibile anche salvare e ripristinare il layout personalizzato, piuttosto che ripetere il comando **Impostazioni di Data Science**.

## <a name="execute-local-r-methods"></a>Eseguire i metodi R locali

1. Creare il cluster HDInsight ML Services.
2. Installare l'[estensione RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Scaricare il [file zip di esempi](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Aprire `examples/Examples.sln` per avviare la soluzione in Visual Studio.
5. Aprire il file `1-Getting Started with R.R` nella cartella `A first look at R`.
6. Partendo dall'inizio del file, premere Ctrl + Invio per inviare ogni riga, una alla volta, nella finestra R interattivo. Alcune righe potrebbero richiedere un po' di tempo a causa dell'installazione di pacchetti.
    * In alternativa, è possibile selezionare tutte le righe nel file R (Ctrl + A), quindi eseguirle tutte (Ctrl + Invio) oppure selezionare l'icona Esegui in finestra interattiva sulla barra degli strumenti.

        ![Esecuzione interattiva di Visual Studio](./media/r-server-submit-jobs-r-tools-vs/execute-interactive1.png)

7. Dopo aver eseguito tutte le righe dello script, verrà visualizzato un output simile al seguente:

    ![Strumenti R dell'area di lavoro di Visual Studio](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Inviare processi a un cluster HDInsight ML Services

Usando Microsoft ML Server/Microsoft R Client da un computer Windows dotato di PuTTY, è possibile creare un contesto di calcolo che eseguirà funzioni `RevoScaleR` distribuite dal client locale al cluster HDInsight. Usare `RxSpark` per creare il contesto di calcolo, specificare il nome utente, il nodo perimetrale del cluster Apache Hadoop, gli switch SSH e così via.

1. L'indirizzo del nodo perimetrale di ml Services `CLUSTERNAME-ed-ssh.azurehdinsight.net` in `CLUSTERNAME` HDInsight è dove è il nome del cluster di servizi ml.

1. Incollare il codice seguente nella finestra R interattivo in Visual Studio, modificando i valori delle variabili di installazione in base all'ambiente.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
          sshHostname = mySshHostname,
          sshSwitches = mySshSwitches,
          sshProfileScript = mySshProfileScript,
          consoleOutput = TRUE,
          hdfsShareDir = myHdfsShareDir,
          shareDir = myShareDir,
          sshClientDir = mySshClientDir
    )

    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```

   ![impostazione del contesto per Apache Spark](./media/r-server-submit-jobs-r-tools-vs/apache-spark-context.png)

1. Nella finestra R interattivo, eseguire i comandi seguenti:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    L'output dovrebbe essere simile al seguente:

    ![Esecuzione](./media/r-server-submit-jobs-r-tools-vs/successful-rx-commands.png) comando RX riuscita a
1. Verificare che `rxHadoopCopy` abbia copiato correttamente il file `people.json` dalla cartella di dati di esempio nella cartella `/user/RevoShare/newUser` appena creata:

    1. Nel riquadro del cluster HDInsight ML Services in Azure selezionare **Account di archiviazione** dal menu a sinistra.

        ![Account di Azure Archiviazione HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdinsight-storage-accounts.png)

    2. Selezionare l'account di archiviazione predefinito per il cluster, prendendo nota del nome del contenitore o della directory.

    3. Selezionare **Contenitori** dal menu a sinistra nel riquadro dell'account di archiviazione.

        ![Contenitori di Azure Archiviazione HDInsight](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Selezionare il nome del contenitore del cluster, scegliere la cartella **utente** (potrebbe essere necessario fare clic su *Carica altri* nella parte inferiore dell'elenco), quindi selezionare *RevoShare*, quindi **newUser**. Il file `people.json` dovrebbe comparire nella cartella `newUser`.

        ![HDInsight percorso cartella file copiato](./media/r-server-submit-jobs-r-tools-vs/hdinsight-copied-file.png)

1. Dopo aver terminato di usare il contesto Apache Spark corrente, è necessario arrestarlo. Non è possibile eseguire contemporaneamente più contesti.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Opzioni del contesto di calcolo per ML Services in HDInsight](r-server-compute-contexts.md)
* [La combinazione di ScaleR e SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fornisce un esempio di stime di ritardo dei voli di una compagnia aerea.
