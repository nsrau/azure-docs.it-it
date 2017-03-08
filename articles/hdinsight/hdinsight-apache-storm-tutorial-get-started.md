---
title: 'Esercitazione di Apache Storm: Introduzione a Storm | Documentazione Microsoft'
description: Introduzione all&quot;analisi di Big Data mediante Apache Storm ed esempi di Storm Starter in HDInsight. Informazioni su come usare Storm per elaborare i dati in tempo reale.
keywords: apache storm,esercitazione su apache storm,analisi Big Data,storm starter
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6f93f7ff-0736-4708-80ef-4289dae532a9
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: e505d02895abd011661b3e4f66c7f4f7ea042358
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Introduzione all'analisi di Big Data tramite esempi di Storm Starter in HDInsight

Apache Storm è un sistema di calcolo in tempo reale scalabile, a tolleranza di errore e distribuito per l'elaborazione di flussi di dati. Con Storm in Microsoft Azure HDInsight è possibile creare un cluster Storm basato sul cloud che esegue analisi di Big Data in tempo reale. 

> [!IMPORTANT]
> I passaggi descritti in questo articolo si basano sull'uso di un cluster HDInsight basato su Windows. HDInsight è disponibile in Windows solo per le versioni precedenti a HDInsight 3.4. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).
>
> Per i passaggi per creare uno Storm basato su Linux nel cluster HDInsight, vedere [Esercitazione Apache Storm: Iniziare a utilizzare l'esempio Storm Starter mediante l’analisi di dati in HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md)

## <a name="prerequisites"></a>Prerequisiti
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per completare correttamente questa esercitazione di Apache Storm, è necessario quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Creare un cluster di Storm

Per creare uno Storm nel cluster HDInsight, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **+ Nuovo**, **Intelligence e analisi** e quindi selezionare **HDInsight**.
   
    ![Creazione di un cluster HDInsight](./media/hdinsight-apache-storm-tutorial-get-started/create-hdinsight.png)

2. Nel pannello **Informazioni di base** immettere le informazioni seguenti:

    * **Nome del cluster**: nome del cluster HDInsight.
    * **Sottoscrizione**: selezionare la sottoscrizione da usare.
    * **Nome utente dell'account di accesso del cluster** e **Password dell'account di accesso del cluster**: account di accesso usato per il cluster su HTTPS. Queste credenziali vengono usate per accedere a servizi quali l'interfaccia utente Web di Ambari o l'API REST.
    * **Nome utente Secure Shell (SSH)**: lasciare l'impostazione predefinita per questi campi. Non vengono usati nei cluster HDInsight basati su Windows.
    * **Gruppo di risorse**: il gruppo di risorse nel quale viene creato il cluster.
    * **Posizione**: area di Azure in cui creare il cluster.
   
    ![Selezionare la sottoscrizione](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-basic-configuration.png)

3. Selezionare **Tipo di cluster**, quindi impostare i valori seguenti nel pannello **Configurazione cluster**:
   
    * **Tipo di cluster**: Storm

    * **Sistema operativo**: Windows

    * **Versione**: Storm 0.10.0 (HDI 3.3)

        > [!NOTE]
        > HDInsight 3.4 e le sue versioni successive sono disponibili solo con il sistema operativo Linux.

    * **Livello cluster**: Standard
     
    Usare infine il pulsante **Seleziona** per salvare le impostazioni.
     
    ![Selezionare il tipo di cluster](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-cluster-type.png)

4. Dopo avere selezionato il tipo di cluster, usare il pulsante __Seleziona__ per impostare il tipo di cluster. Usare quindi il pulsante __Avanti__ per completare la configurazione di base.

5. Nel pannello **Archiviazione** selezionare o creare un account di archiviazione. Per la procedura illustrata in questo documento, non modificare i valori predefiniti degli altri campi nel pannello. Usare il pulsante __Avanti__ per salvare la configurazione della risorsa di archiviazione.

    ![Configurare le impostazioni dell'account di archiviazione per HDInsight](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-storage-account.png)

6. Nel pannello **Riepilogo** esaminare la configurazione per il cluster. Usare i collegamenti __Modifica__ per cambiare eventuali impostazioni non corrette. Usare infine il pulsante __Crea__ per creare il cluster.
   
    ![Riepilogo della configurazione del cluster](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > La creazione del cluster può richiedere fino a 20 minuti.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Eseguire un esempio Storm Starter in HDInsight
Questa esercitazione di Apache Storm presenta l'analisi di Big Data mediante esempi di Storm Starter in GitHub.

Ogni cluster Storm in HDInsight viene fornito con Storm Dashboard, usato per caricare ed eseguire topologie Storm nel cluster. Ogni cluster viene inoltre fornito con topologie di esempio che possono essere eseguite direttamente da Storm Dashboard.

### <a id="connect"></a>Connettersi al dashboard
Il dashboard si trova in **https://&lt;nomecluster>.azurehdinsight.net//**, dove **nomecluster** è il nome del cluster. È anche possibile trovare un collegamento al dashboard selezionando il cluster dalla Schermata iniziale e il collegamento **Dashboard** nella parte superiore del pannello.

![Portale di Azure con collegamento a Storm Dashboard](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [!NOTE]
> Quando si esegue la connessione al dashboard, viene chiesto di immettere un nome utente e una password. Immettere il nome amministratore (**admin**) e la password usati per la creazione del cluster.
> 
> 

Dopo aver caricato Storm Dashboard, verrà visualizzato il modulo **Submit Topology** .

![Inviare la topologia di Storm Starter con Storm Dashboard.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

Il modulo **Invia topologia** può essere usato per caricare ed eseguire file con estensione jar contenenti topologie Storm. Include inoltre diversi esempi di base forniti con il cluster.

### <a id="run"></a>Eseguire l'esempio relativo al conteggio parole dal progetto Storm Starter in GitHub
Gli esempi forniti con il cluster includono diverse varianti di una topologia di conteggio parole. Questi esempi includono uno **spout** che genera frasi in modo casuale e **bolt** che suddividono ogni frase in singole parole e quindi contano le occorrenze di ciascuna di esse. Questi esempi sono compresi negli [esempi di Storm Starter](https://github.com/apache/storm/tree/master/examples/storm-starter), che fanno parte di Apache Storm.

Per eseguire un esempio di Storm Starter, seguire questa procedura:

1. Selezionare **StormStarter - WordCount** dall'elenco a discesa **File JAR**. Nei campi **Nome classe** e **Parametri aggiuntivi** verranno inseriti i parametri relativi a questo esempio.
   
    ![Storm Starter WordCount selezionato in Storm Dashboard.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)
   
   * **Class Name** : nome della classe nel file con estensione jar che invia la topologia.
   * **Additional Parameters** : qualsiasi parametro necessario per la topologia. In questo esempio il campo viene usato per fornire un nome descrittivo della topologia inviata.
2. Fare clic su **Invia**. Dopo qualche secondo, nel campo **Risultato** viene visualizzato il comando usato per inviare il processo nonché i risultati di tale comando. Il campo **Errore** visualizza eventuali errori verificatisi durante l'invio della topologia.
   
    ![Pulsante Submit e risultati di Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)
   
   > [!NOTE]
   > I risultati non indicano che la topologia è stata completata. **Dopo essere stata avviata, una topologia Storm viene eseguita fino a quando non viene arrestata.** La topologia relativa al conteggio parole genera frasi casuali e, fino a quando non viene arrestata, conta le occorrenze di ogni parola.
   > 
   > 

### <a id="monitor"></a>Monitorare la topologia
L'interfaccia utente di Storm consente di monitorare la topologia.

1. Selezionare **Storm UI** nella parte superiore di Storm Dashboard. Verranno visualizzate informazioni di riepilogo relative al cluster e a tutte le topologie in esecuzione.
   
    ![Storm Dashboard con il riepilogo della topologia di Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)
   
    Dalla pagina sopra descritta è possibile visualizzare l'intervallo di tempo per cui la topologia è stata attiva, nonché il numero di thread di lavoro, esecutori e attività usati.
   
   > [!NOTE]
   > La colonna **Nome** contiene il nome descrittivo fornito prima nel campo **Parametri aggiuntivi**.
   > 
   > 
2. Nella sezione **Topology summary** (Riepilogo topologie) selezionare la voce **wordcount** (conteggio parole) nella colonna **Nome**. Verranno visualizzate altre informazioni sulla topologia.
   
    ![Storm Dashboard con informazioni sulla topologia di Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)
   
    In questa pagina sono disponibili le informazioni seguenti:
   
   * **Topology stats** : informazioni di base sulle prestazioni della topologia, organizzate in intervalli di tempo.
     
     > [!NOTE]
     > La selezione di un intervallo di tempo specifico determina la modifica dell'intervallo di tempo relativo a informazioni visualizzate in altre sezioni della pagina.
     > 
     > 
   * **Spouts** : informazioni di base sugli spout, incluso l'ultimo errore restituito da ciascuno di essi.
   * **Bolts** : informazioni di base sui bolt.
   * **Topology configuration** : informazioni dettagliate sulla configurazione della topologia.
     
     Questa pagina fornisce anche azioni che possono essere eseguite sulla topologia:
   * **Activate** : riprende l'elaborazione di una topologia disattivata.
   * **Deactivate** : sospende una topologia in esecuzione.
   * **Rebalance** : regola il parallelismo della topologia. È necessario ribilanciare le topologie in esecuzione dopo aver modificato il numero di nodi del cluster. Questo consente alla topologia di regolare il parallelismo per compensare l'aumento o la diminuzione del numero di nodi del cluster. Per altre informazioni, vedere l'articolo relativo al [parallelismo di una topologia Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
   * **Kill** : arresta una topologia Storm dopo il timeout specificato.
3. In questa pagina selezionare una voce nella sezione **Spouts** o **Bolts**. Verranno visualizzate informazioni relative al componente selezionato.
   
    ![Storm Dashboard con informazioni sui componenti selezionati.](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)
   
    In questa pagina vengono visualizzate le informazioni seguenti:
   
   * **Spout/Bolt stats** : informazioni di base sulle prestazioni, organizzate in intervalli di tempo.
     
     > [!NOTE]
     > La selezione di un intervallo di tempo specifico determina la modifica dell'intervallo di tempo relativo a informazioni visualizzate in altre sezioni della pagina.
     > 
     > 
   * **Input stats** (solo bolt): informazioni sui componenti che generano dati utilizzati dal bolt.
   * **Output stats** : informazioni sui dati generati dal bolt.
   * **Executors** : informazioni sulle istanze del componente.
   * **Errors** : errori generati dal componente.
4. Quando si visualizzano i dettagli di uno spout o di un bolt, selezionare una voce nella colonna **Porta** della sezione **Esecutori** per visualizzare i dettagli relativi a una specifica istanza del componente.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    Da questi dati è possibile osservare che la parola **seven** è stata rilevata 1.493.957 volte. In altri termini, il numero&14939;57 indica le occorrenze della parola dall'avvio della topologia.

### <a name="stop-the-topology"></a>Arrestare la topologia
Tornare alla pagina **Topology summary** (Riepilogo topologie) per la topologia relativa al conteggio parole e quindi selezionare **Termina** nella sezione **Topology actions** (Azioni di topologia). Quando richiesto, immettere 10 per il numero di secondi di attesa prima dell'arresto della topologia. Dopo il periodo di timeout, la topologia non viene più visualizzata nella sezione **Interfaccia utente di Storm** del dashboard.

## <a name="delete-the-cluster"></a>Eliminazione del cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="summary"></a>Riepilogo
In questa esercitazione di Apache Storm si è usato Storm Starter per apprendere come creare un cluster Storm in HDInsight e usare Storm Dashboard per distribuire, monitorare e gestire topologie Storm.

## <a id="next"></a>Passaggi successivi
* **Strumenti HDInsight per Visual Studio**: gli strumenti di HDInsight consentono di usare Visual Studio per inviare, monitorare e gestire topologie Storm in modo simile a quanto avviene in Storm Dashboard, descritto prima. HDInsight Tools offre inoltre la possibilità di creare topologie Storm C#. Include infine topologie di esempio che è possibile distribuire ed eseguire sul cluster.
  
    Per altre informazioni, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio per eseguire una query Hive](hdinsight-hadoop-visual-studio-tools-get-started.md).
* **File di esempio**: nella directory **%STORM_HOME%\contrib** del cluster Storm in HDInsight sono disponibili diversi esempi. Ogni esempio deve contenere quanto riportato di seguito:
  
  * Il codice sorgente, ad esempio storm-starter-0.9.1.2.1.5.0-2057-sources.jar
  * I documenti Java, ad esempio storm-starter-0.9.1.2.1.5.0-2057-javadocs.jar
  * L'esempio, ad esempio storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar
    
    Usare il comando "jar" per estrarre il codice sorgente o i documenti Java, ad esempio "jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar".
    
    > [!NOTE]
    > I documenti Java sono costituiti da pagine Web. Dopo aver estratto i file, usare un browser per visualizzare il file **index.html** .
    > 
    > 
    
    Per accedere a questi esempi, è necessario abilitare Desktop remoto per Storm nel cluster HDInsight e quindi copiare i file da **%STORM_HOME%\contrib**.
* Il documento seguente contiene un elenco di altri esempi che è possibile usare con Storm in HDInsight:
  
  * [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

