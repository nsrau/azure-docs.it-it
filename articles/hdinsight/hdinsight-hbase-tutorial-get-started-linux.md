<properties
	pageTitle="Esercitazione su HBase: Introduzione ai cluster HBase basati su Linux in Hadoop | Microsoft Azure"
	description="Seguire questa esercitazione di HBase per iniziare a usare Apache HBase con Hadoop in HDInsight. Creare tabelle dalla shell HBase e sottoporle a query tramite Hive."
	keywords="Apache hbase, hbase, shell di hbase, esercitazione hbase"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/27/2016"
	ms.author="jgao"/>



# Esercitazione su HBase: Introduzione all'uso di Apache HBase con Hadoop basato su Linux in HDInsight 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Informazioni su come creare un cluster HBase in HDInsight, creare tabelle HBase ed eseguire query sulle tabelle con Hive. Per informazioni generali su HBase, vedere [Panoramica di HDInsight HBase][hdinsight-hbase-overview].

Le informazioni contenute in questo documento sono specifiche per i cluster HDInsight basati su Linux. Per informazioni sui cluster basati su Windows, usare il selettore di schede in alto nella pagina per passare alla scheda specifica.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

###Prerequisiti

Prima di iniziare questa esercitazione di HBase, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell(SSU)](hdinsight-hadoop-linux-use-ssh-unix.md).
- [curl](http://curl.haxx.se/download.html).

## Nome del cluster HBase

La procedura seguente usa un modello di Gestione risorse di Azure per creare un cluster HBase. Per comprendere i parametri usati nella procedure e altri metodi di creazione del cluster, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Fare clic sull'immagine seguente per aprire un modello di Gestione risorse di Azure nel portale di Azure. Il modello di Gestione risorse di Azure è disponibile in un contenitore BLOB pubblico.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/it-IT/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Nel pannello **Parametri** immettere le informazioni seguenti:

    - **ClusterName** (NomeCluster): immettere un nome per il cluster HBase che verrà creato.
    - **Cluster login name and password** (Nome di accesso e password del cluster): il nome dell'account di accesso predefinito è **admin**.
    - **SSH username and password** (Nome utente e password SSH): il nome utente predefinito è **sshuser**. È possibile rinominarlo.
     
    Gli altri parametri sono facoltativi.
    
    Ogni cluster ha una dipendenza dall'account di archiviazione BLOB di Azure. Dopo aver eliminato un cluster, i dati vengono mantenuti nell'account di archiviazione. Il nome dell'account di archiviazione predefinito del cluster è il nome del cluster a cui viene aggiunto "store". È hardcoded nella sezione delle variabili del modello.
        
3. Fare clic su **OK** per salvare i parametri.
4. Nel pannello **Distribuzione personalizzata** fare clic su **Gruppo di risorse** nella casella di riepilogo a discesa e quindi scegliere **Nuovo** per creare un nuovo gruppo di risorse. Il gruppo di risorse è un contenitore che raggruppa il cluster, l'account di archiviazione dipendente e altre risorse collegate.
5. Fare clic su **Note legali** e quindi su **Crea**.
6. Fare clic su **Create**. La creazione di un cluster richiede circa 20 minuti.


>[AZURE.NOTE] Dopo l'eliminazione di un cluster HBase, è possibile creare un altro cluster HBase usando lo stesso contenitore di BLOB predefinito. Il nuovo cluster selezionerà le tabelle HBase create nel cluster originale. È consigliabile disabilitare le tabelle HBase prima di eliminare il cluster per evitare incoerenze.

## Creare tabelle e inserire dati

È possibile usare SSH per connettersi al cluster HBase e usare la shell di HBase per creare tabelle HBase, inserire dati ed eseguire query sui dati. Per altre informazioni sull'uso di SSH da Linux, Unix, OS X e Windows, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Linux, Unix oppure OS X](hdinsight-hadoop-linux-use-ssh-unix.md) e [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Per la maggior parte delle persone, i dati vengono visualizzati in formato tabulare:

![dati tabulari hbase di hdinsight][img-hbase-sample-data-tabular]

In HBase, che rappresenta un'implementazione di BigTable, gli stessi dati sono simili a:

![dati bigtable hbase di hdinsight][img-hbase-sample-data-bigtable]

Ciò può essere più utile dopo avere completato la procedura successiva.


**Per usare la shell HBase**

1. In SSH eseguire il comando seguente:

		hbase shell

4. Creare un HBase con due gruppi di colonne:

		create 'Contacts', 'Personal', 'Office'
		list
5. Inserire alcuni dati:

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![shell hbase di hdinsight hadoop][img-hbase-shell]

6. Ottenere una singola riga

		get 'Contacts', '1000'

	Verranno visualizzati gli stessi risultati usando il comando di analisi, poiché esiste solo una riga.

	Per altre informazioni sullo schema di tabella HBase, vedere [Introduzione alla progettazione dello schema HBase][hbase-schema]. Per altri comandi HBase, vedere la [guida di riferimento di Apache HBase][hbase-quick-start].

6. Chiudere la shell

		exit



**Per il caricamento bulk dei dati nella tabella HBase dei contatti**

HBase include diversi metodi di caricamento dei dati nelle tabelle. Per altre informazioni, vedere [Caricamento bulk](http://hbase.apache.org/book.html#arch.bulk.load).


Un file di dati di esempio è stato caricato in un contenitore BLOB pubblico, *wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*. Il contenuto del file di dati è il seguente:

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

È possibile creare un file di testo e caricare il file nel proprio account di archiviazione. Per le istruzioni, vedere [Caricare dati per processi Hadoop in HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] In questa procedura viene utilizzata la tabella HBase dei contatti creata nella procedura precedente.

1. In SSH eseguire il comando seguente per trasformare il file di dati in StoreFiles e archiviarlo in un percorso relativo specificato da Dimporttsv.bulk.output: Se si è nella shell di HBase, usare il comando exit per uscire.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Eseguire questo comando per caricare i dati da /example/data/storeDataFileOutput nella tabella di HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. È possibile aprire la shell HBase e usare il comando di analisi per visualizzare il contenuto della tabella.



## Usare Hive per eseguire query su HBase

È possibile eseguire query sui dati nelle tabelle HBase tramite Hive. In questa sezione una tabella Hive mappata alla tabella HBase viene creata e usata per interrogare i dati nella tabella HBase.

1. Aprire **PuTTY** e connettersi al cluster. Vedere le istruzioni nella procedura precedente.
2. Aprire la shell di Hive.

	   hive
3. Eseguire il seguente script HiveQL per creare una tabella Hive mappata alla tabella HBase. Prima di eseguire questa istruzione, assicurarsi di aver creato la tabella di esempio usata precedentemente in questa esercitazione come riferimento con la shell HBase.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Eseguire lo script HiveQL seguente. La query Hive interroga i dati nella tabella HBase:

     	SELECT count(*) FROM hbasecontacts;

## Usare le API REST HBase mediante Curl

> [AZURE.NOTE] Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password dell'amministratore cluster HDInsight. È inoltre necessario specificare il nome del cluster come parte dell'URI (Uniform Resource Identifier) usato per inviare le richieste al server.
>
> Per i comandi riportati in questa sezione, sostituire **USERNAME** con l'utente da autenticare nel cluster e **PASSWORD** con la password dell'account utente. Sostituire **CLUSTERNAME** con il nome del cluster.
>
> L'API REST viene protetta tramite l'[autenticazione di base](http://en.wikipedia.org/wiki/Basic_access_authentication). È necessario effettuare sempre le richieste usando il protocollo HTTPS (Secure HTTP) per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Da una riga di comando usare il comando seguente per verificare che sia possibile connettersi al cluster HDInsight:

		curl -u <UserName>:<Password> \
		-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	Dovrebbe essere visualizzato un messaggio simile al seguente:

		{"status":"ok","version":"v1"}

	I parametri usati in questo comando sono i seguenti:

	* **-u**: il nome utente e la password usati per autenticare la richiesta.
	* **-G**: indica che si tratta di una richiesta GET.

2. Usare il comando seguente per ottenere l'elenco delle tabelle HBase esistenti:

		curl -u <UserName>:<Password> \
		-G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Usare il comando seguente per creare una nuova tabella HBase due famiglie di colonne:

		curl -u <UserName>:<Password> \
		-X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
		-H "Accept: application/json" \
		-H "Content-Type: application/json" \
		-d "{"@name":"Contact1","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}" \
		-v

	Lo schema viene fornito nel formato JSON.

4. Usare il comando seguente per inserire alcuni dati:

		curl -u <UserName>:<Password> \
		-X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
		-H "Accept: application/json" \
		-H "Content-Type: application/json" \
		-d "{"Row":{"key":"MTAwMA==","Cell":{"column":"UGVyc29uYWw6TmFtZQ==", "$":"Sm9obiBEb2xl"}}}" \
		-v

	È necessario applicare la codifica base64 ai valori specificati nell'interruttore -d. Nell'esempio:

	- MTAwMA==: 1000
	- UGVyc29uYWw6TmFtZQ==: Peronsal:Name
	- Sm9obiBEb2xl: John Dole

	[false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) consente di inserire più valori in batch.

5. Usare il comando seguente per ottenere una riga:

		curl -u <UserName>:<Password> \
		-X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
		-H "Accept: application/json" \
		-v

Per altre informazioni sulle API REST HBase, vedere la [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Guida di riferimento di Apache HBase).

## Controllare lo stato del cluster

HBase in HDInsight viene fornito con un'interfaccia utente Web per il monitoraggio dei cluster. Usando l’interfaccia Web è possibile richiedere statistiche o informazioni sulle aree.

SSH può essere usato anche per effettuare il tunneling di richieste locali, ad esempio richieste Web, al cluster HDInsight. La richiesta verrà quindi instradata alla risorsa richiesta come se provenisse dal nodo head del cluster HDInsight. Per altre informazioni, vedere [Uso di SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Per stabilire una sessione di tunneling SSH**

1. Aprire **PuTTY**.
2. Se è stata specificata una chiave SSH durante il processo di creazione dell'account utente, è necessario eseguire il passaggio seguente per selezionare la chiave privata da usare durante l'autenticazione nel cluster:

	In **Category** espandere **Connection**, **SSH** e selezionare **Auth**. Infine, fare clic su **Browse** e selezionare il file ppk che contiene la chiave privata.

3. In **Category** fare clic **Session**.
4. Nelle opzioni di base disponibili nella schermata della sessione di PuTTY, immettere i valori seguenti:

	- **Host Name**: indirizzo SSH del server HDInsight nel campo Host Name (o IP address). L'indirizzo SSH è il nome del cluster, quindi **-ssh.azurehdinsight.net**. Ad esempio, *mycluster-ssh.azurehdinsight.net*.
	- **Port**: 22. La porta ssh sul nodo head 0 è 22.
5. Nella sezione **Category** sul lato sinistro della finestra di dialogo espandere **Connection**, **SSH** e infine fare clic su **Tunnels**.
6. Fornire le seguenti informazioni nel modulo "Options controlling SSH port forwarding":

	- **Source port**: la porta del client che si desidera inoltrare. Ad esempio, 9876.
	- **Dynamic**: consente il routing tramite proxy SOCKS dinamico.
7. Fare clic su **Add** per aggiungere le impostazioni.
8. Fare clic su **Open** nella parte inferiore della finestra di dialogo per aprire una connessione SSH.
9. Quando richiesto, accedere al server usando un account SSH. Verrà stabilita una sessione SSH e verrà abilitato il tunnel.

**Per trovare il nome di dominio completo degli zookeeper usando Ambari**

1. Passare a https://<ClusterName>.azurehdinsight.net/.
2. Immettere le credenziali dell'account utente del cluster due volte.
3. Nel menu a sinistra fare clic su **zookeeper**.
4. Fare clic su uno dei tre collegamenti **ZooKeeper Server** nell'elenco di riepilogo.
5. Copiare il valore di **Hostname**. Ad esempio, zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Per configurare un programma client (Firefox) e controllare lo stato del cluster**

1. Aprire Firefox.
2. Fare clic sul pulsante **Apri menu**.
3. Fare clic su **Opzioni**.
4. Fare clic su **Avanzate**, **Rete** e quindi **Impostazioni**.
5. Selezionare **Configurazione manuale dei proxy**.
6. Immettere i valori seguenti:

	- **Host SOCKS**: localhost
	- **Porta**: usare la stessa porta configurata nel tunneling SSH di Putty. Ad esempio, 9876.
	- **SOCKS v5**: (selezionato)
	- **DNS remoto**: (selezionato)
7. Fare clic su **OK** per salvare le modifiche.
8. Passare a http://&lt;The FQDN di un ZooKeeper>:60010/master-status.

In un cluster a disponibilità elevata verrà invece visualizzato un collegamento al nodo master HBase attivo corrente che ospita l'interfaccia utente Web.

##Eliminazione del cluster

È consigliabile disabilitare le tabelle HBase prima di eliminare il cluster per evitare incoerenze.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Passaggi successivi

In questa esercitazione di HBase per HDInsight si è appreso come creare tabelle e un cluster HBase e come visualizzare i dati delle tabelle dalla shell HBase. Si è inoltre appreso come usare una query Hive sui dati nelle tabelle HBase e come usare le API REST C# di HBase per creare una tabella HBase e recuperare i dati dalla tabella.

Per ulteriori informazioni, vedere:

- [Panoramica di HDInsight HBase][hdinsight-hbase-overview]\: HBase è un database NoSQL open source Apache basato su Hadoop che fornisce un accesso casuale e coerenza assoluta a grandi quantità di dati non strutturati e semistrutturati.


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0629_2016-->