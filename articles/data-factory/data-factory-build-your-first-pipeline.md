<properties
	pageTitle="Creare la prima pipeline con Data factory di Azure"
	description="Questa esercitazione mostra come creare una pipeline di dati di esempio che trasforma i dati usando Azure HDInsight con l'editor di Data factory"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article" 
	ms.date="07/27/2015"
	ms.author="spelluru"/>

# Creare la prima pipeline con Data factory di Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)

Questo articolo include informazioni utili per iniziare a creare la prima pipeline e distribuirla in Data factory di Azure.

> [AZURE.NOTE]Questo articolo non fornisce una panoramica concettuale del servizio Data factory di Azure. Per una panoramica dettagliata del servizio, vedere l'articolo [Introduzione al servizio Data factory di Azure](data-factory-introduction.md).

## Panoramica dell'esercitazione
Questa esercitazione illustra i passaggi necessari per rendere operativa la prima pipeline. Si creeranno le pipeline e si specificheranno tutte le risorse necessarie da zero.

Per esplorare rapidamente le diverse funzionalità della data factory, senza crearne una da zero, è possibile usare gli esempi disponibili nel portale di anteprima di Azure. Vedere [Aggiornamento di Azure Data Factory: distribuzione di esempio semplificata](http://azure.microsoft.com/blog/2015/04/24/azure-data-factory-update-simplified-sample-deployment/) per informazioni su come distribuire un esempio basato su un caso di utilizzo con il portale di anteprima di Azure.

## Prerequisiti
Prima di iniziare questa esercitazione, sono necessari i prerequisiti seguenti:

1.	**Sottoscrizione di Azure**: se non si dispone di una sottoscrizione di Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Vedere l'articolo [Versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/) per informazioni su come ottenere un account di valutazione gratuito.

2.	**Archiviazione di Azure**: in questa esercitazione si userà un account di archiviazione di Azure per archiviare i dati. Se non si dispone di un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione di Azure](../storage-create-storage-account/#create-a-storage-account). Dopo avere creato l'account di archiviazione, sarà necessario ottenere la chiave dell'account usata per accedere alla risorsa di archiviazione. Vedere [Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione](../storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

## Contenuto dell'esercitazione	
Azure Data Factory consente di comporre attività di spostamento dati e di elaborazione dati come flusso di lavoro basato sui dati. Si apprenderà come creare la prima pipeline che userà HDInsight per trasformare e analizzare i blog ogni mese.

In questa esercitazione si eseguirà la procedura seguente:

1.	Creare una data factory.
2.	Creare i servizi collegati seguenti:
	1.	**Account di archiviazione di Azure**: l'account di archiviazione di Azure verrà usato per archiviare i file usati dal cluster HDInsight su richiesta.
	2.	**Cluster HDInsight su richiesta**: un cluster HDInsight verrà avviato su richiesta per trasformare e analizzare i dati.
3.	Creare il set di dati di output 
4.	Creare la pipeline che esegue uno script Hive e archivia il risultato nel set di dati di output. Lo script Hive crea prima una tabella esterna, che fa riferimento ai dati dei blog non elaborati archiviati nell'archivio BLOB di Azure. Il passaggio successivo nello script Hive partiziona quindi i dati non elaborati per anno e per mese.

La prima pipeline, denominata **MyFirstPipeline**, usa un'attività Hive per trasformare e analizzare i blog distribuiti come parte del cluster HDInsight e archiviati in **/HdiSamples/WebsiteLogSampleData/SampleLog/**.

![Vista Diagramma](./media/data-factory-build-your-first-pipeline/diagram-view.png)

Dopo l'esecuzione dello script Hive, i risultati verranno archiviati in un contenitore di archiviazione BLOB di Azure: **data/partitioneddata**.

La disponibilità definita nel set di dati **AzureBlobOutput** determina la frequenza di esecuzione dell'attività Hive. In questa esercitazione viene impostata su base mensile.

## Preparare Archiviazione di Azure per l'esercitazione
Prima di iniziare l'esercitazione, bisogna preparare l'archiviazione di Azure con i file necessari per l'esercitazione.

1. Avviare il Blocco note, incollare il testo seguente e salvarlo come file **partitionweblogs.hql** nella cartella C:\\adfgettingstarted sul disco rigido. Questo script Hive crea due tabelle esterne: **WebLogsRaw** e **WebLogsPartitioned**.

	> [AZURE.IMPORTANT]Sostituire **storageaccountname** nell'ultima riga con il nome del proprio account di archiviazione.

		set hive.exec.dynamic.partition.mode=nonstrict;

		DROP TABLE IF EXISTS WebLogsRaw; 
		CREATE EXTERNAL TABLE WebLogsRaw (
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		LINES TERMINATED BY '\n' 
		LOCATION '/HdiSamples/WebsiteLogSampleData/SampleLog/'
		tblproperties ("skip.header.line.count"="2");
		
		DROP TABLE IF EXISTS WebLogsPartitioned ; 
		create external table WebLogsPartitioned (  
		  date  date,
		  time  string,
		  ssitename string,
		  csmethod  string,
		  csuristem  string,
		  csuriquery string,
		  sport int,
		  susername string,
		  cipcsUserAgent string,
		  csCookie string,
		  csReferer string,
		  cshost  string,
		  scstatus  int,
		  scsubstatus  int,
		  scwin32status  int,
		  scbytes int,
		  csbytes int,
		  timetaken int
		)
		partitioned by ( year int, month int)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
		STORED AS TEXTFILE 
		LOCATION '${hiveconf:partitionedtable}';

		INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
		SELECT
		  date,
		  time,
		  ssitename,
		  csmethod,
		  csuristem,
		  csuriquery,
		  sport,
		  susername,
		  cipcsUserAgent,
		  csCookie,
		  csReferer,
		  cshost,
		  scstatus,
		  scsubstatus,
		  scwin32status,
		  scbytes,
		  csbytes,
		  timetaken,
		  year(date),
		  month(date)
		FROM WebLogsRaw

	 
 
2. Per preparare l'archiviazione di Azure per l'esercitazione:
	1. Scaricare l'[ultima versione di **AzCopy**](http://aka.ms/downloadazcopy) o l'[ultima versione di anteprima](http://aka.ms/downloadazcopypr). Vedere l’articolo [Come usare AzCopy](../storage/storage-use-azcopy.md)per istruzioni sull'utilizzo dell'utilità.
	2. Dopo avere installato AzCopy, è possibile aggiungerlo al percorso del sistema eseguendo il comando seguente al prompt dei comandi. 
	
			set path=%path%;C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
	

	3. Passare alla cartella c:\\adfgettingstarted ed eseguire il comando seguente per caricare il file Hive .HQL nell'account di archiviazione. Sostituire **<StorageAccountName>** con il nome del proprio account di archiviazione e **<Storage Key>** con la chiave dell'account di archiviazione.

			AzCopy /Source:. /Dest:https://<StorageAccountName>.blob.core.windows.net/script /DestKey:<Storage Key>
	4. Dopo avere completato il caricamento del file, verrà visualizzato il seguente output da AzCopy.
	
			Finished 1 of total 1 file(s).
			[2015/06/15 15:47:13] Transfer summary:
			-----------------
			Total files transferred: 1
			Transfer successfully:   1
			Transfer skipped:        0
			Transfer failed:         0
			Elapsed time:            00.00:00:01

Eseguire le operazioni seguenti:

- Fare clic sul collegamento [Tramite l'editor di Data factory](data-factory-build-your-first-pipeline-using-editor.md) in alto per eseguire l'esercitazione usando l'Editor di Data factory, incluso nel portale di Azure.
- Fare clic sul collegamento [Tramite PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) in alto per eseguire l'esercitazione usando Azure PowerShell.
- Fare clic sul collegamento [Tramite Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) in alto per eseguire l'esercitazione usando Visual Studio. 

<!---HONumber=August15_HO6-->