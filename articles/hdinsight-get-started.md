<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Introduzione all'uso di Hadoop in HDInsight | Azure" metaKeywords="" description="Introduzione all'uso di Hadoop in HDInsight: una soluzione Big Data. Informazioni sul provisioning di cluster, sull'esecuzione di processi Hive e sull'output dei dati in Excel per l'analisi." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduzione all'uso di Hadoop in HDInsight" authors="nitinme" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="nitinme" />

# Introduzione all'uso di Hadoop in HDInsight

<!--div class="dev-center-tutorial-selector sublanding"> <a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a> <a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a> <!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a> </div-->

HDInsight rende Apache Hadoop, un framework software MapReduce, disponibile in un ambiente di Azure più semplice, scalabile ed efficiente a livello di costi. HDInsight offre inoltre un approccio economicamente vantaggioso alla gestione e all'archiviazione di dati tramite l'archivio BLOB di Azure.

> [WACOM.NOTE] Se non si è esperti di Hadoop e Big Data, è consigliabile ottenere maggiori informazioni sui termini [Apache Hadoop][Apache Hadoop], [MapReduce][MapReduce], [HDFS][HDFS] e [Hive][Hive]. Per informazioni sull'abilitazione di Hadoop in Azure tramite HDInsight, vedere [Introduzione a Hadoop in HDInsight][Introduzione a Hadoop in HDInsight].

In concomitanza con la fase GA (General Availability) di Azure HDInsight, Microsoft offre anche HDInsight Emulator per Azure, noto in precedenza come *Microsoft HDInsight Developer Preview*. L'emulatore è stato progettato per scenari relativi allo sviluppo e supporta solo distribuzioni a nodo singolo. Per informazioni sull'uso di HDInsight Emulator, vedere [Introduzione a HDInsight Emulator][Introduzione a HDInsight Emulator].

> [WACOM.NOTE] Per istruzioni sul provisioning di un cluster HBase, vedere [Provisioning di cluster HBase in HDInsight][Provisioning di cluster HBase in HDInsight]. Per informazioni utili per scegliere tra Hadoop e HBase, vedere l'articolo che illustra le [differenze tra Hadoop e HBase][differenze tra Hadoop e HBase].

## Quali risultati si ottengono con questa esercitazione?

Si supponga di avere un set di dati non strutturati di grandi dimensioni e di volere eseguire query sul set di dati per estrarre informazioni significative. Questo è l'obiettivo dell'esercitazione. Il risultato sarà ottenuto nel modo seguente:

![HDI.GetStartedFlow][HDI.GetStartedFlow]

È anche possibile guardare un video dimostrativo relativo a questa esercitazione:

<center>
<iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen>
</iframe>
</center>
<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->

**Prerequisiti:**

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   Una sottoscrizione di Azure. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][Opzioni di acquisto], [Offerte per i membri][Offerte per i membri] oppure [Versione di valutazione gratuita][Versione di valutazione gratuita].
-   Un computer con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'esercitazione:

-   [Creare un account di archiviazione di Azure][Creare un account di archiviazione di Azure]
-   [Provisioning di un cluster HDInsight][Provisioning di un cluster HDInsight]
-   [Eseguire gli esempi dal portale][Eseguire gli esempi dal portale]
-   [Eseguire un processo Hive][Eseguire un processo Hive]
-   [Passaggi successivi][Passaggi successivi]

## <a name="storage"></a>Creare un account di archiviazione di Azure

HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

Quando si esegue il provisioning di un cluster HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account è designato come file system predefinito, come in HDFS. Per impostazione predefinita, il provisioning del cluster HDInsight è eseguito nello stesso data center che include l'account di archiviazione specificato.

Oltre a questo account di archiviazione, è possibile aggiungere altri account di archiviazione durante la configurazione personalizzata di un cluster HDInsight. L'account di archiviazione aggiuntivo può appartenere alla stessa sottoscrizione di Azure o a sottoscrizioni di Azure diverse. Per istruzioni, vedere [Provisioning di cluster HDInsight con opzioni personalizzate][Provisioning di cluster HDInsight con opzioni personalizzate].

Per semplificare questa esercitazione, verranno usati solo il contenitore BLOB predefinito e l'account di archiviazione predefinito. In pratica, i file di dati sono in genere archiviati in un account di archiviazione designato.

**Per creare un account di archiviazione di Azure**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **NEW** nell'angolo inferiore sinistro, selezionare **DATA SERVICES**, quindi **STORAGE** e infine **QUICK CREATE**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  Immettere i valori desiderati per **URL**, **LOCATION** e **REPLICATION**, quindi fare clic su **CREATE STORAGE ACCOUNT**. I gruppi di affinità non sono supportati. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.

    > [WACOM.NOTE] L'opzione Creazione rapida per il provisioning di un cluster HDInsight, come quello usato in questa esercitazione, non richiede un percorso durante il provisioning del cluster. Il cluster è invece posizionato per impostazione predefinita nello stesso data center usato per l'account di archiviazione. Assicurarsi quindi di creare l'account di archiviazione nei percorsi supportati per il cluster, ovvero nei percorsi seguenti: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali**, **Stati Uniti centro-meridionali**.

4.  Attendere che il valore di **STATUS** per il nuovo account di archiviazione venga modificato in **Online**.
5.  Selezionare il nuovo account di archiviazione dall'elenco, quindi fare clic su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore della pagina.
6.  Prendere nota dei valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA**. È possibile usare indifferentemente una delle due chiavi. Sarà necessario usarli più avanti nell'esercitazione.

Per altre informazioni, vedere
[Come creare un account di archiviazione][Come creare un account di archiviazione] e [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

## <a name="provision"></a>Provisioning di un cluster HDInsight

Quando si esegue il provisioning di un cluster HDInsight, si esegue il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. In questa sezione si esegue il provisioning di un cluster HDInsight versione 3,1, basato su Hadoop versione 2,4. È anche possibile creare cluster Hadoop per altre versioni usando il portale di Azure, i cmdlet di HDInsight PowerShell oppure usando .NET SDK per HDInsight. Per istruzioni, vedere [Provisioning di cluster HDInsight con opzioni personalizzate][Provisioning di cluster HDInsight con opzioni personalizzate]. Per informazioni sulle diverse versioni di HDInsight e i relativi contratti di servizio, vedere la pagina relativa al [controllo delle versioni del componente HDInsight][controllo delle versioni del componente HDInsight].

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Per eseguire il provisioning di un cluster HDInsight**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic su **HDInsight** a sinistra per elencare lo stato dei cluster disponibili nell'account. Nella schermata seguente non è disponibile alcun cluster HDInsight.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  Fare clic su **NUOVO** nell'angolo inferiore sinistro, quindi su **Servizi dati**, **HDInsight** e infine su **Hadoop**.

    ![HDI.QuickCreateCluster][HDI.QuickCreateCluster]

4.  Immettere o selezionare i valori seguenti:

    | Nome                     | Valore                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    |--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome del cluster         | Nome del cluster                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
    | Dimensione del cluster   | Numero di nodi di dati che si vuole distribuire. Il valore predefinito è 4, ma nell'elenco a discesa è disponibile anche l'opzione relativa ai nodi di dati 1 o 2. Quando si usa l'opzione **Creazione personalizzata** è possibile specificare un numero qualsiasi di nodi del cluster. Sono disponibili i dettagli sui prezzi relativi alle tariffe di fatturazione per le diverse dimensioni di cluster. Fare clic sul simbolo **?** immediatamente sopra la casella a discesa, quindi selezionare il collegamento disponibile nel popup. |
    | Password                 | Password per l'account *admin*. Il nome "admin" per l'utente cluster viene specificato quando non si usa l'opzione **Creazione personalizzata**. Si noti che NON si tratta dell'account Administrator di Windows per le VM in cui è eseguito il provisioning dei cluster. È possibile modificare questo valore solo se si usa la procedura guidata **Creazione personalizzata**.                                                                                                                                                             |
    | Account di archiviazione | Selezionare l'account di archiviazione creato dalla casella a discesa.                                                                                                                                                                                                                                                                                                                                                                                                                                                                       
                                 Dopo la selezione, non sarà possibile modificare l'account di archiviazione. In caso di rimozione dell'account di archiviazione, il cluster non sarà più disponibile per l'uso. Il cluster HDInsight è posizionato nello stesso data center dell'account di archiviazione.                                                                                                                                                                                                                                                                   |

    Annotare il nome del cluster. Sarà necessario usarlo più avanti nell'esercitazione.

5.  Fare clic su **Creazione del cluster HDInsight**. Al termine del provisioning, nella colonna relativa allo stato verrà visualizzato il valore **In esecuzione**.

    > [WACOM.NOTE] La procedura precedente permette di creare cluster con HDInsight versione 3.1. Per creare altre versioni di cluster, usare il metodo di creazione personalizzata dal portale di gestione oppure Azure PowerShell. Per informazioni sulle differenze tra ogni versione di cluster, vedere [Novità delle versioni cluster incluse in HDInsight][Novità delle versioni cluster incluse in HDInsight] Per informazioni sull'uso dell'opzione **CREAZIONE PERSONALIZZATA**, vedere [Provisioning di cluster HDInsight con opzioni personalizzate][Provisioning di cluster HDInsight con opzioni personalizzate].

## <a name="sample"></a>Eseguire gli esempi dal portale

Un cluster HDInsight il cui provisioning è stato eseguito correttamente fornisce una Console query per l'esecuzione di esempi direttamente dal portale. Gli esempi possono essere usati per apprendere come lavorare con HDInsight seguendo le procedure dettagliare di alcuni scenari di base. Gli esempi forniscono tutti i componenti necessari, come i dati per l'analisi e le query da eseguire sui dati.

**Per eseguire l'esempio**, dal portale di gestione di Azure fare clic sul nome del cluster sul quale eseguire l'esempio, quindi fare clic su **Console query** nella parte inferiore della pagina. Nella pagina Web che viene aperta, fare clic sulla scheda **Guida introduttiva**, quindi, sotto la categoria **Esempi**, fare clic sull'esempio da eseguire. Seguire le istruzioni nella pagina Web per completare l'esempio. Per saperne di più sulla funzione di ognuno degli esempi, fare clic sui collegamenti sottostanti.

| Esempio                          | Funzione                                                                                                                                                                                                                                                                                                |
|----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Analisi di dati del sensore][Analisi di dati del sensore]  | Uso di HDInsight per l'elaborazione dei dati cronologici prodotti dai sistemi HVAC (Heating, Ventilating and Air Conditioning, ossia riscaldamento, ventilazione e condizionamento dell'aria) per identificare sistemi che non sono in grado di mantenere in modo affidabile una temperatura impostata. |
| [Analisi del log del sito Web][Analisi del log del sito Web] | Uso di HDInsight per analizzare i log dei siti Web e ottenere informazioni dettagliate sulla frequenza delle visite di siti Web esterni nell'arco di un giorno nonché per visualizzare un riepilogo degli errori relativi ai siti Web incontrati dagli utenti.                                          |

## <a name="hivequery"></a>Eseguire una query HIVE dal portale

Dopo il provisioning di un cluster HDInsight, verrà eseguito un processo Hive per eseguire query in una tabella Hive di esempio, *hivesampletable*, inclusa nei cluster HDInsight. La tabella contiene dati relativi a produttori di dispositivi mobili, piattaforme e modelli. Verranno eseguite query in questa tabella per recuperare i dati per i dispositivi mobili di un produttore specifico.

**Per eseguire un processo Hive dal dashboard del cluster**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster creati, incluso il cluster creato nella sezione precedente.
3.  Fare clic sul nome del cluster in cui si vuole eseguire il processo Hive, quindi fare clic su **CONSOLE QUERY** nella parte inferiore della pagina.
4.  Verrà aperta una pagina Web in una scheda diversa del browser. Immettere l'account utente e la password per Hadoop. Il nome utente predefinito è **admin**. La password corrisponde a quella immessa durante il provisioning del cluster. L'aspetto del dashboard è simile al seguente:

    ![hdi.dashboard][hdi.dashboard]

    Nella parte superiore sono disponibili diverse schede. La scheda predefinita è **Editor Hive** e le altre schede sono **Cronologia processo** e **Browser file**. Usando il dashboard, è possibile inviare query Hive, controllare i log dei processi Hadoop ed esplorare i file WASB.

    > [WACOM.NOTE] Si noti che l'URL della pagina Web è *\<NomeCluster\>.azurehdinsight.net*. Invece di aprire il dashboard dal portale di gestione, è possibile aprirlo da un Web browser usando l'URL.

5.  Nella scheda **Editor Hive** in **Nome query** immettere **HTC20**. Il nome della query corrisponde al nome del processo.

6.  Nel riquadro della query immettere la query seguente:

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

7.  Fare clic su **Submit**. Per ottenere i risultati sono necessari alcuni minuti. La schermata viene aggiornata ogni 30 secondi. È anche possibile fare clic su **Aggiorna** per aggiornare la schermata.

    Al termine, la schermata avrà un aspetto analogo al seguente:

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

8.  Fare clic sul nome della query disponibile nella schermata per visualizzate l'output. Annotare il valore per **Ora inizio processo (UTC)**. Sarà necessario più avanti.

    ![hdi.dashboard.query.select.result.output][hdi.dashboard.query.select.result.output]

    Questa pagina mostra anche i valori per **Output processo** e **Registro processo**. Sarà anche possibile scaricare il file di output (\_stdout) e il file di log (\_stderr).

    > [WACOM.NOTE] La tabella **Sessione processo** nella scheda **Editor Hive** elenca i processi completi o in esecuzione fintanto che la scheda rimane visualizzata. Se si esce dalla pagina, nella tabella non saranno elencati processi. La scheda **Cronologia processo** include un elenco di tutti i processi, completati o in esecuzione.

**Per passare al file di output**

1.  Nel dashboard del cluster fare clic su **Browser file** nella parte superiore della pagina.
2.  Fare clic sul nome dell'account di archiviazione, quindi sul nome del contenitore (che corrisponde al nome del cluster) e infine su **utente**.
3.  Fare clic su **admin** e quindi sul GUID la cui modifica più recente è stata eseguita poco dopo l'ora di avvio del processo annotata in precedenza. Annotare il valore relativo al GUID. Sarà necessario nella sezione successiva.

    ![hdi.dashboard.query.browse.output][hdi.dashboard.query.browse.output]

### <a name="powerquery"></a>Connettersi agli strumenti di business intelligence Microsoft

È possibile usare il componente aggiuntivo Power Query per Microsoft Excel per importare l'output del processo da HDInsight in Excel, dove si possono usare gli strumenti di business intelligence (BI) Microsoft per un'analisi più approfondita dei risultati.

Per completare questa parte dell'esercitazione, è necessario disporre di Excel 2010 o 2013.

**Per scaricare Microsoft PowerQuery per Excel**

-   Scaricare Microsoft PowerQuery per Microsoft Excel dall'[Area download Microsoft][Area download Microsoft] e installarlo.

**Per importare dati di HDInsight**

1.  Aprire Excel e creare una nuova cartella di lavoro vuota.
2.  Nel menu **Power Query** scegliere **Da altre origini**, quindi fare clic su **Da Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  Immettere il **Nome account** dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**. Si tratta dell'account di archiviazione creato in precedenza nell'esercitazione.
4.  Immettere la **Chiave account** per l'account di archiviazione BLOB di Azure, quindi fare clic su **Salva**.
5.  Nel riquadro Strumento di navigazione a destra fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster.

6.  Individuare **stdout** nella colonna **Nome**. Assicurarsi che il GUID nella colonna del **Percorso cartella** corrispondente sia uguale al GUID annotato in precedenza. Una corretta corrispondenza indica che i dati di output corrispondono al processo inviato. Fare clic su **Binario** a sinistra di **stdout**.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  Fare clic su **Carica e chiudi** nell'angolo superiore sinistro per importare l'output del processo Hive in Excel.

## <a name="nextsteps"></a>Passaggi successivi

In questa esercitazione si è appreso come eseguire il provisioning di un cluster con HDInsight, eseguire un processo MapReduce su tale cluster e importare i risultati in Excel per un'ulteriore elaborazione e per la visualizzazione grafica mediante gli strumenti di Business Intelligence. Per altre informazioni, vedere gli articoli seguenti:

-   [Introduzione all'uso di HDInsight Emulator][Introduzione a HDInsight Emulator]
-   [Uso dell'archiviazione BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight]
-   [Amministrazione di HDInsight tramite PowerShell][Amministrazione di HDInsight tramite PowerShell]
-   [Caricare i dati in HDInsight][Caricare i dati in HDInsight]
-   [Uso di MapReduce con HDInsight][Uso di MapReduce con HDInsight]
-   [Usare Hive con HDInsight][Usare Hive con HDInsight]
-   [Usare Pig con HDInsight][Usare Pig con HDInsight]
-   [Usare Oozie con HDInsight][Usare Oozie con HDInsight]
-   [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight][Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight]
-   [Sviluppare programmi MapReduce Java per HDInsight][Sviluppare programmi MapReduce Java per HDInsight]

  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [MapReduce]: http://go.microsoft.com/fwlink/?LinkId=510086
  [HDFS]: http://go.microsoft.com/fwlink/?LinkId=510087
  [Hive]: http://go.microsoft.com/fwlink/?LinkId=510085
  [Introduzione a Hadoop in HDInsight]: ../hdinsight-hadoop-introduction/
  [Introduzione a HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [Provisioning di cluster HBase in HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-get-started/
  [differenze tra Hadoop e HBase]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [Opzioni di acquisto]: http://azure.microsoft.com/it-it/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/it-it/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [Creare un account di archiviazione di Azure]: #storage
  [Provisioning di un cluster HDInsight]: #provision
  [Eseguire gli esempi dal portale]: #sample
  [Eseguire un processo Hive]: #hivequery
  [Passaggi successivi]: #nextsteps
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Provisioning di cluster HDInsight con opzioni personalizzate]: ../hdinsight-provision-clusters/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Come creare un account di archiviazione]: ../storage-create-storage-account/
  [controllo delle versioni del componente HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.QuickCreateCluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
  [Novità delle versioni cluster incluse in HDInsight]: ../hdinsight-component-versioning/
  [Analisi di dati del sensore]: ../hdinsight-hive-analyze-sensor-data/
  [Analisi del log del sito Web]: ../hdinsight-hive-analyze-website-log/
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [hdi.dashboard.query.select.result.output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
  [hdi.dashboard.query.browse.output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
  [Area download Microsoft]: http://www.microsoft.com/it-it/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Caricare i dati in HDInsight]: ../hdinsight-upload-data/
  [Uso di MapReduce con HDInsight]: ../hdinsight-use-mapreduce
  [Usare Hive con HDInsight]: ../hdinsight-use-hive/
  [Usare Pig con HDInsight]: ../hdinsight-use-pig/
  [Usare Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Sviluppare programmi per la creazione di flussi Hadoop in C# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Sviluppare programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
