<properties 
	pageTitle="Copiare i dati con Data factory di Azure" 
	description="Informazioni su come usare l'attività di copia in Data factory di Azure per copiare dati tra origini dati." 
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
	ms.date="04/02/2015" 
	ms.author="spelluru"/>

# Copiare i dati con Data factory di Azure \(copia attività\)
## Panoramica
È possibile usare **l'attività di copia** in una pipeline per copiare i dati da un'origine a un sink \(destinazione\) in un batch. L'attività di copia può essere usata negli scenari seguenti:

- **Entrata in Azure**. In questo scenario, i dati vengono copiati da un'origine dati locale \(ad esempio: SQL Server\) in un archivio dati di Azure \(ad esempio: Database SQL Azure, tabelle di Azure o BLOB di Azure\) per gli scenari secondari seguenti:
	- Raccolta dei dati in una posizione centralizzata in Azure per un'ulteriore elaborazione.
	- Migrazione dei dati da piattaforme cloud locali o non Azure in Azure.
	- Archiviazione o backup dei dati in Azure per l'archiviazione a livelli conveniente.
- **Uscita da Azure**. In questo scenario, i dati vengono copiati da Azure \(ad esempio: Database SQL Azure, tabelle di Azure o BLOB di Azure\) in data mart e data warehouse locali \(ad esempio: SQL Server\) per gli scenari secondari seguenti:
	- Trasferimento dei dati in locale dovuto alla mancanza di supporto del data warehouse nel cloud.
	- Trasferimento dei dati in locale per sfruttare i vantaggi di soluzioni locali o infrastrutture di creazione report esistenti.
	- Archiviazione o backup dei dati in locale per l'archiviazione a livelli
- **Copia da Azure ad Azure**. In questo scenario, i dati distribuiti tra le origini dati di Azure vengono aggregati in un archivio dati centralizzato di Azure. Esempi: da tabella di Azure a BLOB Azure, da BLOB Azure a tabella di Azure, da tabella di Azure a SQL di Azure, da BLOB Azure a SQL di Azure.

Per altre informazioni, è possibile:

- Vedere il video [Introduzione all'attività di copia con Data factory di Azure][copy-activity-video]
- Per un'esercitazione che illustra come copiare dati da una risorsa di archiviazione BLOB di Azure a un database SQL di Azure usando l'attività di copia, vedere [Introduzione a Data factory di Azure][adfgetstarted]. 
- Per una procedura dettagliata che mostra come copiare dati da un database locale di SQL Server a una risorsa di archiviazione BLOB di Azure usando l'attività di copia, vedere [Consentire alle pipeline di usare dati locali][use-onpremises-datasources].


## Sink e origini supportate
L'attività di copia supporta gli scenari seguenti di spostamento dei dati:

- Copiare i dati da un BLOB di Azure a un BLOB di Azure, tabelle di Azure, Database SQL di Azure, SQL Server locale o SQL Server in IaaS.
- Copiare i dati da un database SQL di Azure a un BLOB di Azure, tabelle di Azure, Database SQL di Azure, SQL Server locale o SQL Server in IaaS
- Copiare i dati da una tabella di Azure a un BLOB di Azure, tabelle di Azure o Database SQL di Azure.
- Copiare i dati da un SQL Server locale/SQL Server in IaaS a un BLOB di Azure o Database SQL Azure
- Copiare i dati da un database Oracle locale a un BLOB di Azure
- Copiare i dati da un file system locale a un BLOB di Azure
 

<table border="1">	
	<tr>
		<th><i>Origine/Sink<i></th>
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
		<td>X</td>
		<td>X</td>
	</tr>


	<tr>
		<td><b>Server SQL locale</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>SQL Server in IaaS</b></td>
		<td>X</td>
		<td></td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>File System locale</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>Database Oracle in locale</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>


</table>

### SQL in Infrastructure-as-a-Service \(IaaS\)
Per SQL in IaaS, è supportato Azure come provider IaaS. Sono supportate le topologie VPN e di rete seguenti. Tenere presente che il Gateway di gestione dati è necessario per i casi 2 e 3, ma non per il caso 1. Per informazioni dettagliate sul Gateway di gestione dati, vedere [Consentire alle pipeline di usare dati locali][use-onpremises-datasources].

1.	Macchina virtuale con nome DNS pubblico e porta pubblica statica: mapping delle porte private
2.	Macchina virtuale con nome DNS pubblico senza endpoint SQL esposto
3.	Rete virtuale <ol type='a'> <li> VPN del cloud di Azure con topologia seguente alla fine dell'elenco. </li> <li>Macchina virtuale con VPN da sito a sito e da locale al cloud usando la rete virtuale di Azure.</li> </ol> ![Data factory con l'attività di copia][image-data-factory-copy-actvity]

## Attività di copia - componenti
L'attività di copia contiene i componenti seguenti:

- **Tabella di input**. Una tabella è un set di dati che contiene uno schema ed è di forma rettangolare. La tabella di input descrive i dati di input per l'attività, tra cui il nome della tabella, il tipo di tabella e il servizio collegato che fa riferimento a un'origine dati contenente i dati di input.
- **Tabella di output**. La tabella di output descrive i dati di output per l'attività, tra cui il nome della tabella, il tipo di tabella e il servizio collegato che fa riferimento a un'origine dati contenente i dati di output.
- **Regole di trasformazione**. Le regole di trasformazione specificano come estrarre i dati di input dall'origine, caricare i dati di output nel sink e così via.
 
Un'attività di copia può avere un'unica **tabella di input** e un'unica **tabella di output**.

## <a name="CopyActivityJSONSchema"></a>JSON per attività di copia
Una pipeline è costituita da una o più attività. Le attività nelle pipeline vengono definite nella sezione **activities[]**. Il JSON per una pipeline è il seguente:
         
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

Ogni attività all'interno della sezione **activities** presenta la seguente struttura di livello superiore. La proprietà **type** deve essere impostata su **CopyActivity**. Un'attività di copia può avere una sola tabella di input e una sola tabella di output.
         

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
		<td>Tabelle di input usate dall'attività. Specificare una sola tabella di input per l'attività di copia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>Tabelle di output usate dall'attività. Specificare una sola tabella di output per l'attività di copia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>Le proprietà nella trasformazione dipendono dal tipo. L'<b>attività di copia</b> richiede di specificare una sezione <b>source</b> e <b>sink</b> nella sezione <b>transformation</b>. Altri dettagli vengono forniti più avanti in questo articolo. </td>
		<td>S</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>Criteri che influiscono sul comportamento di runtime dell'attività. Se vengono omessi, verranno usati i valori predefiniti.</td>
		<td>N</td>
	</tr>


</table>

Per informazioni dettagliate su proprietà/tag JSON, vedere [Riferimento agli script JSON][json-script-reference].

### tipi di origine e sink
Per un elenco di tipi di origine e sink e le proprietà supportate da questi tipi, vedere l'argomento [Origini e sink supportati][msdn-supported-sources-sinks] in MSDN Library.

## Attività di copia - esempio
In questo esempio, vengono definite una tabella di input e una tabella di output, che vengono quindi usate in un'attività di copia in una pipeline che copia i dati da un database di SQL Server locale in un BLOB di Azure.

**Presupposti** Ai seguenti elementi di Data factory di Azure viene fatto riferimento negli script JSON di esempio che seguono:

* Gruppo di risorse denominato **ADF**.
* Una Data factory di Azure denominata **CopyFactory**.
* Un servizio collegato denominato **MyOnPremisesSQLDB** che punta a un database SQL Server locale.
* Un servizio collegato denominato **MyAzureStorage** che fa riferimento a una risorsa di archiviazione BLOB di Azure.

### Tabella di input JSON
Il seguente script JSON definisce una tabella di input che fa riferimento a una tabella di SQL: **MyTable** in un database di SQL Server locale definito dal servizio collegato **MyOnPremisesSQLDB**. Si noti che **name** è il nome della tabella di Data factory di Azure e **tableName** è il nome della tabella SQL in un database di SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
	{ "location": { "type": "OnPremisesSqlServerTableLocation", "tableName": "MyTable", "linkedServiceName": "MyOnPremisesSQLDB" }, "availability": { "frequency": "Hour", "interval": 1 } } }

Il comando PowerShell di Azure di esempio seguente usa **New-AzureDataFactoryTable** che usa un file JSON contenente lo script sopra riportato per creare una tabella \(\*\*MyOnPremTable\*\*\) in una Data factory di Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

Per dettagli sui cmdlet di Data factory, vedere [Riferimento ai cmdlet][cmdlet-reference].

### Tabella di output JSON
Il seguente script JSON definisce una tabella di output: **MyDemoBlob**, che fa riferimento a un BLOB di Azure: **MyBlob** nella cartella BLOB: **MySubFolder** nel contenitore BLOB: **MyContainer**.
         
	{
	"name": "MyDemoBlob", "properties": { "location": { "type": "AzureBlobLocation", "folderPath": "MyContainer/MySubFolder", "fileName": "MyBlob", "linkedServiceName": "MyAzureStorage", "format": { "type": "TextFormat", "columnDelimiter": ",", "rowDelimiter": ";", "EscapeChar": "$", "NullValue": "NaN" } }, "availability": { "frequency": "Hour", "interval": 1 } } }

Il comando PowerShell di Azure di esempio seguente usa **New-AzureDataFactoryTable** che usa un file JSON contenente lo script sopra riportato per creare una tabella \(\*\*MyDemoBlob\*\*\) in una Data factory di Azure: **CopyFactory**.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


### Pipeline \(con attività di copia\) JSON
In questo esempio, una pipeline **CopyActivityPipeline** è definita con le proprietà seguenti:

- La proprietà **type** è impostata su **CopyActivity**.
- **MyOnPremTable** viene specificato come input \(tag \*\*inputs\*\*\).
- **MyAzureBlob** viene specificato come output \(tag \*\*outputs\*\*\).
- La sezione **Transformation** contiene due sezioni secondarie: **source** e **sink**. Il tipo per source è impostato su **SqlSource** e il tipo per sink è impostato su **BlobSink**. **sqlReaderQuery** definisce la trasformazione \(proiezione\) da eseguire sull'origine. Per informazioni dettagliate su tutte le proprietà, vedere [Riferimento agli script JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      { "name": "CopyActivity", "description": "description", "type": "CopyActivity", "inputs": [ { "name": "MyOnPremTable" } ], "outputs": [ { "name": "MyAzureBlob" } ], "transformation": { "source": { "type": "SqlSource", "sqlReaderQuery": "select \* from MyTable" }, "sink": { "type": "BlobSink" } } } \] } }


 Il comando PowerShell di Azure di esempio seguente usa **New-AzureDataFactoryPipeline** che usa un file JSON contenente lo script sopra riportato per creare una pipeline \(\*\*CopyActivityPipeline\*\*\) in una Data factory di Azure: **CopyFactory**.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>

> [AZURE.NOTE]Per ulteriori esempi di utilizzo dell'attività di copia, vedere gli [esempi sull'utilizzo dell'attività di copia in Data factory di Azure][copy-activity-examples].

## Sicurezza
Questa sezione include le linee guida sulla sicurezza generali e procedure consigliate che consentono di stabilire un accesso protetto agli archivi di dati per l'attività di copia.

Per gli archivi dati che offrono una connessione HTTPS, scegliere la connessione HTTPS per l'attività di copia al fine di stabilire una comunicazione protetta sulla rete. Ad esempio, per **Archiviazione di Azure**, usare **DefaultEndpointsProtocol=https** che è nella stringa di connessione.

Per **Database SQL di Azure**, richiedere in modo esplicito una connessione crittografata e non ritenere attendibili i certificati server per evitare l'attacco "man in the middle". A tale scopo, usare **Encrypt=True** e **TrustServerCertificate=False** nella stringa di connessione. Per informazioni dettagliate vedere [Linee guida e limitazioni per la sicurezza per il database SQL di Azure](https://msdn.microsoft.com/library/azure/ff394108.aspx).

Per i database tradizionali, ad esempio **SQL Server**, soprattutto quando le istanze sono in una macchina virtuale di Azure, abilitare l'opzione di connessione crittografata tramite la configurazione di un certificato firmato con **Encrypt=True** e **TrustServerCertificate=False** nella stringa di connessione. Per altre informazioni, vedere [Abilitazione di connessioni crittografate al Motore di database]\(https://msdn.microsoft.com/library/ms191192(v=sql.110).aspx\) e [Sintassi della stringa di connessione](https://msdn.microsoft.com/library/ms254500.aspx).

## Scenari avanzati
- **Filtro di colonna usando la definizione della struttura**. A seconda del tipo di tabella, è possibile specificare un subset di colonne dall'origine specificando un minor numero di colonne nella definizione **Structure** della definizione della tabella rispetto a quelle presenti nell'origine dati sottostante.
- **Regole di trasformazione - Mapping di colonne**. Il mapping di colonne consente di specificare come mappare le colonne nella tabella di origine rispetto alle colonne della tabella di sink.
- **Gestione del tipo di dati nell'attività di copia**. Illustra i casi in cui i tipi di dati specificati nella sezione Structure della definizione Table vengono accettati/ignorati.
- **Richiama stored procedure per SQL Sink**. Quando si copiano dati in Database SQL Azure o SQL Server, una stored procedure di un utente specificato può essere configurata e richiamata.

Per informazioni dettagliate su questi scenari, vedere l'articolo relativo agli [scenari avanzati per l'uso dell'attività di copia con Data factory di Azure][copy-activity-advanced].

## Procedure dettagliate
Per un'esercitazione che illustra come copiare dati da una risorsa di archiviazione BLOB di Azure a un database SQL di Azure usando l'attività di copia, vedere [Introduzione a Data factory di Azure][adfgetstarted].
 
Per una procedura dettagliata che mostra come copiare dati da un database locale di SQL Server a una risorsa di archiviazione BLOB di Azure usando l'attività di copia, vedere la pagina [Consentire alle pipeline di usare dati locali][use-onpremises-datasources]

## Vedere anche
- [Attività di copia - esempi][copy-activity-examples]
- [Video: Introduzione all'attività di copia con Data factory di Azure][copy-activity-video]
- [Argomento Attività di copia in MSDN Library][msdn-copy-activity]
- [Scenari avanzati per l'utilizzo dell'attività di copia con Data factory di Azure][copy-activity-advanced]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[copy-activity-advanced]: data-factory-copy-activity-advanced.md
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=52-->
