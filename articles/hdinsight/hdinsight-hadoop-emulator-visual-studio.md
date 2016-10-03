<properties
pageTitle="Utilizzare gli strumenti di Microsoft Azure Data Lake per Visual Studio con Sandbox di Hortonworks | Microsoft Azure"
description="Informazioni su come usare gli strumenti di Azure Data Lake per VIsual Studio con Sandbox di Hortonworks (in esecuzione su una macchina virtuale locale). Grazie a questi strumenti è possibile creare ed eseguire i processi Hive e Pig in Sandbox, oltre a visualizzare l'output del processo e la cronologia."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>  

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/26/2016"
ms.author="larryfr"/>  

# Utilizzare gli strumenti di Azure Data Lake per Visual Studio con Sandbox di Hortonworks

Gli strumenti di Azure Data Lake per Visual Studio includono gli strumenti per utilizzare i cluster Hadoop generici, oltre agli strumenti per utilizzare Azure Data Lake e HDInsight. Questo documento fornisce i passaggi necessari per utilizzare gli strumenti di Azure Data Lake con Sandbox di Hortonworks in esecuzione su una macchina virtuale locale.

Sandbox di Hortonworks consente di utilizzare Hadoop in locale nell'ambiente di sviluppo. Dopo aver sviluppato una soluzione che si desidera distribuire in modo scalabile, è possibile passare a un cluster HDInsight.

## Prerequisiti

* Sandbox di Hortonworks in esecuzione su una macchina virtuale nell'ambiente di sviluppo. Questo documento è stato scritto e testato con Sandbox in esecuzione in Oracle VirtualBox, configurato con le informazioni contenute nel documento [Introduzione all'ecosistema Hadoop](hdinsight-hadoop-emulator-get-started.md).

* Visual Studio 2013 o 2015, qualsiasi edizione.

* [Azure SDK per .NET](https://azure.microsoft.com/downloads/) 2.7.1 o versioni successive

* [Strumenti di Azure Data Lake per Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)

## Configurare le password per Sandbox

Assicurarsi che Sandbox di Hortonworks sia in esecuzione, quindi seguire i passaggi indicati nell'[Introduzione all'ecosistema Hadoop](hdinsight-hadoop-emulator-get-started.md#set-passwords) per configurare la password dell'account `root` SSh e dell'account `admin` Ambari. Queste password verranno utilizzate quando ci si connette a Sandbox da Visual Studio.

## Connettere gli strumenti a Sandbox

1. Aprire Visual Studio e selezionare __Visualizza__ e __Esplora server__.

2. Da __Esplora server__, fare clic con il tasto destro del mouse sulla voce __HDInsight__ e selezionare __Connetti a HDInsight Emulator__.

    ![Connettersi a HDInsight Emulator](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)  

3. Dalla finestra di dialogo __Connetti a HDInsight Emulator__, immettere la password configurata per Ambari.

    ![Immettere la password Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)  

    Selezionare __Avanti__ per continuare.

4. Utilizzare il campo __Password__ per immettere la password configurata per l'account `root`. Mantenere i valori predefiniti per gli altri campi.

    ![Reimpostare la password radice](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)  

    Selezionare __Avanti__ per continuare.

5. Attendere il completamento della convalida dei servizi. In alcuni casi, la convalida potrebbe non riuscire e verrebbe chiesto di aggiornare la configurazione. In questa eventualità, selezionare il pulsante __Aggiorna__ e attendere il completamento della configurazione e della verifica del servizio.

    ![Errori e pulsante Aggiorna](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)  

    > [AZURE.NOTE] Il processo di aggiornamento utilizza Ambari per modificare la configurazione di Hortonworks Sandbox su quella prevista dagli strumenti di Azure Data Lake per Visual Studio.

    Al termine della convalida, selezionare __Fine__ per completare la configurazione.

    ![Terminare la connessione](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)  

    > [AZURE.NOTE] A seconda della velocità dell'ambiente di sviluppo e della quantità di memoria allocata sulla macchina virtuale, la configurazione e la convalida dei servizi potrebbe richiedere alcuni minuti.

Al termine della procedura indicata, si dispone di una voce "Cluster locale HDInsight" in Esplora server nella sezione HDInsight.

## Scrivere una query Hive

Hive fornisce un linguaggio di query simile a SQL (HiveQL) per l'utilizzo dei dati strutturati. Utilizzare la procedura seguente per informazioni su come eseguire query ad hoc rispetto al cluster locale.

1. In __Esplora server__, fare clic con il tasto destro del mouse sulla voce del cluster locale aggiunto in precedenza e selezionare __Scrivere una query Hive__.

    ![Scrivere una query Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)  

    Così facendo si apre una finestra di query che consente di digitare rapidamente e inviare una query al cluster locale.

2. Nella finestra delle query, immettere quanto segue:

        select count(*) from sample_08;
    
    Nella parte superiore della finestra di query, verificare di aver selezionato la configurazione del cluster locale e fare clic su __Invia__. Mantenere i valori predefiniti negli altri campi (__Batch__ e nome server).

    ![finestra di query e pulsante di invio](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)  

    Tenere presente che è inoltre possibile utilizzare il menu a discesa accanto a __Invia__ per selezionare __Avanzate__. Ciò consente di aprire una finestra di dialogo per fornire altre opzioni durante l'invio del processo.

    ![invio avanzato](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)  

3. Quando si invia la query, viene visualizzato lo stato del processo, che fornisce informazioni sul processo mentre viene elaborato da Hadoop. La voce __Stato processo__ indica lo stato corrente del processo. Lo stato verrà aggiornato periodicamente, oppure è possibile utilizzare l'icona Aggiorna per un aggiornamento manuale dello stato.

    ![Stato processo](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)  

    Una volta che __Stato processo__ diventa __Terminato__, viene visualizzato un grafo aciclico diretto (DAG), che descrive il percorso di esecuzione determinato da Tez (il motore di esecuzione predefinito di Hive nel cluster locale).
    
    > [AZURE.NOTE] Tez è anche il motore predefinito quando si utilizzano i cluster HDInsight basati su Linux. Non è il motore predefinito in HDInsight basato su Windows; per utilizzare Tez in questo ambiente, è necessario aggiungere la riga `set hive.execution.engine = tez;` all'inizio della query Hive.

    Usare il collegamento __Output processo__ per visualizzare l'output. In questo caso, è __823__; il numero di righe nella tabella sample\_08. È possibile visualizzare le informazioni di diagnostica relative al processo tramite i collegamenti __Log processo__ e __Scarica Log YARN__.

4. È inoltre possibile eseguire in modo interattivo i processi Hive modificando il campo __Batch__ su __Interattivo__, per poi selezionare __Esegui__.

    ![Query interattiva](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)  

    In questo modo il log di output generato durante l'elaborazione viene trasmesso alla finestra __Output di HiveServer2 __.
    
    > [AZURE.NOTE] Si tratta delle stesse informazioni disponibili dal collegamento __Log processo__ dopo il completamento del processo.

    ![Output di HiveServer2](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)  

## Creare un progetto Hive

Inoltre, è possibile creare un progetto che contiene più script Hive. Ciò è utile quando si hanno degli script correlati da tenere insieme o da conservare utilizzando un sistema di controllo delle versioni.

1. In Visual Studio, selezionare __File__, __Nuovo__ e \_\_Progetto\_\_.

2. Dall'elenco di progetti, espandere __Modelli__, __Azure Data Lake__ e selezionare __HIVE (HDInsight)__. Dall'elenco dei modelli selezionare __Hive Sample__. Immettere un nome e il percorso, quindi selezionare __OK__.

    ![Modello HIVE (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)  

Il progetto __Hive Sample__ contiene due script, __WebLogAnalysis.hql__ e __SensorDataAnalysis.hql__. È possibile inviarli utilizzando lo stesso pulsante __Invia__ nella parte superiore della finestra.

## Creare un progetto Pig

Mentre Hive offre un linguaggio simile a SQL per utilizzare i dati strutturati, Pig fornisce un linguaggio (Pig Latin) che consente di sviluppare una pipeline di trasformazioni da applicare ai dati. Attenersi alla seguente procedura per utilizzare Pig con il cluster locale.

1. Aprire Visual Studio e selezionare __File__, __Nuovo__ e __Progetto__. Dall'elenco di progetti, espandere __Modelli__, __Azure Data Lake__ e selezionare __Pig (HDInsight)__. Dall'elenco dei modelli selezionare __Applicazione Pig__. Immettere un nome, il percorso e selezionare __OK__.

    ![Progetto Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)  

2. Immettere quanto segue come contenuto del file __script.pig__ creato con questo progetto.

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

    Mentre Pig utilizza un linguaggio diverso da Hive, la modalità di esecuzione dei processi si mantiene coerente tra entrambi i linguaggi attraverso il pulsante __Invia__. Selezionando l'elenco a discesa il basso accanto a __Invia__ si apre una finestra di dialogo di invio avanzato per Pig.

    ![Invio avanzato Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)  
    
3. Anche lo stato del processo e l'output vengono visualizzati allo stesso modo di una query Hive.

    ![immagine di un processo pig completato](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)  

## Visualizzare i processi

Gli strumenti di Azure Data Lake consentono anche di visualizzare facilmente le informazioni sui processi che sono stati eseguiti in Hadoop. Utilizzare la procedura seguente per visualizzare i processi che sono stati eseguiti nel cluster locale.

1. Da __Esplora server__, fare clic con il pulsante destro del mouse sul cluster locale e selezionare __Visualizza processi__. per aprire l'elenco dei processi che sono stati inviati al cluster.

    ![Visualizzare i processi](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)  

2. Da questo elenco, selezionare un processo per visualizzarne i dettagli.

    ![selezionare un processo](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)  

    Le informazioni visualizzate sono simili a quelle che appaiono dopo l'esecuzione di una query Hive o Pig, complete dei collegamenti per visualizzare le informazioni relative al log e all'output.

3. Da qui si può anche modificare il processo e inviarlo di nuovo.

## Visualizzare i database Hive

1. In __Esplora server__, espandere la voce __Cluster locale di HDInsight__ e __Database Hive__. per visualizzare i database __Predefinito__ e __xademo__ nel cluster locale. Attraverso l'espansione di un database è possibile visualizzare le tabelle al suo interno.

    ![database espansi](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)  

2. L'espansione della consente di visualizzare le colonne presenti in essa. È possibile fare clic con il tasto destro del mouse su una tabella e selezionare __Visualizza le prime 100 righe__ per una visualizzazione rapida dei dati.

    ![visualizzazione di database hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)  

### Proprietà del database e della tabella

Si può notare che esiste la possibilità di scegliere di visualizzare __Proprietà__ in un database o una tabella, così da mostrare i dettagli per l'elemento selezionato nella finestra Proprietà.

![Proprietà](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)  

### Creare una tabella

Per creare una nuova tabella, fare clic con il tasto destro del mouse su un database e selezionare __Crea tabella__.

![Creare una tabella](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)  

È quindi possibile creare la tabella utilizzando un modulo. È possibile visualizzare il codice HiveQL non elaborato che verrà utilizzato per creare la tabella nella parte inferiore della pagina corrente.

![creare un modulo di tabella](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)  

## Passaggi successivi

* [Acquisire dimestichezza con Sandbox di Hortonworks](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Esercitazione di Hadoop: introduzione a HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

<!---HONumber=AcomDC_0921_2016-->