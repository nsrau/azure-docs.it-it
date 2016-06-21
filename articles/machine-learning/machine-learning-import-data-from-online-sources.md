<properties
	pageTitle="Importare i dati in Machine Learning Studio da origini dati online | Microsoft Azure"
	description="Come importare dati di training in Azure Machine Learning Studio da varie origini online."
	keywords="dati di importazione, formato dati, tipi di dati, origini dati, dati di training"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="bradsev;garye;gopitk" />


# Importare i dati in Azure Machine Learning Studio da diverse origini dati online con il modulo Import Data

Questo documento illustra il supporto per l'importazione di dati online da varie origini e le informazioni necessarie per spostare i dati da queste origini a un esperimento di Azure Machine Learning.

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## Introduzione

È possibile accedere in Azure Machine Learning Studio ai dati provenienti da una delle origini dati online disponibili durante l'esecuzione degli esperimenti tramite il modulo [Import Data][import-data]\:

- URL Web tramite HTTP
- Hadoop tramite HiveQL
- Archiviazione BLOB di Azure
- Tabella di Azure
- Database SQL di Azure o SQL Server in una macchina virtuale di Azure
- Provider di feed di dati, attualmente OData
 

Il flusso di lavoro per l'esecuzione di esperimenti in Azure Machine Learning Studio è costituito dal trascinamento e dal rilascio di componenti nell'area di disegno. Per accedere alle origini dati online, aggiungere il modulo [Import Data][import-data] all'esperimento, selezionare **Data source** e quindi fornire i parametri necessari per accedere ai dati. Le origini dati online supportate vengono illustrate nella tabella seguente. Questa tabella riepiloga anche i formati di file supportati e i parametri usati per accedere ai dati.

> [AZURE.NOTE] Questo articolo fornisce informazioni generali sul modulo [Import Data][import-data]. Per ulteriori informazioni sui tipi di dati a cui è possibile accedere, i formati, i parametri e le risposte alle domande comuni, vedere l'argomento di riferimento del modulo per il modulo [Import Data][import-data].

> [AZURE.NOTE] Poiché si accede a questi dati di training durante l'esecuzione dell'esperimento, saranno disponibili solo durante l'esperimento. I dati archiviati nei moduli del set di dati sono invece disponibili per ogni esperimento nell'area di lavoro.


## Origini dati online supportate
Il modulo **Import Data** di Azure Machine Learning supporta le origini dati seguenti:

Origine dati | Descrizione | Parametri |
---|---|---|
URL Web tramite HTTP |Legge i dati nei formati CSV (Comma-Separated Values), TSV (Tab-Separated Values), ARFF (Attribute-Relation File Format) e SVM-light (Support Vector Machines), da qualsiasi URL Web che usa HTTP. | <b>URL</b>: specifica il nome completo del file, inclusi l'URL del sito e il nome file, con qualsiasi estensione. <br/><br/><b>Data format</b>: specifica uno dei formati di dati supportati, ovvero CSV, TSV, ARFF o SVM-light. Se i dati includono una riga di intestazione, la riga verrà usata per assegnare i nomi di colonna.|
Hadoop/HDFS|Legge i dati dall'archivio distribuito in Hadoop. Specificare i dati desiderati usando HiveQL, un linguaggio di query analogo a SQL. HiveQL può essere usato anche per aggregare i dati e applicare filtri ai dati prima di aggiungere i dati a Machine Learning Studio.|<b>Hive database query</b>: specifica la query Hive usata per generare i dati.<br/><br/><b>HCatalog server URI</b>: specifica il nome del cluster con formato *&lt;nome cluster&gt;.azurehdinsight.net.*<br/><br/><b>Hadoop user account name</b>: specifica il nome dell'account utente Hadoop usato per il provisioning del cluster.<br/><br/><b>Hadoop user account password</b>: specifica le credenziali usate durante il provisioning del cluster. Per altre informazioni, vedere [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Location of output data</b>: specifica se i dati vengono archiviati in Hadoop Distributed File System (HDFS) o in Azure. <br/><ul>Se si archiviano i dati di output in HDFS, specificare l'URI del server HDFS. Assicurarsi di usare il nome del cluster HDInsight senza il prefisso HTTPS://. <br/><br/>Se si archiviano i dati di output in Azure, sarà necessario specificare il nome dell'account di archiviazione di Azure, la chiave di accesso alle risorse di archiviazione e il nome del contenitore di archiviazione.</ul>|
Database SQL |Legge i dati archiviati in un database SQL di Azure o in un database SQL Server in esecuzione in una macchina virtuale di Azure. | <b>Database server name</b>: specifica il nome del server in cui è in esecuzione il database.<br/><ul>Se si usa il database SQL di Azure, immettere il nome del server generato. In genere ha il formato seguente: *&lt;identificatore\_generato&gt;.database.windows.net.* <br/><br/>Se si usa un server SQL ospitato in una macchina virtuale di Azure, immettere *tcp:&lt;Nome DNS macchina virtuale&gt;, 1433*</ul><br/><b>Database name</b>: specifica il nome del database sul server. <br/><br/><b>Server user account name</b>: specifica un nome utente per un account con autorizzazioni di accesso per il database. <br/><br/><b>Server user account password</b>: specifica la password per l'account utente.<br/><br/><b>Accept any server certificate</b>: usare questa opzione, meno sicura, se si vuole evitare la verifica del certificato del sito prima di leggere i dati.<br/><br/><b>Database query</b>: immettere un'istruzione SQL che descrive i dati da leggere.|
Tabella di Azure|Legge i dati dal servizio tabelle nell'Archiviazione di Azure Storage.<br/><br/>Se si leggono raramente quantità elevate di dati, usare il servizio tabelle di Azure. Offre una soluzione di archiviazione flessibile, non relazionale (NoSQL), a scalabilità elevata, poco costosa e a disponibilità elevata.| Le opzioni disponibili in **Import Data** dipendono dal tipo di informazioni a cui si accede, ovvero informazioni pubbliche o un account di archiviazione privato che richiede credenziali di accesso. Questo aspetto è determinato da <b>Authentication Type</b>, che può avere un valore "PublicOrSAS" o "Account", ognuno dei quali ha un set di parametri specifico. <br/><br/><b>Public or Shared Access Signature (SAS) URI</b>: i parametri sono:<br/><br/><ul><b>Table URI</b>: specifica l'URL pubblico o la firma di accesso condiviso per la tabella.<br/><br/><b>Specifica le righe da analizzare alla ricerca dei nomi di proprietà</b>: i valori sono <i>TopN</i> per analizzare il numero specificato di righe o <i>ScanAll</i> per ottenere tutte le righe della tabella. <br/><br/>Se i dati sono omogenei e prevedibili, è consigliabile selezionare *TopN* e immettere un numero per N. Per tabelle di grandi dimensioni, ciò può consentire tempi di risposta più rapidi.<br/><br/>Se i dati sono strutturati con set di proprietà che variano in base alla profondità e alla posizione della tabella, scegliere l'opzione *ScanAll* per analizzare tutte le righe. Ciò assicura l'integrità della conversione risultante di proprietà e metadati.<br/><br/></ul><b>Private Storage Account</b>: i parametri sono: <br/><br/><ul><b>Account name</b>: specifica il nome dell'account che include la tabella da leggere.<br/><br/><b>Account key</b>: specifica la chiave di archiviazione associata all'account.<br/><br/><b>Table name</b> : specifica il nome della tabella che include i dati da leggere.<br/><br/><b>Rows to scan for property names</b>: i valori sono <i>TopN</i> per analizzare il numero di righe specificato o <i>ScanAll</i> per ottenere tutte le righe della tabella.<br/><br/>Se i dati sono omogenei e prevedibili, è consigliabile selezionare *TopN* e immettere un numero per N. Per tabelle di grandi dimensioni, ciò può consentire tempi di risposta più rapidi.<br/><br/>Se i dati sono strutturati con set di proprietà che variano in base alla profondità e alla posizione della tabella, scegliere l'opzione *ScanAll* per analizzare tutte le righe. Ciò assicura l’integrità della conversione risultante di proprietà e metadati.<br/><br/>|
Archiviazione BLOB di Azure | Legge i dati archiviati nel servizio BLOB dell'Archiviazione di Azure, incluse immagini, testo non strutturato o dati binari.<br/><br/>È possibile usare il servizio BLOB per esporre pubblicamente i dati o per archiviare privatamente i dati dell'applicazione. È possibile accedere ai dati da qualsiasi posizione mediante connessioni HTTP o HTTPS. | Le opzioni disponibili nel modulo **Import Data** dipendono dal tipo di informazioni a cui si accede, ovvero informazioni pubbliche o un account di archiviazione privato che richiede credenziali di accesso. Questo aspetto è determinato da <b>Authentication Type</b>, che può avere un valore "PublicOrSAS" o "Account".<br/><br/><b>Public or Shared Access Signature (SAS) URI</b>: i parametri sono:<br/><br/><ul><b>URI</b>: specifica l'URL pubblico o la firma di accesso condiviso per il BLOB di archiviazione.<br/><br/><b>File Format</b>: specifica il formato dei dati nel servizio BLOB. I formati supportati sono CSV, TSV e ARFF.<br/><br/></ul><b>Private Storage Account</b>: i parametri sono: <br/><br/><ul><b>Account name</b>: specifica il nome dell'account che include il BLOB da leggere.<br/><br/><b>Account key</b>: specifica la chiave di archiviazione associata all'account.<br/><br/><b>Path to container, directory, or blob</b>: specifica il nome del BLOB che include i dati da leggere.<br/><br/><b>Blob file format</b>: specifica il formato dei dati nel servizio BLOB. I formati di dati supportati sono CSV, TSV, ARFF, CSV con una codifica specificata, ed Excel. <br/><br/><ul>Se il formato è CSV o TSV, assicurarsi di indicare se il file contiene una riga di intestazione.<br/><br/>È possibile usare l'opzione Excel per leggere dati dalle cartelle di lavoro di Excel. Nell'opzione<i>Excel data format</i> indicare se i dati si trovano in un intervallo di foglio di lavoro di Excel o in una tabella di Excel. Nell'opzione <i>Excel sheet or embedded table</i> specificare il nome del foglio o della tabella da cui leggere i dati.</ul><br/>|
Provider di feed di dati | Legge dati da un provider di feed supportato. È attualmente supportato solo il formato OData (Open Data Protocol). | <b>Data content type</b>: specifica il formato OData.<br/><br/><b>Source URL</b>: specifica l'URL completo per il feed di dati. <br/>Ad esempio, l'URL seguente legge dal database Northwind di esempio: http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0608_2016-->