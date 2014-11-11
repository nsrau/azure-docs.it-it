<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Introduzione all'utilizzo di Hadoop 2.2 in HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Introduzione all'utilizzo di Hadoop 2.4 in HDInsight">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Introduzione all'utilizzo di Hadoop 2.2 in HDInsight" class="current">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a-->
</div>

HDInsight rende Apache Hadoop, un framework software MapReduce, disponibile in un ambiente di Azure più semplice, scalabile ed efficiente a livello di costi. HDInsight offre inoltre un approccio economicamente vantaggioso alla gestione e all'archiviazione di dati tramite l'archivio BLOB di Azure.

> [WACOM.NOTE] Se non si è esperti di Hadoop e Big Data, è consigliabile ottenere maggiori informazioni sui termini [Apache Hadoop][Apache Hadoop], [MapReduce][MapReduce], [HDFS][HDFS] e [Hive][Hive].

In concomitanza con la fase GA (General Availability) di Azure HDInsight, Microsoft offre anche HDInsight Emulator per Azure, noto in precedenza come *Microsoft HDInsight Developer Preview*. L'emulatore è stato progettato per scenari relativi allo sviluppo e supporta solo distribuzioni a nodo singolo. Per informazioni sull'utilizzo di HDInsight Emulator, vedere [Introduzione a HDInsight Emulator][Introduzione a HDInsight Emulator].

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

-   Una sottoscrizione di Azure. Per ulteriori informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][Opzioni di acquisto], [Offerte per i membri][Offerte per i membri] oppure [Versione di valutazione gratuita][Versione di valutazione gratuita].
-   Un computer con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

**Tempo previsto per il completamento:** 30 minuti

## Contenuto dell'esercitazione:

-   [Creare un account di archiviazione di Azure][Creare un account di archiviazione di Azure]
-   [Provisioning di un cluster HDInsight][Provisioning di un cluster HDInsight]
-   [Eseguire un processo Hive][Eseguire un processo Hive]
-   [Connettersi agli strumenti di Microsoft Business Intelligence][Connettersi agli strumenti di Microsoft Business Intelligence]
-   [Passaggi successivi][Passaggi successivi]

## <a name="storage"></a>Creare un account di archiviazione di Azure

HDInsight usa l'archivio BLOB di Azure per l'archiviazione dei dati. Questa risorsa è denominata *WASB* o *Archiviazione BLOB di Azure*. WASB è l'implementazione Microsoft di HDFS sull'archivio BLOB di Azure. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight][Utilizzo dell'archivio BLOB di Azure con HDInsight].

Quando si esegue il provisioning di un cluster HDInsight, si specifica un account di archiviazione di Azure. Un contenitore di archiviazione BLOB specifico dell'account è designato come file system predefinito, come in HDFS. Per impostazione predefinita, il provisioning del cluster HDInsight è eseguito nello stesso data center che include l'account di archiviazione specificato.

Oltre a questo account di archiviazione, è possibile aggiungere altri account di archiviazione durante la configurazione personalizzata di un cluster HDInsight. L'account di archiviazione aggiuntivo può appartenere alla stessa sottoscrizione di Azure o a sottoscrizioni di Azure diverse. Per istruzioni, vedere [Provisioning di cluster HDInsight con opzioni personalizzate][Provisioning di cluster HDInsight con opzioni personalizzate].

Per semplificare questa esercitazione, verranno usati solo il contenitore BLOB predefinito e l'account di archiviazione predefinito. In pratica, i file di dati sono in genere archiviati in un account di archiviazione designato.

**Per creare un account di archiviazione di Azure**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **NEW** nell'angolo inferiore sinistro, selezionare **DATA SERVICES**, quindi **STORAGE** e infine **QUICK CREATE**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  Immettere i valori desiderati per **URL**, **LOCATION** e **REPLICATION**, quindi fare clic su **CREATE STORAGE ACCOUNT**. I gruppi di affinità non sono supportati. Il nuovo account di archiviazione verrà incluso nell'elenco di archiviazione.

    > [WACOM.NOTE] Un cluster HDInsight e l'account di archiviazione di Azure associato devono trovarsi nello stesso data center. Assicurarsi quindi di creare l'account di archiviazione nei percorsi supportati per il cluster, ovvero nei percorsi seguenti: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali**, **Stati Uniti centro-meridionali**.

4.  Attendere che il valore di **STATUS** per il nuovo account di archiviazione venga modificato in **Online**.
5.  Selezionare il nuovo account di archiviazione dall'elenco, quindi fare clic su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore della pagina.
6.  Prendere nota dei valori di **NOME ACCOUNT DI ARCHIVIAZIONE** e **CHIAVE DI ACCESSO PRIMARIA** o **CHIAVE DI ACCESSO SECONDARIA**. È possibile usare indifferentemente una delle due chiavi. Sarà necessario utilizzarli più avanti nell'esercitazione.

Per altre informazioni, vedere
[Come creare un account di archiviazione][Come creare un account di archiviazione] e [Usare l'archivio BLOB di Azure con HDInsight][Utilizzo dell'archivio BLOB di Azure con HDInsight].

## <a name="provision"></a>Provisioning di un cluster HDInsight

Quando si esegue il provisioning di un cluster HDInsight, si esegue il provisioning delle risorse di calcolo di Azure che includono Hadoop e le applicazioni correlate. In questa sezione si esegue il provisioning di un cluster HDInsight versione 3.0, basato su Hadoop versione 2.2. Per eseguire il provisioning di un cluster HDInsight con Hadoop versione 2.4, fare clic sulla scheda relativa alla versione specifica all'inizio di questo articolo. È anche possibile creare cluster Hadoop per altre versioni usando i cmdlet di HDInsight PowerShell oppure usando .NET SDK per HDInsight. Per istruzioni, vedere [Provisioning di cluster HDInsight con opzioni personalizzate][Provisioning di cluster HDInsight con opzioni personalizzate]. Per informazioni sulle diverse versioni di HDInsight e i relativi contratti di servizio, vedere la pagina relativa al [controllo delle versioni del componente HDInsight][controllo delle versioni del componente HDInsight].

**Per eseguire il provisioning di un cluster HDInsight**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic su **HDInsight** a sinistra per elencare lo stato dei cluster disponibili nell'account. Nella schermata seguente non è disponibile alcun cluster HDInsight.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  Fare clic su **NUOVO** nell'angolo inferiore sinistro, quindi su **Servizi dati**, **HDInsight** e infine su **Creazione personalizzata**.

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

    Immettere o selezionare i valori come illustrato nell'immagine precedente, quindi fare clic sulla freccia destra.

4.  Nella pagina **Configura cluster** immettere o selezionare i valori seguenti:

    | Nome               | Valore                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    |--------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nodi di dati       | Numero di nodi di dati che si desidera distribuire. Ai fini di test, creare un cluster a singolo nodo.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     
                          Il limite relativo alle dimensioni del cluster dipende dalla sottoscrizione di Azure. Per aumentare il limite, contattare il team del supporto fatturazione di Azure.                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | Area/Rete virtuale | Selezionare la stessa area geografica specificata per l'account di archiviazione creato nell'ultima procedura. HDInsight richiede che l'account di archiviazione si trovi nella stessa area geografica. Più avanti nella configurazione sarà possibile selezionare un account di archiviazione situato nella stessa area geografica specificata qui. Le aree geografiche disponibili sono: **Asia orientale**, **Asia sudorientale**, **Europa settentrionale**, **Europa occidentale**, **Stati Uniti orientali**, **Stati Uniti occidentali**, **Stati Uniti centro-settentrionali**, **Stati Uniti centro-meridionali** |

    Fare clic sulla freccia destra.

5.  Nella pagina **Configura utente cluster** digitare o scegliere il valore seguente:

    ![HDI.CustomCreateCluster.ClusterUser][HDI.CustomCreateCluster.ClusterUser]

    | Proprietà                  | Valore                                                                                                                                                                                                                                                                                                             |
    |----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome utente                | Specificare il nome utente del cluster HDInsight.                                                                                                                                                                                                                                                                  |
    | Password/Conferma password | Specificare la password utente del cluster HDInsight.                                                                                                                                                                                                                                                              |
    | Enter Hive/Oozie Metastore | Selezionare questa casella di controllo per specificare un database SQL nello stesso data center del cluster, da usare come metastore Hive/Oozie. Ciò risulta utile se si vogliono conservare i metadati sui progetti Hive/Oozie anche dopo aver eliminato un cluster.                                             |
    | Database metastore         | Specificare il database SQL di Azure che verrà utilizzato come metastore per Hive/OOzie. Questo database SQL deve trovarsi nello stesso data center del cluster HDInsight. Nella casella di riepilogo vengono elencati solo i database SQL nello stesso data center specificato nella pagina **Dettagli cluster**. |
    | Utente database            | Specificare il database SQL che verrà utilizzato per connettersi al database.                                                                                                                                                                                                                                      |
    | Password utente database   | Specificare la password utente del database SQL.                                                                                                                                                                                                                                                                   |

    > [WACOM.NOTE] Il database SQL usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Windows Azure** fare clic su **Sì** e infine su **Salva**.

    Fare clic sulla freccia destra.

6.  Nella pagina **Account di archiviazione** indicare il valore seguente:

    ![HDI.CustomCreateCluster.StorageAccount][HDI.CustomCreateCluster.StorageAccount]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Proprietà</th>
    <th align="left">Valore</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Storage Account</td>
    <td align="left">Specificare l'account di archiviazione di Azure che verrà usato come file system predefinito per il cluster HDInsight. È possibile scegliere una delle tre opzioni seguenti:
    <ul>
    <li>Use Existing Storage</li>
    <li>Create New Storage</li>
    <li>Use Storage From Another Subscription</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Nome account</td>
    <td align="left"><ul>
    <li>Se si sceglie di usare le risorse di archiviazione esistenti, per <strong>Nome account</strong> selezionare un account di archiviazione esistente. Nella casella di riepilogo vengono elencati solo gli account di archiviazione che si trovano nello stesso data center in cui si è scelto di effettuare il provisioning del cluster..</li>
    <li>Se si sceglie l'opzione <strong>Crea nuova archiviazione</strong> o <strong>Utilizzare l'archiviazione da un'altra sottoscrizione</strong> è necessario indicare il nome dell'account di archiviazione.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">Chiave account</td>
    <td align="left">Se si sceglie l'opzione <strong>Utilizza l'archiviazione da un'altra sottoscrizione</strong> specificare la chiave dell'account relativa a tale account di archiviazione.</td>
    </tr>
    <tr class="even">
    <td align="left">Contenitore predefinito</td>
    <td align="left"><p>Consente di specificare il contenitore predefinito nell'account di archiviazione viene usato come file system predefinito per il cluster HDInsight. Se si sceglie <strong>Utilizzare l'archiviazione esistente</strong> per il campo <strong>Account di archiviazione</strong> e non ci sono contenitori in tale account, il contenitore viene creato per impostazione predefinita con lo stesso nome del cluster. Se esiste già un contenitore con il nome del cluster, al nome del contenitore verrà aggiunto un numero di sequenza. Ad esempio, mycontainer1, mycontainer2 e così via. Tuttavia, se un account di archiviazione esistente ha un contenitore con un nome differente da quello del cluster specificato, sarà possibile usare anche tale contenitore.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Account di archiviazione aggiuntivi</td>
    <td align="left">HDInsight supporta più account di archiviazione. Un cluster può utilizzare un numero illimitato di account di archiviazione aggiuntivi. Se tuttavia si crea un cluster mediante il portale di gestione, esiste un limite di sette dovuto ai vincoli dell'interfaccia utente. Per ogni account di archiviazione aggiuntivo specificato viene aggiunta un'ulteriore pagina Account di archiviazione alla procedura guidata, in cui è possibile specificare le informazioni account. Nella schermata precedente, ad esempio, è selezionato un account di archiviazione aggiuntivo, per cui la pagina 5 viene aggiunta alla finestra di dialogo.</td>
    </tr>
    </tbody>
    </table>

    Se si è optato per gli account di archiviazione aggiuntivi, fare clic sulla freccia destra. In caso contrario, fare clic sul segno di spunta per avviare il provisioning del cluster. Al termine del provisioning, nella colonna relativa allo stato verrà visualizzato il valore **In esecuzione**.

7.  Se si è optato per l'account di archiviazione aggiuntivo, immettere le relative informazioni nella pagina **Account di archiviazione**:

    ![HDI.CustomCreateCluster.AddOnStorage][HDI.CustomCreateCluster.AddOnStorage]

    Anche in questo caso, è possibile scegliere risorse di archiviazione esistenti, creare nuove risorse di archiviazione o usare le risorse di archiviazione di un'altra sottoscrizione di Azure. La procedura che consente di ottenere i valori è simile al passaggio precedente.

    Fare clic sul segno di spunta per avviare il provisioning del cluster. Al termine del provisioning, nella colonna relativa allo stato verrà visualizzato il valore **In esecuzione**.

## <a name="sample"></a>Eseguire un processo Hive

Dopo il provisioning di un cluster HDInsight, verrà eseguito un processo Hive per eseguire query in una tabella Hive di esempio, *hivesampletable*, inclusa nei cluster HDInsight. La tabella contiene dati relativi a produttori di dispositivi mobili, piattaforme e modelli. Verranno eseguite query in questa tabella per recuperare i dati per i dispositivi mobili di un produttore specifico.

**Per eseguire un processo Hive dal dashboard del cluster**

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **HDINSIGHT** nel riquadro sinistro. Verrà visualizzato un elenco di cluster creati, incluso il cluster creato nella sezione precedente.
3.  Fare clic sul nome del cluster in cui si vuole eseguire il processo Hive, quindi fare clic su **GESTIONE DEL CLUSTER** nella parte inferiore della pagina.
4.  Verrà aperta una pagina Web in una scheda diversa del browser. Immettere l'account utente e la password per Hadoop. Il nome utente predefinito è **admin**. La password corrisponde a quella immessa durante il provisioning del cluster. Il dashboard ha un aspetto analogo al seguente:

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
3.  Fare clic su admin e quindi sul numero di GUID la cui modifica più recente è stata eseguita poco dopo l'ora di avvio del processo annotata in precedenza. Annotare il valore relativo al GUID. Sarà necessario nella sezione successiva.

    ![hdi.dashboard.query.browse.output][hdi.dashboard.query.browse.output]

## <a name="powerquery"></a>Connettersi agli strumenti di business intelligence Microsoft

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

6.  Individuare **stdout** nella colonna **Nome**. Assicurarsi che il GUID nella colonna Percorso cartella corrispondente sia uguale al GUID annotato in precedenza. Fare clic su **Binario** a sinistra di **stdout**.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  Fare clic su **Carica e chiudi** nell'angolo superiore sinistro per importare l'output del processo Hive in Excel.

## <a name="nextsteps"></a>Passaggi successivi

In questa esercitazione si è appreso come eseguire il provisioning di un cluster con HDInsight, eseguire un processo MapReduce su tale cluster e importare i risultati in Excel per un'ulteriore elaborazione e per la visualizzazione grafica mediante gli strumenti di Business Intelligence. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione a HDInsight Emulator][Introduzione a HDInsight Emulator]
-   [Utilizzo dell'archivio BLOB di Azure con HDInsight][Utilizzo dell'archivio BLOB di Azure con HDInsight]
-   [Amministrazione di HDInsight tramite PowerShell][Amministrazione di HDInsight tramite PowerShell]
-   [Caricamento di dati in HDInsight][Caricamento di dati in HDInsight]
-   [Utilizzo di MapReduce con HDInsight][Utilizzo di MapReduce con HDInsight]
-   [Utilizzo di Hive con HDInsight][Utilizzo di Hive con HDInsight]
-   [Utilizzo di Pig con HDInsight][Utilizzo di Pig con HDInsight]
-   [Utilizzo di Oozie con HDInsight][Utilizzo di Oozie con HDInsight]
-   [Sviluppo di programmi per la creazione di flussi Hadoop in C# per HDInsight][Sviluppo di programmi per la creazione di flussi Hadoop in C# per HDInsight]
-   [Sviluppo di programmi MapReduce Java per HDInsight][Sviluppo di programmi MapReduce Java per HDInsight]

  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [MapReduce]: http://go.microsoft.com/fwlink/?LinkId=510086
  [HDFS]: http://go.microsoft.com/fwlink/?LinkId=510087
  [Hive]: http://go.microsoft.com/fwlink/?LinkId=510085
  [Introduzione a HDInsight Emulator]: ../hdinsight-get-started-emulator/
  [Provisioning di cluster HBase in HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-hbase-get-started/
  [differenze tra Hadoop e HBase]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [Opzioni di acquisto]: http://azure.microsoft.com/it-it/pricing/purchase-options/
  [Offerte per i membri]: http://azure.microsoft.com/it-it/pricing/member-offers/
  [Versione di valutazione gratuita]: http://azure.microsoft.com/it-it/pricing/free-trial/
  [Creare un account di archiviazione di Azure]: #storage
  [Provisioning di un cluster HDInsight]: #provision
  [Eseguire un processo Hive]: #sample
  [Connettersi agli strumenti di Microsoft Business Intelligence]: #powerquery
  [Passaggi successivi]: #nextsteps
  [Utilizzo dell'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Provisioning di cluster HDInsight con opzioni personalizzate]: ../hdinsight-provision-clusters/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Come creare un account di archiviazione]: ../storage-create-storage-account/
  [controllo delle versioni del componente HDInsight]: http://azure.microsoft.com/it-it/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [hdi.dashboard.query.select.result.output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
  [hdi.dashboard.query.browse.output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
  [Area download Microsoft]: http://www.microsoft.com/it-it/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Amministrazione di HDInsight tramite PowerShell]: ../hdinsight-administer-use-powershell/
  [Caricamento di dati in HDInsight]: ../hdinsight-upload-data/
  [Utilizzo di MapReduce con HDInsight]: ../hdinsight-use-mapreduce
  [Utilizzo di Hive con HDInsight]: ../hdinsight-use-hive/
  [Utilizzo di Pig con HDInsight]: ../hdinsight-use-pig/
  [Utilizzo di Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Sviluppo di programmi per la creazione di flussi Hadoop in C# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Sviluppo di programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
