<properties 
	pageTitle="Spostare dati da Cassandra con Data Factory | Microsoft Azure" 
	description="Informazioni su come spostare dati da un database Cassandra locale mediante Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="linda33wj" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2016" 
	ms.author="jingwang"/>

# Spostare dati da un database Cassandra locale mediante Azure Data Factory
Questo articolo illustra come usare l'attività di copia in una data factory di Azure per copiare dati da un database Cassandra locale in qualsiasi archivio dati elencato nella colonna Sink della sezione relativa alle [origini e ai sink supportati](data-factory-data-movement-activities.md#supported-data-stores). Questo articolo si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivio dati supportate.

Data Factory supporta attualmente solo lo spostamento di dati da un database Cassandra agli [archivi dati sink supportati](data-factory-data-movement-activities.md#supported-data-stores) e non supporta lo spostamento dei dati da altri archivi dati in un database Cassandra.

## Prerequisiti
Per consentire al servizio Azure Data Factory di connettersi al database Cassandra in locale, è necessario installare quanto segue:

- Gateway di gestione dati 2.0 o versione successiva nello stesso computer che ospita il database o in un computer separato per evitare che competa per le risorse con il database. Il Gateway di gestione dati è un software che connette le origini dati locali ai servizi cloud in modo sicuro e gestito. Vedere l’articolo [Spostare dati tra cloud e locale](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sui Gateway di Gestione dati.
  
	Quando si installa il gateway, viene installato automaticamente un driver Microsoft ODBC Cassandra che viene usato per la connessione al database Cassandra.

> [AZURE.NOTE] Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).

## Copia di dati guidata
Il modo più semplice per creare una pipeline che copia i dati da un database Cassandra in uno degli archivi dati sink supportati è la procedura guidata per la copia dei dati. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

L'esempio di seguito fornisce le definizioni JSON campione da usare per creare una pipeline con il [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da un database Cassandra in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores) usando l'attività di copia in Azure Data Factory.


## Esempio: Copiare dati da Cassandra a BLOB
Nell'esempio i dati vengono copiati da un database Cassandra a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi. I dati possono essere copiati direttamente in uno qualsiasi dei sink indicati nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores), usando l'attività di copia in Azure Data Factory.

- Un servizio collegato di tipo [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [set di dati](data-factory-create-datasets.md) di input di tipo [CassandraTable](#cassandratable-properties).
- Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [CassandraSource](#cassandrasource-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Servizio collegato Cassandra**

Questo esempio usa il servizio collegato **Cassandra**. Per informazioni sulle proprietà supportate da questo servizio collegato, vedere la sezione dedicata al [servizio collegato Cassandra](#onpremisescassandra-linked-service-properties).

	{
    	"name": "CassandraLinkedService",
    	"properties":
    	{
			"type": "OnPremisesCassandra",
			"typeProperties":
			{
				"authenticationType": "Basic",
				"host": "mycassandraserver",
				"port": 9042,
				"username": "user",
				"password": "password",
				"gatewayName": "mygateway"
        	}
    	}
	}


**Servizio collegato Archiviazione di Azure**

	{
		"name": "AzureStorageLinkedService",
		"properties": {
		"type": "AzureStorage",
			"typeProperties": {
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
			}
		}
	}

**Set di dati di input Cassandra**

	{
		"name": "CassandraInput",
		"properties": {
			"linkedServiceName": "CassandraLinkedService",
			"type": "CassandraTable",
			"typeProperties": {
				"tableName": "mytable",
				"keySpace": "mykeyspace" 
			},
			"availability": {
				"frequency": "Hour",
				"interval": 1
			},
			"external": true,
			"policy": {
				"externalData": {
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}
		}
	}

Impostando **external** su **true** si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1).

	{
		"name": "AzureBlobOutput",
		"properties":
		{
			"type": "AzureBlob",
			"linkedServiceName": "AzureStorageLinkedService",
			"typeProperties":
			{
				"folderPath": "adfgetstarted/fromcassandra"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1
			}
		}
	}


**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **CassandraSource** e il tipo di **sink** è impostato su **BlobSink**.

Per l'elenco delle proprietà supportate da RelationalSource, vedere le [proprietà del tipo RelationalSource](#cassandrasource-type-properties).
	
	{  
		"name":"SamplePipeline",
		"properties":{  
			"start":"2016-06-01T18:00:00",
			"end":"2016-06-01T19:00:00",
			"description":"pipeline with copy activity",
			"activities":[  
			{
				"name": "CassandraToAzureBlob",
				"description": "Copy from Cassandra to an Azure blob",
				"type": "Copy",
				"inputs": [
				{
					"name": "CassandraInput"
				}
				],
				"outputs": [
				{
					"name": "AzureBlobOutput"
				}
				],
				"typeProperties": {
					"source": {
						"type": "CassandraSource",
						"query": "select id, firstname, lastname from mykeyspace.mytable"
		
					},
					"sink": {
						"type": "BlobSink"
					}
				},
				"scheduler": {
					"frequency": "Hour",
					"interval": 1
				},
				"policy": {
					"concurrency": 1,
					"executionPriorityOrder": "OldestFirst",
					"retry": 0,
					"timeout": "01:00:00"
				}
			}
			]	
		}
	}
## Proprietà del servizio collegato OnPremisesCassandra

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato Cassandra.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| type | La proprietà type deve essere impostata su: **OnPremisesCassandra** | Sì |
| host | Uno o più indirizzi IP o nomi host di server Cassandra.<br/><br/>Specificare un elenco delimitato da virgole degli indirizzi IP o nomi host per la connessione a tutti i server contemporaneamente. | Sì | 
| port | La porta TCP che il server Cassandra usa per ascoltare le connessioni client. | No, valore predefinito: 9042 |
| authenticationType | Di base o anonima | Sì |
| username |Specificare il nome utente per l'account utente. | Sì, se authenticationType è impostato su Basic. |
| password | Specifica la password per l'account utente. | Sì, se authenticationType è impostato su Basic. |
| gatewayName | Il nome del gateway che viene usato per connettersi al database Cassandra locale. | Sì |
| encryptedCredential | Credenziali crittografate dal gateway. | No | 

## Proprietà CassandraTable

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **CassandraTable** presenta le proprietà seguenti

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| keyspace | Nome del keyspace o schema nel database Cassandra. | Sì, se **query** per **CassandraSource** non è definito. |
| tableName | Nome della tabella in un database Cassandra. | Sì, se **query** per **CassandraSource** non è definito. |


## Proprietà del tipo CassandraSource
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

In caso di origine di tipo **CassandraSource**, nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Usare la query personalizzata per leggere i dati. | Query SQL-92 o query CQL. Vedere il [riferimento a CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando si usa una query SQL, specificare **nome keyspace.nome tabella** per indicare la tabella su cui eseguire la query. | No (se tableName e keyspace sul set di dati sono definiti). |
| consistencyLevel | Il livello di coerenza specifica quante repliche devono rispondere a una richiesta di lettura prima della restituzione dei dati all'applicazione client. Cassandra controlla il numero di repliche specificato perché i dati soddisfino la richiesta di lettura. | ONE, TWO, THREE, QUORUM, ALL, LOCAL\_QUORUM, EACH\_QUORUM, LOCAL\_ONE. Per informazioni dettagliate, vedere [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) (Configurazione della coerenza dei dati). | No. Il valore predefinito è ONE. |  


### Mapping dei tipi per Cassandra
Tipo Cassandra | Tipo basato su .Net
--------------- | ---------------
ASCII |	String 
BIGINT | Int64
BLOB | Byte
BOOLEAN | Boolean
DECIMAL | Decimale
DOUBLE | Double
FLOAT | Single
INET | String
INT | Int32
TEXT | String
TIMESTAMP | DateTime
TIMEUUID | Guid
UUID | Guid
VARCHAR | String
VARINT | Decimal

> [AZURE.NOTE]  
Per i tipi di raccolta (mappa, set, elenco e così via), vedere la sezione [Uso delle raccolte con una tabella virtuale](#work-with-collections-using-virtual-table).
> 
> I tipi definiti dall'utente non sono supportati.
> 
> La lunghezza di una colonna Binary o String non può essere maggiore di 4000.

## Uso delle raccolte con una tabella virtuale
Azure Data Factory usa un driver ODBC integrato per connettersi ai dati di un database Cassandra e copiarli. Per i tipi di raccolta, fra cui mappa, set ed elenco, il driver normalizza di nuovo i dati in tabelle virtuali corrispondenti. In particolare, se una tabella contiene colonne della raccolta, il driver genera le tabelle virtuali seguenti:

-	Una **tabella di base** che contiene gli stessi dati della tabella reale eccetto le colonne della raccolta. La tabella di base ha lo stesso nome della tabella reale che rappresenta.
-	Una **tabella virtuale** per ogni colonna della raccolta che espande i dati nidificati. Alle tabelle virtuali che rappresentano le raccolte vengono assegnati nomi composti dal nome della tabella reale seguito dal separatore "_vt_" e dal nome della colonna.

Le tabelle virtuali fanno riferimento ai dati nella tabella reale, consentendo al driver di accedere ai dati denormalizzati. Per i dettagli vedere la sezione Esempio. È possibile accedere al contenuto delle raccolte Cassandra eseguendo query e join sulle tabelle virtuali.

È possibile usare la [Copia guidata](data-factory-data-movement-activities.md#data-factory-copy-wizard) per visualizzare in modo intuitivo l'elenco delle tabelle nel database Cassandra, comprese le tabelle virtuali, e visualizzare in anteprima i dati all'interno. È inoltre possibile costruire una query nella Copia guidata ed eseguire la convalida per visualizzare il risultato.

### Esempio
Ad esempio, "ExampleTable" è una tabella di un database Cassandra contenente una colonna chiave primaria integer denominata "pk\_int", una colonna testo denominata value, una colonna elenco, una colonna mappa e una colonna set (denominata "StringSet").

pk\_int | Valore | Elenco | Mappa |	StringSet
------ | ----- | ---- | --- | --------
1 | "valore di esempio 1" | ["1", "2", "3"] | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "valore di esempio 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

Il driver genera più tabelle virtuali per rappresentare questa singola tabella. Le colonne chiave esterna nelle tabelle virtuali fanno riferimento alle colonne chiave primaria nella tabella reale e indicano a quale riga della tabella reale corrisponde la riga della tabella virtuale.

La prima tabella virtuale è la tabella di base denominata "ExampleTable", illustrata nella tabella di seguito. La tabella di base contiene gli stessi dati della tabella di database originale, tranne le raccolte, che vengono omesse da questa tabella ed espanse in altre tabelle virtuali.

pk\_int | Valore
------ | -----
1 | "valore di esempio 1"
3 | "valore di esempio 3"

Le tabelle seguenti illustrano le tabelle virtuali che normalizzano di nuovo i dati dalle colonne elenco, mappa e StringSet. Le colonne con nomi che terminano con “\_index” o “\_key” indicano la posizione dei dati all'interno dell'elenco o della mappa originale. Le colonne con nomi che terminano con “\_value” contengono i dati espansi dalla raccolta.

#### Tabella “ExampleTable\_vt\_List”:
pk\_int | List\_index | List\_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### Tabella “ExampleTable\_vt\_Map”:
pk\_int | Map\_key | Map\_value
------ | ------- | ---------
1 | S1 | A
1 | S2 | b
3 | S1 | t

#### Tabella “ExampleTable\_vt\_StringSet”:
pk\_int | StringSet\_value
------ | ---------------
1 | A
1 | B
1 | C
3 | A
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Ottimizzazione delle prestazioni  
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!---HONumber=AcomDC_0928_2016-->