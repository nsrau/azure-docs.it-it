<properties
   pageTitle="Creare cluster Hadoop di HDInsight con Archivio Data Lake di Azure tramite il portale | Azure"
   description="Usare il portale di Azure per creare e usare cluster Hadoop di HDInsight con Archivio Data Lake di Azure."
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/11/2016"
   ms.author="nitinme"/>

# Creare un cluster HDInsight con Archivio Data Lake tramite il portale di Azure

> [AZURE.SELECTOR]
- [Uso del portale](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Tramite PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Informazioni su come usare il portale di Azure per creare un cluster HDInsight (Hadoop, HBase o Storm) con accesso ad Archivio Data Lake di Azure. Alcune considerazioni importanti per questa versione:

* **Per i cluster Hadoop (Windows e Linux)**, Archivio Data Lake può essere usato solo come account di archiviazione aggiuntivo. L'account di archiviazione predefinito per tali cluster continuerà a essere WASB (BLOB di Archiviazione di Azure).

* **Per i cluster Storm (Windows e Linux)**, Archivio Data Lake può essere usato per scrivere dati da una topologia Storm. È anche possibile usare Archivio Data Lake per archiviare dati di riferimento che possono essere letti da una topologia Storm. Per altre informazioni, vedere [Usare Archivio Data Lake in una topologia Storm](#use-data-lake-store-in-a-storm-topology).

* **Per i cluster HBase (Windows e Linux)**, Archivio Data Lake può essere usato come risorsa di archiviazione predefinita o aggiuntiva. Per altre informazioni, vedere [Usare Archivio Data Lake con cluster HBase](#use-data-lake-store-with-hbase-clusters).

> [AZURE.NOTE] L'opzione per creare cluster HDInsight con accesso ad Archivio Data Lake è disponibile solo con HDInsight versione 3.2, sia per Windows che per Linux.


## Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Archivio Data Lake. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup).
- **Account di Archivio Azure Data Lake**. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md). Dopo aver creato l'account, eseguire le attività seguenti per caricare alcuni dati di esempio. Questi dati saranno necessari più avanti nell'esercitazione per eseguire i processi da un cluster HDInsight che accede ai dati nell'Archivio Data Lake.

	* [Creare una cartella in Archivio Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Caricare un file in Archivio Data Lake](data-lake-store-get-started-portal.md#uploaddata). Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## Apprendimento rapido con i video

Guardare i video seguenti per informazioni su come effettuare il provisioning di cluster HDInsight con accesso ad Archivio Data Lake.

* [Creare un cluster HDInsight con accesso ad Archivio Data Lake](https://mix.office.com/watch/l93xri2yhtp2)
* Una volta configurato il cluster, [accedere ai dati in Archivio Data Lake usando gli script Hive e Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)

## Creare un cluster Azure HDInsight con accesso ad Archivio Azure Data Lake.

In questa sezione si creerà un cluster HDInsight Hadoop che usa Archivio Data Lake come risorsa di archiviazione aggiuntiva. In questa versione, Archivio Data Lake può essere usato solo come risorsa di archiviazione aggiuntiva per i cluster Hadoop. L'archivio predefinito continuerà a essere WASB (BLOB di Archiviazione di Azure). Si procederà quindi prima di tutto alla creazione dell'account di archiviazione e dei contenitori di archiviazione richiesti per il cluster.

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).

2. Seguire i passaggi descritti in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) per avviare il provisioning di un cluster HDInsight.

3. Nel pannello **Configurazione facoltativa** fare clic su **Origine dati**. Nel pannello **Origine dati** specificare i dettagli per l'account di archiviazione e il contenitore di archiviazione, impostare la **Località** su **Stati Uniti orientali 2** e quindi fare clic su **Identità AAD cluster**.

	![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Aggiungere entità servizio a cluster HDInsight")

4. Nel pannello **Identità AAD cluster** è possibile scegliere di selezionare un'entità servizio esistente o crearne una nuova.

	* **Creare una nuova entità servizio.**

		* Nel pannello **Identità AAD del cluster** fare clic su **Crea nuovo**, su **Entità servizio** e quindi nel pannello **Crea entità servizio** specificare i valori per creare una nuova entità servizio. Nell'ambito questa operazione vengono creati anche un certificato e un'applicazione Azure Active Directory. Fare clic su **Create**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Aggiungere entità servizio a cluster HDInsight")

		* Nel pannello **Identità AAD del cluster** fare clic su **Gestisci l'accesso ad Archivio Azure Data Lake**. Il riquadro mostra gli account Archivio Data Lake associati alla sottoscrizione. Tuttavia, è possibile impostare le autorizzazioni solo per l'account creato. Selezionare le autorizzazioni di LETTURA/SCRITTURA/ESECUZIONE per l'account che si vuole associare al cluster HDInsight e quindi fare clic su **Salva autorizzazioni**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Aggiungere entità servizio a cluster HDInsight")

		* Nel pannello **Identità AAD del cluster** fare clic su **Scarica certificato** per scaricare il certificato associato all'entità servizio creata. Questa operazione è utile se si vuole usare la stessa entità servizio in futuro durante la creazione di altri cluster HDInsight. Fare clic su **Seleziona**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Aggiungere entità servizio a cluster HDInsight")


	* **Scegliere un'entità servizio esistente**.

		* Nel pannello **Identità AAD del cluster** fare clic su **Usa esistente**, su **Entità servizio** e quindi nel pannello **selezione entità servizio** cercare un'entità servizio esistente. Fare clic sul nome di un'entità servizio e quindi fare clic su **Seleziona**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Aggiungere entità servizio a cluster HDInsight")

		* Nel pannello **Identità AAD del cluster** caricare il certificato con estensione pfx associato all'entità servizio selezionata e quindi specificare la password del certificato.

		* Fare clic su **Gestisci l'accesso ad Archivio Azure Data Lake**. Il riquadro mostra gli account Archivio Data Lake associati alla sottoscrizione. Tuttavia, è possibile impostare le autorizzazioni solo per l'account creato. Selezionare le autorizzazioni di LETTURA/SCRITTURA/ESECUZIONE per l'account che si vuole associare al cluster HDInsight e quindi fare clic su **Salva autorizzazioni**.

			![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "Aggiungere entità servizio a cluster HDInsight")

		* Fare clic su **Salva autorizzazioni** e quindi fare clic su **Seleziona**.

6. Nel pannello **Origine dati** fare clic su **Seleziona** e continuare con il provisioning del cluster come descritto in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal).

7. Al termine del provisioning del cluster, è possibile verificare che l'entità servizio sia effettivamente associata al cluster HDInsight. A tale scopo, nel pannello del cluster fare clic su **Impostazioni** e quindi su **Identità AAD cluster**. Dovrebbe essere visualizzata l'entità servizio associata.

	![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Aggiungere entità servizio a cluster HDInsight")

## Eseguire processi di test sul cluster HDInsight per usare Archivio Data Lake di Azure

Dopo aver configurato un cluster HDInsight, è possibile eseguire processi di test sul cluster per verificare che il cluster HDInsight possa effettivamente accedere ad Archivio Data Lake di Azure. A tale scopo, si eseguiranno ora alcune query Hive destinate ad Archivio Data Lake.

### Per un cluster Linux

1. Aprire il pannello relativo al cluster di cui è appena stato eseguito il provisioning e quindi fare clic su **Dashboard**. Verrà aperto Ambari per il cluster Linux. Quando si accede ad Ambari, verrà richiesto di eseguire l'autenticazione al sito. Immettere il nome dell'account amministratore, il cui valore predefinito è admin, e la password usati durante la creazione del cluster.

	![Avviare il dashboard del cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Avviare il dashboard del cluster")

	È anche possibile passare direttamente ad Ambari passando a https://CLUSTERNAME.azurehdinsight.net in un Web browser, dove **CLUSTERNAME** è il nome del cluster HDInsight.

2. Aprire la visualizzazione Hive. Selezionare il set di quadratini nel menu della pagina, accanto al collegamento **Admin** e al pulsante a destra della pagina, per elencare le viste disponibili. Selezionare **Hive view** (Visualizzazione Hive).

	![Selezione delle visualizzazioni di Ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Verrà visualizzata una pagina simile alla seguente:

	![Immagine della pagina della visualizzazione Hive, contenente una sezione di Query Editor](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. Nella sezione **Query Editor** (Editor di query) della pagina incollare l'istruzione HiveQL seguente nel foglio di lavoro:

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Fare clic sul pulsante **Execute** (Esegui) nella parte inferiore della sezione **Query Editor** (Editor di query) per avviare la query. Sotto **Query Editor** (Editor di query) verrà visualizzata la sezione **Query Process Results** (Risultati elaborazione query) con le informazioni sul processo.

6. Al termine dell'elaborazione della query, la sezione **Query Process Results** (Risultati elaborazione query) visualizzerà i risultati dell'operazione. La scheda **Results** conterrà le informazioni seguenti:

7. Per verificare che la tabella sia stata creata correttamente, eseguire la query seguente.

		SHOW TABLES;

	La scheda **Results** (Risultati) dovrebbe mostrare quanto segue:

		hivesampletable
		vehicles

	**vehicles** costituisce la tabella creata in precedenza, mentre **hivesampletable** è una tabella di esempio disponibile in tutti i cluster HDInsight per impostazione predefinita.

8. È anche possibile eseguire una query per recuperare i dati dalla tabella **vehicles**.

		SELECT * FROM vehicles LIMIT 5;

### Per un cluster Windows

1. Aprire il pannello relativo al cluster di cui è appena stato eseguito il provisioning e quindi fare clic su **Dashboard**.

	![Avviare il dashboard del cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Avviare il dashboard del cluster")

	Quando richiesto, immettere le credenziali di amministratore per il cluster.

2. Verrà aperta la console di query di Microsoft Azure HDInsight. Fare clic su **Editor Hive**.

	![Aprire l'editor Hive](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Aprire l'editor Hive")

3. Nell'editor Hive immettere la query seguente e fare clic su **Invia**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	In questa query Hive si crea una tabella a partire dai dati presenti in Archivio Data Lake, nel percorso `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Questo percorso corrisponde a un file di dati di esempio caricato in precedenza.

	La tabella **Sessione processo** nella parte inferiore riporta lo stato del processo, che cambia da **Inizializzazione** a **In esecuzione** a **Completato**. È anche possibile fare clic su **Visualizza dettagli** per visualizzare maggiori informazioni sul processo completato.

	![Creare una tabella](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Creare una tabella")

4. Per verificare che la tabella sia stata creata correttamente, eseguire la query seguente.

		SHOW TABLES;

	Facendo clic su **Visualizza dettagli** in corrispondenza della query, dovrebbe essere visualizzato quanto segue:

		hivesampletable
		vehicles

	**vehicles** costituisce la tabella creata in precedenza, mentre **hivesampletable** è una tabella di esempio disponibile in tutti i cluster HDInsight per impostazione predefinita.

5. È anche possibile eseguire una query per recuperare i dati dalla tabella **vehicles**.

		SELECT * FROM vehicles LIMIT 5;


## Accedere ad Archivio Data Lake tramite comandi HDFS

Dopo aver configurato il cluster HDInsight perché funzioni con Archivio Data Lake, è possibile usare i comandi della shell HDFS per accedere all'archivio.

### Per un cluster Linux

In questa sezione viene usato SSH nel cluster e vengono eseguiti i comandi HDFS. Windows non fornisce un client SSH incorporato. È consigliabile usare **PuTTY**, disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Per altre informazioni sull'uso di PuTTY, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Dopo avere stabilito la connessione, usare il comando del file system HDFS seguente per elencare i file nell'Archivio Data Lake.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Dovrebbe essere elencato anche il file precedentemente caricato in Archivio Data Lake.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.


### Per un cluster Windows

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).

2. Fare clic su **Sfoglia**, su **Cluster HDInsight** e quindi sul cluster HDInsight creato.

3. Nel pannello del cluster fare clic su **Desktop remoto** e quindi nel pannello **Desktop remoto** fare clic su **Connetti**.

	![Remoto nel cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Creare un gruppo di risorse di Azure")

	Quando richiesto, immettere le credenziali fornite per l'utente desktop remoto.

4. Nella sessione remota, avviare Windows PowerShell e usare i comandi del file system HDFS per elencare i file presenti in Archivio Data Lake.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Dovrebbe essere elencato anche il file precedentemente caricato in Archivio Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	È inoltre possibile usare il comando `hdfs dfs -put` per caricare dei file in Archivio Data Lake e quindi usare `hdfs dfs -ls` per verificare che i file siano stati caricati correttamente.


## Usare Archivio Data Lake in una topologia Storm

È possibile usare Archivio Data Lake per scrivere dati da una topologia Storm. Per le istruzioni su come ottenere questo scenario, vedere [Usare Archivio Azure Data Lake con Apache Storm in HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## Usare Archivio Data Lake con cluster HBase

Con i cluster HBase, è possibile usare Archivio Data Lake come archivio predefinito nonché come spazio di archiviazione aggiuntivo. A tale scopo, procedere come segue:

1.  Nel pannello **Origine dati** per **Percorso dati HBase** selezionare **Archivio Data Lake**.
2.  Selezionare il nome dell'Archivio Data Lake che si desidera usare o crearne uno nuovo.
3.  Infine, specificare **Cartella radice HBase** all'interno di Archivio Data Lake. Se l'account di Archivio Data Lake non dispone di una cartella radice, crearne una nuova.

	![HBase con Archivio Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "Creare un gruppo di risorse di Azure")

### Considerazioni sull'uso di Archivio Data Lake come archivio predefinito per i cluster HBase

* È possibile usare lo stesso account di Archivio Data Lake per più cluster HBase. Il valore specificato in **Cartella radice HBase** per il cluster (passaggio 4 nella schermata riportata sopra) deve tuttavia essere univoco. **Non** usare la stessa cartella radice in due diversi cluster HBase.
* Anche se si utilizza l'account di Archivio Data Lake come archivio predefinito, i file di log del cluster HBase rimangono archiviati nei BLOB di archiviazione di Azure (WASB) associati al cluster. Questo è evidenziato nella casella blu nella schermata riportata sopra.



## Vedere anche

* [PowerShell: Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0518_2016-->