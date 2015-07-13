<properties 
	pageTitle="Scenari avanzati per l'utilizzo dell'attività di copia in Data factory di Azure" 
	description="Descrive gli scenari avanzati per l'utilizzo dell'attività di copia in Data factory di Azure." 
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
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="spelluru"/>

# Scenari avanzati per l'utilizzo dell'attività di copia in Data factory di Azure 
## Panoramica
È possibile usare **l'attività di copia** in una pipeline per copiare i dati da un'origine a un sink (destinazione) in un batch. Questo argomento descrive gli scenari avanzati supportati dall'attività di copia. Per una panoramica dettagliata dell'attività di copia e degli scenari principali supportati, vedere [Copiare dati con Data factory di Azure][adf-copyactivity].


## Filtro di colonna usando la definizione della struttura
A seconda del tipo di tabella, è possibile specificare un subset di colonne dall'origine specificando un minor numero di colonne nella definizione **Structure** della definizione della tabella rispetto a quelle presenti nell'origine dati sottostante. Nella tabella seguente vengono fornite informazioni sulla logica di filtro colonne per diversi tipi di tabella.

<table>

	<tr>
		<th align="left">Tipo di tabella</th>
		<th align="left">Logica di filtro colonne</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>La definizione <b>Structure</b> del file JSON della tabella deve corrispondere alla struttura del BLOB. Per selezionare un subset di colonne, usare la funzionalità di mapping delle colonne descritta nella sezione seguente: Regole di trasformazione - Mapping di colonne.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation e OnPremisesSqlServerTableLocation</td>
		<td align="left">
			Se la proprietà <b>SqlReaderQuery</b> viene specificata come parte della definizione di attività di copia, la definizione <b>Structure</b> della tabella deve allinearsi con le colonne selezionate nella query.<br/><br/>
			Se la proprietà <b>SqlReaderQuery</b> non viene specificata, l'attività di copia crea automaticamente una query SELECT in base alle colonne specificate nella definizione <b>Structure</b> della definizione della tabella.
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
Il mapping di colonne consente di specificare come mappare le colonne nella tabella di origine rispetto alle colonne della tabella di sink. Supporta gli scenari seguenti:

- Mapping di tutte le colonne della tabella di origine "structure" alla tabella di destinazione "structure".
- Un subset delle colonne nella tabella di origine "structure" viene mappato alla tabella di destinazione "structure".

Non supporta quanto segue, generando un'eccezione:

- Numero di colonne inferiore o superiore nella destinazione.
- Mapping duplicato.
- Risultato della query SQL privo di un nome di colonna

In particolare, durante la copia di dati tra due BLOB di Azure, l'attività di copia funzionerà per lo più come una copia di dati binari diretta, tranne che nei 3 scenari seguenti:


1. Se le tabelle di input e di output hanno formati diversi, l'attività di copia eseguirà la conversione dei formati.
2. Se la tabella di input viene specificata come cartella che può contenere più file e la tabella di output viene specificata come file, l'attività di copia unirà i file presenti nella cartella di origine in un solo file sink.
3. Se viene specificato "columnMapping", l'attività di copia eseguirà la trasformazione dei dati corrispondenti.


### Esempio 1: mapping di colonne da SQL Server al BLOB di Azure
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

In questo esempio, la **tabella di output** è definita come segue. La tabella di output ha una struttura che punta a un BLOB in un'archiviazione BLOB di Azure.
         
		
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

Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid>.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

#### Esempio: definire il mapping di colonne
In questo esempio, viene definita un'attività in una pipeline come indicato di seguito. Le colonne dell'origine vengono mappate alle colonne nel sink (**columnMappings**) usando la proprietà **Translator**.

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

![Mapping di colonne][image-data-factory-column-mapping-1]

### Esempio 2: mapping di colonne con query SQL da SQL Server al BLOB di Azure
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
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = '{0:yyyyMMdd-HH}'', SliceStart)"
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

![Mapping di colonne 2][image-data-factory-column-mapping-2]

## Gestione del tipo di dati nell'attività di copia

I tipi di dati specificati nella sezione Structure della definizione Table vengono accettati solo per **BlobSource**. La tabella seguente descrive la modalità di gestione dei tipi di dati per altri tipi di origine e sink.

<table>	
	<tr>
		<th align="left">Origine/Sink</th>
		<th align="left">Logica di gestione di tipi dei dati</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati. I tipi di dati definiti nel database SQL sottostante verranno usati per l'estrazione dei dati durante l'attività di copia.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati. Verranno confrontati i tipi di dati nell'origine e nella destinazione sottostanti e verrà eseguita la conversione implicita dei tipi se sono presenti tipi non corrispondenti.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td>Durante il trasferimento da <b>BlobSource</b> a <b>BlobSink</b>, non avviene alcuna trasformazione dei tipi. I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati. Per destinazioni differenti da <b>BlobSink</b>, i tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella verranno accettati.<br/><br/>
		Se la sezione <b>Structure</b> non è specificata nella definizione della tabella, la gestione dipende dalla proprietà <b>format</b> della tabella <b>BlobSource</b>:
		<ul>
			<li> <b>TextFormat:</b> tutti i tipi di colonna vengono considerati come stringa e tutti i nomi di colonna sono impostati come "Prop_&lt;0-N>"</li> 
			<li><b>AvroFormat:</b> usare i tipi di colonna predefiniti e i nomi nel file Avro.</li> 
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati. Verranno usati i tipi di dati definiti nell'archivio dati di input sottostante. Le colonne verranno specificate come nullable per la serializzazione Avro.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati. Verranno usati i tipi di dati definiti nella tabella di Azure sottostante.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>I tipi di dati definiti nella sezione <b>Structure</b> della definizione della tabella vengono ignorati. Verranno usati i tipi di dati definiti nell'archivio dati di input sottostante.</td>
	</tr>

</table>

**Nota:** la tabella di Azure supporta solo un set limitato di tipi di dati. Fare riferimento a [Informazioni sul modello di dati del servizio tabelle][azure-table-data-type].

## Richiamare stored procedure per SQL Sink
Quando si copiano dati nel database SQL Azure o SQL Server, una stored procedure di un utente specificato può essere configurata e richiamata con parametri aggiuntivi.
### Esempio
1. Definire il file JSON della tabella di output come indicato di seguito (considerare la tabella del database SQL di Azure come un esempio):

    	{
    		"name": "MyAzureSQLTable",
    		"properties":
    		{
    			"location":
    			{
    				"type": "AzureSqlTableLocation",
    				"tableName": "Marketing",
    				"linkedServiceName": "AzureSqlLinkedService"
    			},
    			"availability":
    			{
    				"frequency": "Hour",
    				"interval": 1
    			}
    		}
    	}

2. Definire la sezione **SqlSink** nel file JSON dell'attività di copia come indicato di seguito. Per chiamare una stored procedure durante l'inserimento dei dati sono necessarie entrambe le proprietà **SqlWriterStoredProcedureName** e **SqlWriterTableType**.

		"sink":
	    {
			"type": "SqlSink",
	        "SqlWriterTableType": "MarketingType",
		    "SqlWriterStoredProcedureName": "spOverwriteMarketing",	
			"storedProcedureParameters":
					{
                    	"stringData": 
						{
                        	"value": "str1"		
						}
                    }
	    }

3. Nel database definire la stored procedure con lo stesso nome **SqlWriterStoredProcedureName**, che gestisce i dati di input dell'origine specificata e li inserisce nella tabella di output. Si noti che il nome di parametro della stored procedure deve essere identico al **tableName** definito nel file JSON della tabella.

		CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
		AS
		BEGIN
			DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    		INSERT [dbo].[Marketing](ProfileID, State)
    		SELECT * FROM @Marketing
		END


4. Nel database definire il tipo di tabella con lo stesso nome **SqlWriterTableType**. Si noti che lo schema del tipo di tabella deve essere identico allo schema restituito dai dati di input.

		CREATE TYPE [dbo].[MarketingType] AS TABLE(
    	    [ProfileID] [varchar](256) NOT NULL,
    	    [State] [varchar](256) NOT NULL,
    	)

La funzionalità di stored procedure sfrutta i [parametri valutati a livello di tabella][table-valued-parameters].

## Specificare la codifica per i file di testo
Anche se la codifica UTF-8 è molto diffusa, spesso i file di testo nel BLOB di Azure seguono altre codifiche per ragioni storiche. La proprietà **encodingName** consente di specificare la codifica in base al nome di tabella codici per le tabelle di tipo TextFormat. Per l'elenco di nomi di codifica validi, vedere: Proprietà Encoding.EncodingName. Ad esempio: windows-1250 o shift_jis. Il valore predefinito è UTF-8. Vedere [Classe di codifica](https://msdn.microsoft.com/library/system.text.encoding.aspx) per i nomi di codifica validi.

## Vedere anche

- [Esempi per l'utilizzo dell'attività di copia][copy-activity-examples]
- [Copiare i dati con Data factory di Azure][adf-copyactivity]
- [Attività di copia - Informazioni di riferimento sugli script JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Video: Introduzione all'attività di copia con Data factory di Azure][copy-activity-video]


[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx


[adfgetstarted]: data-factory-get-started.md
[adf-copyactivity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[azure-table-data-type]: https://msdn.microsoft.com/it-it/library/azure/dd179338.aspx

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity-advanced/ColumnMappingSample2.png
 

<!---HONumber=62-->