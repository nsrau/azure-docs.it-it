---
title: Inviare processi da R Tools per Visual Studio - Azure HDInsight | Microsoft Docs
description: Inviare processi R dal computer Visual Studio locale a un cluster HDInsight.
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 1a82ba7790f739768156a8bee33a74d7130e24e1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Inviare processi da R Tools per Visual Studio

[R Tools per Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) è un'estensione gratuita e open source per le edizioni Community (gratuita), Professional ed Enterprise sia di [Visual Studio 2017](https://www.visualstudio.com/downloads/) che di [Visual Studio 2015 Update 3](http://go.microsoft.com/fwlink/?LinkId=691129) o versioni successive.

RTVS migliora il flusso di lavoro R offrendo strumenti come la [finestra R interattivo](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (completamento del codice), [visualizzazione tracciato](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) tramite librerie R come ggplot2 e ggviz, [debug del codice R](https://docs.microsoft.com/visualstudio/rtvs/debugging)e altro ancora.

## <a name="set-up-your-environment"></a>Configurazione dell'ambiente

1. Installare [R Tools per Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Installazione di RTVS in Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Selezionare il carico di lavoro *Analisi scientifica dei dati e applicazioni analitiche*, quindi selezionare le opzioni **Supporto del linguaggio R**, **Supporto runtime per lo sviluppo R** e  **Microsoft R Client**.

3. È necessario disporre di chiavi pubbliche e private per l'autenticazione SSH.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installare [R Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) sul computer. R Server offre le funzioni [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) e `RxSpark`.

5. Installare [PuTTY](http://www.putty.org/) per fornire un contesto di calcolo per eseguire le funzioni `RevoScaleR` dal client locale al cluster HDInsight.

6. È possibile applicare le Impostazioni di Data Science all'ambiente di Visual Studio, ottenendo un nuovo layout per l'area di lavoro per gli strumenti R.
    1. Per salvare le impostazioni correnti di Visual Studio, usare il comando **Strumenti > Importa/Esporta impostazioni**, quindi selezionare **Esporta le impostazioni di ambiente selezionate** e specificare un nome file. Per ripristinare tali impostazioni, usare lo stesso comando e selezionare **Importa le impostazioni di ambiente selezionate**.

    2. Passare alla voce di menu **Strumenti R**, quindi selezionare **	Impostazioni di Data Science**.

        ![Impostazioni di Data Science](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > Usando l'approccio del passaggio 1 è possibile anche salvare e ripristinare il layout personalizzato, piuttosto che ripetere il comando **Impostazioni di Data Science**.

## <a name="execute-local-r-methods"></a>Eseguire i metodi R locali

1. Creare il [cluster HDInsight Server R](r-server-get-started.md).
2. Installare l'[estensione RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Scaricare il [file zip di esempi](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Aprire `examples/Examples.sln` per avviare la soluzione in Visual Studio.
5. Aprire il file `1-Getting Started with R.R` nella cartella `A first look at R`.
6. Partendo dall'inizio del file, premere Ctrl + Invio per inviare ogni riga, una alla volta, nella finestra R interattivo. Alcune righe potrebbero richiedere un po' di tempo a causa dell'installazione di pacchetti.
    * In alternativa, è possibile selezionare tutte le righe nel file R (Ctrl + A), quindi eseguirle tutte (Ctrl + Invio) oppure selezionare l'icona Esegui in finestra interattiva sulla barra degli strumenti.
        ![Esegui in finestra interattiva](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Dopo aver eseguito tutte le righe dello script, verrà visualizzato un output simile al seguente:

    ![Impostazioni di Data Science](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-r-cluster"></a>Inviare processi a un cluster R HDInsight

Usando Microsoft R Server/Microsoft R Client da un computer Windows dotato di PuTTY, è possibile creare un contesto di calcolo che eseguirà funzioni `RevoScaleR` distribuite dal client locale al cluster HDInsight. Usare `RxSpark` per creare il contesto di calcolo, specificare il nome utente, il nodo perimetrale del cluster Hadoop, gli switch SSH e così via.

1. Per trovare il nome host del nodo perimetrale, aprire il riquadro del cluster HDInsight R in Azure, quindi selezionare **Secure Shell (SSH)** nel menu in alto del riquadro Panoramica.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Copiare il valore relativo al **nome host del nodo perimetrale**.

    ![Nome host del nodo perimetrale](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Incollare il codice seguente nella finestra R interattivo in Visual Studio, modificando i valori delle variabili di installazione in base all'ambiente.

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
    
    ![Impostazione del contesto di Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. Nella finestra R interattivo, eseguire i comandi seguenti:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    L'output dovrebbe essere simile al seguente:

    ![Esecuzione del comando rx avvenuta con esito positivo](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Verificare che `rxHadoopCopy` abbia copiato correttamente il file `people.json` dalla cartella di dati di esempio nella cartella `/user/RevoShare/newUser` appena creata:

    1. Dal riquadro del cluster R HDInsight in Azure, selezionare **Account di archiviazione** dal menu a sinistra.

        ![Account di archiviazione](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Selezionare l'account di archiviazione predefinito per il cluster, prendendo nota del nome del contenitore o della directory.

    3. Selezionare **Contenitori** dal menu a sinistra nel riquadro dell'account di archiviazione.

        ![Contenitori](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Selezionare il nome del contenitore del cluster, scegliere la cartella **utente** (potrebbe essere necessario fare clic su *Carica altri* nella parte inferiore dell'elenco), quindi selezionare *RevoShare*, quindi **newUser**. Il file `people.json` dovrebbe comparire nella cartella `newUser`.

        ![File copiato](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Dopo aver terminato di usare il contesto Spark corrente, è necessario arrestarlo. Non è possibile eseguire contemporaneamente più contesti.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)](r-server-compute-contexts.md)
* [La combinazione di ScaleR e SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fornisce un esempio di stime di ritardo dei voli di una compagnia aerea.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
