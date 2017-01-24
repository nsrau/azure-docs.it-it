---
title: Usare Microsoft Azure Data Lake Tools per Visual Studio con Sandbox di Hortonworks | Documentazione Microsoft
description: "Informazioni su come usare gli strumenti di Azure Data Lake per VIsual Studio con Sandbox di Hortonworks (in esecuzione su una macchina virtuale locale). Grazie a questi strumenti è possibile creare ed eseguire i processi Hive e Pig in Sandbox, oltre a visualizzare l&quot;output del processo e la cronologia."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4b0572d8fb3b81fd4bd2a34794141f137b50eed0
ms.openlocfilehash: 42f53e0c725ed7744c2e3d919c960c42e4cebc0c


---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Utilizzare gli strumenti di Azure Data Lake per Visual Studio con Sandbox di Hortonworks

Gli strumenti di Azure Data Lake per Visual Studio includono gli strumenti per utilizzare i cluster Hadoop generici, oltre agli strumenti per utilizzare Azure Data Lake e HDInsight. Questo documento fornisce i passaggi necessari per utilizzare gli strumenti di Azure Data Lake con Sandbox di Hortonworks in esecuzione su una macchina virtuale locale.

Sandbox di Hortonworks consente di utilizzare Hadoop in locale nell'ambiente di sviluppo. Dopo aver sviluppato una soluzione che si desidera distribuire in modo scalabile, è possibile passare a un cluster HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Sandbox di Hortonworks in esecuzione su una macchina virtuale nell'ambiente di sviluppo. Questo documento è stato scritto e testato con Sandbox in esecuzione in Oracle VirtualBox, configurato con le informazioni contenute nel documento [Introduzione all'ecosistema Hadoop](hdinsight-hadoop-emulator-get-started.md) .

* Visual Studio 2013 o 2015, qualsiasi edizione.

* [Azure SDK per .NET](https://azure.microsoft.com/downloads/) 2.7.1 o versioni successive.

* [Azure Data Lake Tools per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configurare le password per Sandbox

Assicurarsi che Sandbox di Hortonworks sia in esecuzione, quindi seguire la procedura descritta nell'[introduzione all'ecosistema Hadoop](hdinsight-hadoop-emulator-get-started.md#set-sandbox-passwords) per configurare la password dell'account `root` SSH e dell'account `admin` Ambari. Queste password verranno utilizzate quando ci si connette a Sandbox da Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Connettere gli strumenti a Sandbox

1. Aprire Visual Studio e selezionare **Visualizza** e quindi **Esplora server**.
2. In **Esplora server** fare clic con il pulsante destro del mouse sulla voce **HDInsight** e quindi scegliere **Connect to HDInsight Emulator** (Connetti a HDInsight Emulator).

    ![Connetti a HDInsight Emulator](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Nella finestra di dialogo **Connect to HDInsight Emulator** (Connetti a HDInsight Emulator) immettere la password configurata per Ambari.

    ![Immettere la password Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Selezionare **Avanti** per continuare.

4. Usare il campo **Password** per immettere la password configurata per l'account `root`. Mantenere i valori predefiniti per gli altri campi.

    ![Reimpostare la password radice](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Selezionare **Avanti** per continuare.

5. Attendere il completamento della convalida dei servizi. In alcuni casi, la convalida potrebbe non riuscire e verrebbe chiesto di aggiornare la configurazione. In questa eventualità, selezionare il pulsante **Aggiorna** e attendere il completamento della configurazione e della verifica del servizio.

    ![Errori e pulsante Aggiorna](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > Il processo di aggiornamento utilizza Ambari per modificare la configurazione di Hortonworks Sandbox su quella prevista dagli strumenti di Azure Data Lake per Visual Studio.

    Al termine della convalida, selezionare **Fine** per completare la configurazione.

    ![Terminare la connessione](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [!NOTE]
    > A seconda della velocità dell'ambiente di sviluppo e della quantità di memoria allocata sulla macchina virtuale, la configurazione e la convalida dei servizi potrebbe richiedere alcuni minuti.

Al termine della procedura indicata, si dispone di una voce "Cluster locale HDInsight" in Esplora server nella sezione HDInsight.

## <a name="write-a-hive-query"></a>Scrivere una query Hive

Hive fornisce un linguaggio di query simile a SQL (HiveQL) per l'utilizzo dei dati strutturati. Utilizzare la procedura seguente per informazioni su come eseguire query ad hoc rispetto al cluster locale.

1. In **Esplora server** fare clic con il pulsante destro del mouse sulla voce del cluster locale aggiunto in precedenza e quindi scegliere **Scrivi una query Hive**.

    ![Scrivere una query Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Così facendo si apre una finestra di query che consente di digitare rapidamente e inviare una query al cluster locale.

2. Nella finestra delle query, immettere quanto segue:

        select count(*) from sample_08;

    Nella parte superiore della finestra di query, verificare che sia selezionata la configurazione del cluster locale e quindi fare clic su **Invia**. Mantenere i valori predefiniti negli altri campi (**Batch** e nome del server).

    ![finestra di query e pulsante di invio](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Tenere presente che è anche possibile usare il menu a discesa accanto a **Invia** per selezionare **Avanzate**. Ciò consente di aprire una finestra di dialogo per fornire altre opzioni durante l'invio del processo.

    ![invio avanzato](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Quando si invia la query, viene visualizzato lo stato del processo, che fornisce informazioni sul processo mentre viene elaborato da Hadoop. La voce **Stato processo** indica lo stato corrente del processo. Lo stato verrà aggiornato periodicamente, oppure è possibile utilizzare l'icona Aggiorna per un aggiornamento manuale dello stato.

    ![Stato processo](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Quando **Stato processo** diventa **Terminato**, viene visualizzato un grafo aciclico diretto (DAG), che descrive il percorso di esecuzione determinato da Tez (il motore di esecuzione predefinito di Hive nel cluster locale).

    > [!NOTE]
    > Tez è anche il motore predefinito quando si utilizzano i cluster HDInsight basati su Linux. Non è il motore predefinito in HDInsight basato su Windows; per utilizzare Tez in questo ambiente, è necessario aggiungere la riga `set hive.execution.engine = tez;` all'inizio della query Hive.

    Usare il collegamento **Job Output** (Output processo) per visualizzare l'output. In questo caso è **823**, il numero di righe nella tabella sample_08. È possibile visualizzare le informazioni di diagnostica relative al processo usando i collegamenti **Job Log** (Log processo) e **Download YARN Log** (Scarica log YARN).

4. È anche possibile eseguire in modo interattivo i processi Hive impostando il campo **Batch** su **Interattivo** e quindi selezionando **Esegui**.

    ![Query interattiva](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    In questo modo il log di output generato durante l'elaborazione viene trasmesso alla finestra **Output HiveServer2**.

    > [!NOTE]
    > Si tratta delle stesse informazioni accessibili dal collegamento **Job Log** (Log processo) dopo il completamento del processo.

    ![Output di HiveServer2 ](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Creare un progetto Hive

Inoltre, è possibile creare un progetto che contiene più script Hive. Ciò è utile quando si hanno degli script correlati da tenere insieme o da conservare utilizzando un sistema di controllo delle versioni.

1. In Visual Studio selezionare **File**, **Nuovo** e quindi Progetto.

2. Nell'elenco dei progetti espandere **Modelli** e **Azure Data Lake** e quindi selezionare **HIVE (HDInsight)**. Nell'elenco dei modelli selezionare **Hive Sample** (Esempio Hive). Immettere un nome e un percorso e quindi selezionare **OK**.

    ![Modello HIVE (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

Il progetto **Hive Sample** (Esempio Hive) contiene due script, **WebLogAnalysis.hql** e **SensorDataAnalysis.hql**. È possibile inviare entrambi usando lo stesso pulsante **Invia** nella parte superiore della finestra.

## <a name="create-a-pig-project"></a>Creare un progetto Pig

Mentre Hive offre un linguaggio simile a SQL per utilizzare i dati strutturati, Pig fornisce un linguaggio (Pig Latin) che consente di sviluppare una pipeline di trasformazioni da applicare ai dati. Attenersi alla seguente procedura per utilizzare Pig con il cluster locale.

1. Aprire Visual Studio e selezionare **File** **Nuovo** e quindi **Progetto**. Nell'elenco dei progetti espandere **Modelli** e **Azure Data Lake** e quindi selezionare **Pig (HDInsight)**. Nell'elenco dei modelli selezionare **Pig Application** (Applicazione Pig). Immettere un nome e un percorso e quindi selezionare **OK**.

    ![Progetto Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Immettere quanto segue come contenuto del file **script.pig** creato con questo progetto.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Nonostante Pig usi un linguaggio diverso rispetto a Hive, la modalità di esecuzione dei processi si mantiene coerente tra entrambi i linguaggi tramite il pulsante **Invia**. Selezionando l'elenco a discesa accanto a **Invia** viene visualizzata una finestra di dialogo di invio avanzato per Pig.

    ![Invio avanzato Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Anche lo stato del processo e l'output vengono visualizzati allo stesso modo di una query Hive.

    ![immagine di un processo pig completato](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Visualizzare i processi

Gli strumenti di Azure Data Lake consentono anche di visualizzare facilmente le informazioni sui processi che sono stati eseguiti in Hadoop. Utilizzare la procedura seguente per visualizzare i processi che sono stati eseguiti nel cluster locale.

1. In **Esplora server** fare clic con il pulsante destro del mouse sul cluster locale e quindi scegliere **Visualizza processi** per aprire l'elenco dei processi che sono stati inviati al cluster.

    ![Visualizzare i processi](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Da questo elenco, selezionare un processo per visualizzarne i dettagli.

    ![selezionare un processo](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Le informazioni visualizzate sono simili a quelle che appaiono dopo l'esecuzione di una query Hive o Pig, complete dei collegamenti per visualizzare le informazioni relative al log e all'output.

3. Da qui si può anche modificare il processo e inviarlo di nuovo.

## <a name="view-hive-databases"></a>Visualizzare i database Hive

1. In **Esplora server** espandere la voce **HDInsight local cluster** (Cluster locale HDInsight) e quindi **Hive Databases** (Database Hive). Verranno così visualizzati i database **Predefinito** e **xademo** nel cluster locale. Attraverso l'espansione di un database è possibile visualizzare le tabelle al suo interno.

    ![database espansi](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. L'espansione della consente di visualizzare le colonne presenti in essa. È possibile fare clic con il pulsante destro del mouse su una tabella e scegliere **Visualizza prime 100 righe** per visualizzare rapidamente i dati.

    ![visualizzazione di database hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Proprietà del database e della tabella

Si sarà notato che è possibile scegliere di visualizzare **Proprietà** per un database o una tabella, così da mostrare i dettagli per l'elemento selezionato nella finestra Proprietà.

![Properties](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Creare una tabella

Per creare una nuova tabella, fare clic con il pulsante destro del mouse su un database e quindi scegliere **Crea tabella**.

![Crea tabella](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

È quindi possibile creare la tabella utilizzando un modulo. È possibile visualizzare il codice HiveQL non elaborato che verrà utilizzato per creare la tabella nella parte inferiore della pagina corrente.

![creare un modulo di tabella](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Passaggi successivi

* [Acquisire dimestichezza con Sandbox di Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Esercitazione di Hadoop: introduzione a HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)



<!--HONumber=Jan17_HO2-->


