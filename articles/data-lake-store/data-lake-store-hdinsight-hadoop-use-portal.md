<properties 
   pageTitle="Creare cluster Hadoop di HDInsight con Archivio Data Lake di Azure tramite il portale | Azure" 
   description="Usare il portale di Azure per creare e usare cluster Hadoop di HDInsight con Archivio Data Lake di Azure." 
   services="data-lake-store" 
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
   ms.date="01/06/2016"
   ms.author="nitinme"/>

# Creare un cluster HDInsight con Archivio Data Lake tramite il portale di Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Informazioni su come usare il portale di Azure per creare un cluster HDInsight (Hadoop, HBase o Storm) con accesso ad Archivio Data Lake di Azure. Alcune considerazioni importanti per questa versione:

* **Per i cluster Hadoop e Storm (Windows e Linux)**, Archivio Data Lake può essere usato solo come account di archiviazione aggiuntivo. L'account di archiviazione predefinito per tali cluster continuerà a essere WASB (BLOB di Archiviazione di Azure).

* **Per i cluster HBase (Windows e Linux)**, Archivio Data Lake può essere usato come risorsa di archiviazione predefinita o aggiuntiva.


In questo articolo si effettuerà il provisioning di un cluster Hadoop con Archivio Data Lake come risorsa di archiviazione aggiuntiva. La configurazione di HDInsight in modo che interagisca con Archivio Data Lake tramite il portale di Azure prevede i passaggi seguenti:

* Creare un cluster HDInsight con autenticazione per un'entità servizio di Azure Active Directory
* Configurare l'accesso ad Archivio Data Lake usando la stessa entità servizio
* Eseguire un processo di test sul cluster

## Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Archivio Data Lake. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup).


## Creare un cluster HDInsight con autenticazione per un'entità servizio di Azure Active Directory

In questa sezione si creerà un cluster HDInsight Hadoop che usa Archivio Data Lake come risorsa di archiviazione aggiuntiva. In questa versione, Archivio Data Lake può essere usato solo come risorsa di archiviazione aggiuntiva per i cluster Hadoop. L'archivio predefinito continuerà a essere WASB (BLOB di Archiviazione di Azure). Si procederà quindi prima di tutto alla creazione dell'account di archiviazione e dei contenitori di archiviazione richiesti per il cluster.

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).

2. Seguire i passaggi descritti in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) per avviare il provisioning di un cluster HDInsight.
 
3. Nel pannello **Configurazione facoltativa** fare clic su **Origine dati**. Nel pannello **Origine dati** specificare i dettagli per l'account di archiviazione e il contenitore di archiviazione, impostare la **Località** su **Stati Uniti orientali 2** e quindi fare clic su **Identità AAD cluster**.

	![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Aggiungere entità servizio a cluster HDInsight")

4. Nel pannello **Identità AAD cluster** è possibile scegliere di selezionare un'entità servizio esistente o crearne una nuova.
	
	* **Creare una nuova entità servizio**. Nel pannello **Identità AAD cluster** fare clic su **Crea nuovo**. Fare clic su **Entità servizio** e nel pannello **Crea un'entità servizio** specificare i valori necessari per creare una nuova entità servizio. Nell'ambito questa operazione vengono creati anche un certificato e un'applicazione Azure Active Directory. Fare clic su **Create**.

		![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Aggiungere entità servizio a cluster HDInsight")

		Nel pannello **Identità AAD cluster** fare clic su **Seleziona** per continuare la creazione dell'entità servizio.

		![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Aggiungere entità servizio a cluster HDInsight")


	* **Scegliere un'entità servizio esistente**. Nel pannello **Identità AAD cluster** fare clic su **Usa esistente**. Fare clic su **Entità servizio** e nel pannello **Seleziona un'entità servizio** cercare un'entità servizio esistente. Fare clic sul nome di un'entità servizio e quindi fare clic su **Seleziona**.

		![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Aggiungere entità servizio a cluster HDInsight")

		Nel pannello **Identità AAD cluster** caricare il certificato (PFX) creato in precedenza e specificare la password usata per creare il certificato. Fare clic su **Seleziona**. Con questa operazione si completa la configurazione di Azure Active Directory per un cluster HDInsight.

		![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Aggiungere entità servizio a cluster HDInsight")

6. Nel pannello **Origine dati** fare clic su **Seleziona** e continuare con il provisioning del cluster come descritto in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal).

7. Al termine del provisioning del cluster, è possibile verificare che l'entità servizio sia effettivamente associata al cluster HDInsight. A tale scopo, nel pannello del cluster fare clic su **Impostazioni** e quindi su **Identità AAD cluster**. Dovrebbe essere visualizzata l'entità servizio associata.

	![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Aggiungere entità servizio a cluster HDInsight")

## <a name="acl"></a>Configurare l'entità servizio per accedere al file system di Archivio Data Lake

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).

2. Se non si dispone di un account di Archivio Data Lake, crearne uno. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md).

	Se si dispone già di un account di Archivio Data Lake, nel riquadro sinistro fare clic su **Sfoglia**, su **Archivio Data Lake** e quindi sul nome dell'account per il quale si desidera concedere l'accesso.

	Eseguire le operazioni seguenti nell'account di Archivio Data Lake.

	* [Creare una cartella in Archivio Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Caricare un file in Archivio Data Lake](data-lake-store-get-started-portal.md#uploaddata). Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

	Questi file verranno usati più tardi, al momento di eseguire il test dell'account di Archivio Data Lake nel cluster HDInsight.

3. Nel pannello di Archivio Data Lake, fare clic su **Esplora dati**.

	![Esplora dati](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.start.data.explorer.png "Esplora dati")

4. Nel pannello **Esplora dati** fare clic sulla cartella principale dell'account e, nel pannello dell'account, fare clic sull'icona **Accedi**.

	![Impostare elenchi di controllo accesso nel file system di Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.1.png "Impostare elenchi di controllo accesso nel file system di Data Lake")

5. Il pannello di **accesso** elenca i profili di accesso standard e personalizzato già assegnati all'elemento radice. Fare clic sull'icona **Aggiungi** per aggiungere elenchi di controllo di accesso di livello personalizzato e includere l'entità servizio creata in precedenza.

	![Elencare profili di accesso standard e personalizzato](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.2.png "Elencare profili di accesso standard e personalizzato")

6. Fare clic sull'icona **Aggiungi** per aprire il pannello **Aggiungi accesso personalizzato**. In questo pannello fare clic su **Seleziona utente o gruppo** e, nel pannello **Seleziona utente o gruppo**, cercare l'entità servizio precedentemente creata in Azure Active Directory. Il nome dell'entità servizio creata in precedenza è **HDIADL**. Fare clic sul nome dell'entità servizio e quindi su **Seleziona**.

	![Aggiungere un gruppo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.3.png "Aggiungere un gruppo")

7. Fare clic su **Selezionare le autorizzazioni**, selezionare le autorizzazioni che si desidera assegnare all'entità servizio e quindi fare clic su **OK**.

	![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.4.png "Assegnare autorizzazioni a un gruppo")

8. Nel pannello **Aggiungi accesso personalizzato** fare clic su **OK**. Il gruppo appena aggiunto, con le autorizzazioni associate, risulterà ora elencato nel pannello di **accesso**.

	![Assegnare autorizzazioni a un gruppo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.5.png "Assegnare autorizzazioni a un gruppo")

7. Se necessario, è possibile modificare le autorizzazioni di accesso anche dopo aver aggiunto l'entità servizio. Selezionare o deselezionare la casella di controllo per ogni tipo di autorizzazione (lettura, scrittura, esecuzione), in modo da aggiungere o rimuovere l'autorizzazione corrispondente. Fare clic su **Salva** per salvare le modifiche o su **Ignora** per annullare le modifiche.



## Eseguire processi di test sul cluster HDInsight per usare Archivio Data Lake di Azure

Dopo aver configurato un cluster HDInsight, è possibile eseguire processi di test sul cluster per verificare che il cluster HDInsight possa effettivamente accedere ad Archivio Data Lake di Azure. A tale scopo, si eseguiranno ora alcune query Hive destinate ad Archivio Data Lake.

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

	Facendo clic sull'opzione Visualizza dettagli corrispondente alla query, dovrebbe essere visualizzato quanto segue:

		hivesampletable
		vehicles

	**vehicles** costituisce la tabella creata in precedenza, mentre **hivesampletable** è una tabella di esempio disponibile in tutti i cluster HDInsight per impostazione predefinita.

5. È possibile anche eseguire una query per recuperare i dati di **vehicles**.

		SELECT * FROM vehicles LIMIT 5;

## Accedere ad Archivio Data Lake tramite comandi HDFS

Dopo aver configurato il cluster HDInsight perché funzioni con Archivio Data Lake, è possibile usare i comandi della shell HDFS per accedere all'archivio.

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

## Considerazioni sul provisioning di cluster HBase che usano Archivio Data Lake come risorsa di archiviazione predefinita

Con i cluster HBase è possibile usare gli account di Archivio Data Lake come risorse di archiviazione predefinita. In questo caso, per eseguire il provisioning di un cluster, l'entità servizio associata al cluster **deve** avere i diritti di accesso all'account di Archivio Data Lake. È possibile soddisfare questo requisito in due modi:

* **Se si usa un'entità servizio esistente**, prima di iniziare il provisioning del cluster è necessario assicurarsi che l'entità servizio sia stata aggiunta all'elenco di controllo di accesso al livello radice del file system di Archivio Data Lake.
* **Se si è scelto di creare una nuova entità servizio**, non appena viene avviato il provisioning del cluster è necessario aggiungere l'entità servizio appena creata al livello radice del file system di Archivio Data Lake. Se non si riesce a effettuare questa operazione, il provisioning del cluster verrà eseguito, ma i servizi di HBase non verranno avviati. Per ovviare a questo problema, è possibile aggiungere l'entità servizio all'elenco di controllo di accesso dell'account di Archivio Data Lake e quindi riavviare i servizi di HBase tramite l'interfaccia utente Web Ambari.

Per istruzioni su come aggiungere un'entità servizio al file system di Archivio Data Lake, vedere [Configurare l'entità servizio per accedere al file system di Archivio Data Lake](#acl).



## Vedere anche

* [PowerShell: Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0107_2016-->