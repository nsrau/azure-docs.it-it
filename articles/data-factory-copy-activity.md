<properties title="Copy data with Azure Data Factory" pageTitle="Copiare i dati con Data factory di Azure" description="Informazioni su come usare l'attività di copia in Data factory di Azure per copiare dati tra origini dati." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Copiare i dati con Data factory di Azure (copia attività)
È possibile usare l'**attività di copia** in una pipeline per copiare i dati da un'origine a un sink (destinazione) in un batch. L'attività di copia può essere usata negli scenari seguenti:

- **Entrata in Azure**. In questo scenario, i dati vengono copiati da un'origine di dati locale (ad esempio: SQL Server) in un archivio dati di Azure (ad esempio: Blob di Azure, tabelle di Azure o Database di SQL Azure) per gli scenari secondari seguenti:
	- Raccolta dei dati in una posizione centralizzata in Azure per un'ulteriore elaborazione.
	- Migrazione dei dati da piattaforme cloud locali o non Azure in Azure.
	- Archiviazione o backup dei dati in Azure per l'archiviazione a livelli conveniente.
- **Uscita da Azure**. In questo scenario, i dati vengono copiati da Azure (ad esempio: Blob di Azure, tabelle di Azure o Database di SQL Azure) in data mart e data warehouse locali (ad esempio: SQL Server) per gli scenari secondari seguenti:
	- Trasferimento dei dati in locale dovuto alla mancanza di supporto del data warehouse nel cloud.
	- Trasferimento dei dati in locale per sfruttare i vantaggi di soluzioni locali o infrastrutture di creazione report esistenti.
	- Archiviazione o backup dei dati in locale per l'archiviazione a livelli
- **Copia da Azure ad Azure**. In questo scenario, i dati distribuiti tra le origini dati di Azure vengono aggregati in un archivio dati centralizzato di Azure. Esempi: da tabella di Azure a BLOB Azure, da BLOB Azure a tabella di Azure, da tabella di Azure a SQL di Azure, da BLOB Azure a SQL di Azure.

Per un'esercitazione che illustra come copiare dati da una risorsa di archiviazione BLOB di Azure a un database SQL di Azure usando l'attività di copia, vedere [Introduzione a Data factory di Azure][adfgetstarted]. Per una procedura dettagliata che mostra come copiare dati da un database SQL Server locale a un BLOB di Azure usando l'attività di copia, vedere [Consentire alle pipeline di usare dati locali][use-onpremises-datasources].


## Attività di copia - componenti
L'attività di copia contiene i componenti seguenti: 

- **Tabella di input**. Una tabella è un set di dati che contiene uno schema ed è di forma rettangolare. Il componente della tabella di input 
- descrive i dati di input per l'attività che includono quanto segue: nome della tabella, tipo di tabella e servizio collegato che fa riferimento a un'origine dati che contiene i dati di input.
- **Tabella di output**. La tabella di output descrive i dati di output per l'attività che includono quanto segue: nome della tabella, tipo di tabella e servizio collegato che fa riferimento a un'origine dati che contiene i dati di output.
- **Regole di trasformazione**. Le regole di trasformazione specificano come estrarre i dati di input dall'origine e caricare i dati di output nel sink, e così via.
 
Un'attività di copia può avere un'unica **tabella di input** e un'unica **tabella di output**.

## JSON per attività di copia
Una pipeline è costituita da una o più attività. Le attività nelle pipeline vengono definite nella sezione **activities []**. Il JSON per una pipeline è il seguente:
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

Ogni attività all'interno della sezione **activities** presenta la seguente struttura di livello superiore. La proprietà **type** deve essere impostata su **CopyActivity**. Un'attività di copia può avere un'unica tabella di input e un'unica tabella di output.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

Nella tabella seguente vengono descritti i tag usati con una sezione di attività. 

<table border="1">	
	<tr>
		<th align="left">Tag</th>
		<th align="left">Descrizione</th>
		<th align="left">Obbligatorio</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>Nome dell'attività.</td>
		<td>S</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>Testo descrittivo per lo scopo dell'attività.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>type</td>
		<td>Specifica il tipo di attività. <br/><br/>La proprietà <b>type</b> deve essere impostata su <b>CopyActivity</b>.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>Tabelle di input usate dall'attività.  Specificare solo una tabella di input per l'attività di copia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Tabelle di output usate dall'attività.  Specificare solo una tabella di output per l'attività di copia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Le proprietà nel tag transformation dipendono dal tipo.  L'<b>attività di copia</b> richiede di specificare un sezione <b>source</b> e <b>sink</b> nella sezione <b>transformation</b>. Altri dettagli vengono forniti più avanti in questo articolo. </td>
		<td>S</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Criteri che influiscono sul comportamento di runtime dell'attività. Se vengono omessi, verranno usati i valori predefiniti.</td>
		<td>N</td>
	</tr>


</table>

Vedere il [riferimento agli script JSON][json-script-reference] per informazioni dettagliate sulle proprietà/tag JSON.

## Attività di copia - esempio
In questo esempio, vengono definite una tabella di input e una tabella di output, che vengono quindi usate in un'attività di copia in una pipeline che copia i dati da un database di SQL Server locale in un BLOB di Azure.

**Presupposti**
Ai seguenti elementi di Data factory di Azure viene fatto riferimento negli script JSON di esempio che seguono:

* Gruppo di risorse denominato **ADF**.
* Una Data factory di Azure denominata **CopyFactory**.
* Un servizio collegato denominato **MyOnPremisesSQLDB** che punta a un database SQL Server locale.
* Un servizio collegato denominato **MyAzureStorage** che fa riferimento a una risorsa di archiviazione BLOB di Azure.

### Tabella di input JSON
Il seguente script JSON definisce una tabella di input che fa riferimento a una tabella SQL: **MyTable** in un database di SQL Server locale definito dal servizio collegato **MyOnPremisesSQLDB**. Si noti che **name** è il nome della tabella di Data factory di Azure e **tableName** è il nome della tabella SQL in un database di SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

Il comando PowerShell di Azure di esempio seguente usa **New-AzureDataFactoryTable** che usa un file JSON contenente lo script sopra riportato per creare una tabella (**MyOnPremTable**) in una Data factory di Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

Vedere [Riferimento ai cmdlet][cmdlet-reference] per dettagli sui cmdlet Data factory. 

### Tabella di output JSON
Il seguente script JSON definisce una tabella di output: **MyDemoBlob**, che fa riferimento a un BLOB di Azure: **MyBlob** nella cartella BLOB: **MySubFolder** nel contenitore BLOB: **MyContainer**.
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Il comando PowerShell di Azure di esempio seguente usa **New-AzureDataFactoryTable** che usa un file JSON contenente lo script sopra riportato per creare una tabella (**MyDemoBlob**) in una Data factory di Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### Pipeline (con attività di copia) JSON
In questo esempio, una pipeline: **CopyActivityPipeline** è definita con le proprietà seguenti: 

- La proprietà **type** è impostata su **CopyActivity**.
- **MyOnPremTable** viene specificato come input (tag **inputs**).
- **MyAzureBlob** viene specificato come output (tag **outputs**).
- **La sezione **transformation contiene due sezioni secondarie: **source** e **sink**. Il tipo per source è impostato su **SqlSource** e il tipo per sink è impostato su **BlobSink**. sqlReaderQuery** definisce la trasformazione (proiezione) da eseguire sull'origine.** Per informazioni dettagliate su tutte le proprietà, vedere i [riferimenti allo scripting JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}


Il comando PowerShell di Azure di esempio seguente usa **New-AzureDataFactoryPipeline** che usa un file JSON contenente lo script sopra riportato per creare una pipeline (**CopyActivityPipeline**) in una Data factory di Azure: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## Input e output supportati
L'esempio precedente usava SqlSource come origine e BlobSink come sink nella sezione transformation. Nella tabella seguente sono elencate le origini e i sink supportati dall'attività di copia. 

<table border="1">	
	<tr>
		<th><i>Sink/origine<i></th>
		<th>BLOB Azure</th>
		<th>Tabella di Azure</th>
		<th>Database SQL di Azure</th>
		<th>Server SQL locale</th>
		<th>SQL Server in IaaS</th>
	</tr>	

	<tr>
		<td><b>BLOB Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Tabella di Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>Database SQL di Azure</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>Server SQL locale</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>SQL Server in IaaS</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


Nella tabella seguente sono elencati i tipi di origine e sink che è possibile usare in un file JSON per una pipeline che contiene un'attività di copia.


<table border="1">	
	<tr>
		<th></th>
		<th align="left">Tipo di origine</th>
		<th align="left">Tipo di sink</th>
	</tr>	

	<tr>
		<td><b>BLOB Azure</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Tabella di Azure</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>Database SQL di Azure</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL Server locale</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>SQL su IaaS</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

Nella tabella seguente sono elencate le proprietà supportate da origini e sink.

<table border="1">

	<tr>
	<th align="left">Origine/Sink</th>
	<th align="left">Proprietà supportata</th>
	<th align="left">Descrizione</th>
	<th align="left">Valori consentiti</th>
	<th align="left">Obbligatorio</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>Specifica se considerare una stringa vuota o null come valore null.</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>Indicare quante righe devono essere ignorate.</td>
		<td>Numero intero compreso tra 0 e Max.</td>
		<td>N</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>Usare la query personalizzata per leggere i dati.</td>
		<td>Stringa di query di tabella di Azure.<br/>Esempio: "ColumnA eq ValueA"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>Indica se ignorare l'eccezione: "table does not exist".</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>Usare la query personalizzata per leggere i dati.</td>
		<td>Stringa di query SQL.<br/><br/> Ad esempio: "Select * from MyTable".<br/><br/> Se non specificato, selezionare <columns defined in structure> dalla query MyTable.</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>Valore predefinito della chiave di partizione che può essere usato dal sink.</td>
		<td>Valore stringa.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>Nome della colonna specificato dall'utente, i cui valori di colonna vengono usati come chiave di partizione.<br/><br/> Se non specificato, AzureTableDefaultPartitionKeyValue viene usato come chiave di partizione.</td>
		<td>Nome colonna.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>Valori di colonna del nome di colonna specificato da usare come una chiave di riga.<br/><br/>Se non specificato, viene usato un GUID per ogni riga.</td>
		<td>Nome colonna.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>La modalità per inserire dati in tabelle di Azure.</td>
		<td>"merge"<br/><br/>"replace"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>Inserisce dati nella tabella di Azure quando viene raggiunto il writeBatchSize o writeBatchTimeout.</td>
		<td>Numero intero compreso tra 1 e 100 (unità = conteggio righe)</td>
		<td>N<br/><br/>(Impostazione predefinita = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>Inserisce i dati nella tabella di Azure quando viene raggiunto writeBatchSize o writeBatchTimeout</td>
		<td>(Unità = timespan)<br/><br/>Esempio: "00:20:00" (20 minuti)</td>
		<td>N<br/><br/>(il valore predefinito è il timeout del client di archiviazione pari a 90 secondi)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>Specifica il nome della stored procedure per i dati upsert (aggiornamento/inserimento) nella tabella di destinazione.</td>
		<td>Nome della stored procedure.</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>Specifica il tipo di tabella da usare nella stored procedure precedente.</td>
		<td>Nome del tipo di tabella.</td>
		<td>N</td>
	</tr>
</table>

### SQL in Infrastructure-as-a-Service (IaaS)
Per SQL in IaaS, è supportato Azure come provider IaaS. Sono supportate le topologie VPN e di rete seguenti. Si noti che il Gateway di gestione dati è necessario per i casi 2 e 3, ma non per il caso 1. Per informazioni dettagliate sul Gateway di gestione dati, vedere [Abilitare le pipeline ad accedere ai dati locali][use-onpremises-datasources].

1.	Macchina virtuale con nome DNS pubblico e porta pubblica statica: mapping di porta privata
2.	Macchina virtuale con nome DNS pubblico senza endpoint SQL esposto
3.	Rete virtuale
	<ol type='a'>
	<li>VPN del cloud di Azure con topologia seguente alla fine dell'elenco. </li>	
	<li>Macchina virtuale con VPN da sito a sito e da locale al cloud usando la rete virtuale di Azure.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Filtro di colonna usando la definizione della struttura
A seconda del tipo di tabella, è possibile specificare un subset di colonne dall'origine specificando un minor numero di colonne nella definizione **Structure** della definizione della tabella rispetto a quelle presenti nell'origine dati sottostante. Nella tabella seguente vengono fornite informazioni sulla logica di filtro colonne per diversi tipi di tabella. 

<table>

	<tr>
		<th align="left">Tipo di tabella</th>
		<th align="left">Logica di filtro colonne</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>La definizione <b>Structure</b> nella tabella JSON deve corrispondere alla struttura del BLOB.  Per selezionare un subset delle colonne, usare la funzionalità di mapping delle colonne descritta nella sezione successiva: Regole di trasformazione - Mapping di colonne.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation e OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>Se la proprietà <b>SqlReaderQuery</b> viene specificata come parte della definizione di attività di copia, la definizione <b>Structure</b> della tabella deve allinearsi con le colonne selezionate nella query.</p>
			<p>Se la proprietà <b>SqlReaderQuery</b> non viene specificata, l'attività di copia crea automaticamente una query SELECT in base alle colonne specificate nella definizione <b>Structure</b> della definizione della tabella.</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			La sezione <b>Structure</b> nella definizione della tabella può contenere un set completo o un subset di colonne nella tabella sottostante in Azure.
		</td>
	<tr>

</table> 

## Regole di trasformazione - Mapping di colonne.
Il mapping di colonne consente di specificare come mappare ler colonne nella tabella di origine rispetto alle colonne della tabella di sink. Supporta gli scenari seguenti:

- Mapping di tutte le colonne della tabella di origine "structure" alla tabella di destinazione "structure".
- Un subset delle colonne nella tabella di origine "structure" viene mappato alla tabella di destinazione "structure".

Non supporta quanto segue, generando un'eccezione: 

- Numero di colonne inferiore o superiore nella destinazione.
- Mapping duplicato.
- Risultato della query SQL privo di un nome di colonna

#### Esempio 1: mapping di colonne da SQL Server al BLOB di Azure
In questo esempio, la **tabella di input** è definita come segue. La tabella di input ha una struttura che punta a una tabella SQL in un database di SQL Server.
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
				"location":
				{
					"type": "OnPremisesSqlServerTableLocation",
					"tableName": "MyTable",
					"linkedServiceName": "MyOnPremisesSQLDB"
				},
				"availability":	
				{
    				"frequency": "Hour",
    				"interval": 1
				}
     		}
		}

In questo esempio, la **tabella di output** è definita come segue. La tabella di output ha una struttura che punta a un BLOB a tabella SQL in un'archiviazione BLOB di Azure.
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
    	    	"format":
    	    	{
    	        	"type": "TextFormat",
		            "columnDelimiter": ",",
    		        "rowDelimiter": ";",
    		        "EscapeChar": "$",
    		        "NullValue": "NaN"
    		    }
			},
			"availability":
			{
    			"frequency": "Hour",
    			"interval": 1
			}
		}
	}	

Se non si specifica un oggetto **fileName** per una **tabella di input**, tutti i file/BLOB della cartella di input (**folderPath**) vengono considerati input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Vedere i file di esempio nell'[esercitazione][adf-tutorial] per gli esempi.

Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid>.txt (ad esempio:Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

Per impostare **folderPath** e **fileName** dinamicamente in base all'ora **SliceStart**, usare la proprietà **partitionedBy**. Nell'esempio seguente **folderPath** usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Ad esempio, se una sezione viene generata per 2014-10-20T08:00:00, folderName è impostato su wikidatagateway/wikisampledataout/2014/10/20 e fileName è impostato su 08.csv. 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

In questo esempio, viene definita un'attività in una pipeline come indicato di seguito. Le colonne dell'origine vengono mappate alle colonne nel sink (**columnMappings**) usando la proprietà **Translator**.

##### Esempio: definire il mapping di colonne

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![Column Mapping][image-data-factory-column-mapping-1]

##### Esempio 2: mapping di colonne con query SQL da SQL Server al BLOB di Azure
In questo esempio, una query SQL (vedere la tabella nell'esempio precedente) viene usata per estrarre dati da un SQL Server locale e viene eseguito il mapping delle colonne dai risultati delle query all'elemento di origine e quindi all'elemento di destinazione. Ai fini di questo esempio, la query restituisce 5 colonne.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![Column Mapping 2][image-data-factory-column-mapping-2]

#### Gestione del tipo di dati nell'attività di copia

I tipi di dati specificati nella sezione Structure della definizione Table vengono accettati solo per **BlobSource**.  Nella tabella seguente viene descritta la modalità di gestione dei tipi di dati per altri tipi di origine e sink.

<table>	
	<tr>
		<th align="left">Origine/Sink</th>
		<th align="left">Logica di gestione di tipi dei dati</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati.  I tipi di dati definiti nel database SQL sottostante verranno usati per l'estrazione dei dati durante l'attività di copia.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati.  Verranno confrontati i tipi di dati di origine e destinazione sottostante e verrà eseguita la conversione implicita dei tipi se sono presenti tipi non corrispondenti.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p>Durante il trasferimento da <b>BlobSource</b> a <b>BlobSink</b>, non viene eseguita alcuna trasformazione di tipo. I tipi definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati.  Per destinazioni differenti da <b>BlobSink</b>, i tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella verranno accettati.</p>
		<p>
		Se la sezione <b>Structure</b> non è specificata nella definizione della tabella, la gestione dipende dalla proprietà <b>format</b> di <b>BlobSink</b>:
		</p>
		<ul>
			<li> <b>TextFormat:</b> tutti i tipi di colonna vengono considerati come stringa e tutti i nomi di colonna sono impostati come "Prop_<0-N>"</li> 
			<li><b>AvroFormat:</b> usare i tipi di colonna predefiniti e i nomi nel file Avro.</li> 
			<li><b>JsonFormat:</b> tutti i tipi di colonna vengono considerati come stringa e usano i nomi di colonna predefiniti nel file Json.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella di input vengono ignorati.  Verranno usati i tipi di dati definiti nell'archivio dati di input sottostante.  Le colonne verranno specificate come nullable per la serializzazione Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati.  Verranno usati i tipi di dati definiti nella tabella di Azure sottostante.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati.  Verranno usati i tipi di dati definiti nell'archivio dati di input sottostante.</td>
	</tr>

</table>

## Limitazioni

> [WACOM.NOTE] Quando una pipeline viene sospesa o eliminata o quando viene eliminato un data factory, l'operazione di copia in corso non viene sospesa. Continuerà a essere eseguita fino al completamento. Tuttavia, l'operazione di copia che implica un'origine dei dati in locale può essere arrestata riavviando il servizio Gateway di gestione dati usando l'apposita Gestione configurazione o l'applet Servizi. 



## Procedure dettagliate
Per un'esercitazione che illustra come copiare dati da una risorsa di archiviazione BLOB di Azure a un database SQL di Azure usando l'attività di copia, vedere [Introduzione a Data factory di Azure][adfgetstarted].
 
Per una procedura dettagliata che mostra come copiare dati da un database SQL Server locale a un BLOB di Azure usando l'attività di copia, vedere [Consentire alle pipeline di usare dati locali][use-onpremises-datasources].



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
