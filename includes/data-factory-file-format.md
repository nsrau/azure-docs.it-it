### Specifica di TextFormat

Se il formato è impostato su **TextFormat**, è possibile specificare le proprietà **facoltative** seguenti nella sezione **Format**.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| columnDelimiter | Carattere usato come separatore di colonne in un file. In questa fase è consentito un solo carattere. Questo tag è facoltativo. Il valore predefinito è la virgola (,). | No |
| rowDelimiter | Carattere usato come separatore di righe in un file. In questa fase è consentito un solo carattere. Questo tag è facoltativo. Il valore predefinito è uno dei seguenti: ["\\r\\n", "\\r"," \\n"]. | No |
| escapeChar | Carattere speciale usato per eseguire l'escape di un delimitatore di colonna visualizzato nel contenuto. Questo tag è facoltativo. Nessun valore predefinito. Per questa proprietà è necessario specificare al massimo un carattere.<br/><br/>Ad esempio, se la virgola (,) viene usata come delimitatore di colonna ma si vuole inserire una virgola nel testo, come in "Hello, world", è possibile definire '$' come carattere di escape e usare la stringa "Hello$, world" nel sorgente.<br/><br/>Si noti che non è possibile specificare sia escapeChar che quoteChar per una tabella. | No | 
| quoteChar | Carattere speciale usato per inserire il valore della stringa tra virgolette. I delimitatori di colonne e righe tra virgolette vengono considerati come parte del valore stringa. Questo tag è facoltativo. Nessun valore predefinito. Per questa proprietà è necessario specificare non più di un carattere .<br/><br/>Ad esempio, se è presente una virgola (,) come delimitatore di colonna, ma si desidera inserire un carattere virgola nel testo (ad esempio: <Hello  world>), è possibile definire '"' come carattere virgolette e usare la stringa <"Hello, world"> nell'origine. Questa proprietà è applicabile sia alle tabelle di input che a quelle di output.<br/><br/>Si noti che non è possibile specificare sia escapeChar che quoteChar per una tabella. | No |
| nullValue | Carattere/i usato/i per rappresentare un valore null nel contenuto del file BLOB. Questo tag è facoltativo. Il valore predefinito è "\\N".<br/><br/>Ad esempio, in base al precedente esempio, "NaN" in BLOB verrà tradotto come valore null durante la copia in SQL Server. | No |
| encodingName | Specificare il nome della codifica. Per l'elenco di nomi di codifica validi, vedere: [Proprietà Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Ad esempio: windows-1250 o shift\_jis. Il valore predefinito è UTF-8. | No | 

#### Esempio di TextFormat
L'esempio seguente illustra alcune delle proprietà del formato per TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Per usare un escapeChar anziché un quoteChar, sostituire la riga con quoteChar con la stringa seguente:

	"escapeChar": "$",

### Specifica di AvroFormat
Se il formato è impostato su AvroFormat, non è necessario specificare proprietà nella sezione Format all'interno della sezione typeProperties. Esempio:

	"format":
	{
	    "type": "AvroFormat",
	}

Per usare il formato Avro in una tabella Hive, fare riferimento all'[esercitazione su Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

### Impostazione di JsonFormat

Se il formato è impostato su **JsonFormat**, è possibile specificare le proprietà **facoltative** seguenti nella sezione **Format**.

| Proprietà | Descrizione | Obbligatoria |
| -------- | ----------- | -------- |
| filePattern | Indicare il modello dei dati archiviati in ogni file JSON. I valori consentiti sono: **setOfObjects** e **arrayOfObjects**. Il valore **predefinito** è: **setOfObjects**. Per i dettagli su questi motivi, vedere le sezioni successive.| No |
| encodingName | Specificare il nome della codifica. Per l'elenco dei nomi di codifica validi, vedere: [Proprietà Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Ad esempio: windows-1250 o shift\_jis. Il valore **predefinito** è: **UTF-8**. | No | 
| nestingSeparator | Carattere utilizzato per separare i livelli di nidificazione. Il valore **predefinito** è **. (punto)**. | No | 


#### Modello di file setOfObjects

Ogni file contiene un solo oggetto o più oggetti con delimitatori di riga/concatenati. Quando si sceglie questa opzione in un set di dati di output, la copia produrrà un singolo file JSON con un oggetto per riga (delimitatoti di riga).

**singolo oggetto**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}

**JSON con delimitatori di riga**

	{"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
	{"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
	{"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concatenati**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}
	{
		"time": "2015-04-29T07:13:21.0220000Z",
		"callingimsi": "466922202613463",
		"callingnum1": "123436380",
		"callingnum2": "789037573",
		"switch1": "US",
		"switch2": "UK"
	}
	{
		"time": "2015-04-29T07:13:21.4370000Z",
		"callingimsi": "466923101048691",
		"callingnum1": "678901578",
		"callingnum2": "345626404",
		"switch1": "Germany",
		"switch2": "UK"
	}


#### Modello di file arrayOfObjects. 

Ogni file contiene una matrice di oggetti.

	[
	    {
	        "time": "2015-04-29T07:12:20.9100000Z",
	        "callingimsi": "466920403025604",
	        "callingnum1": "678948008",
	        "callingnum2": "567834760",
	        "switch1": "China",
	        "switch2": "Germany"
	    },
	    {
	        "time": "2015-04-29T07:13:21.0220000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789037573",
	        "switch1": "US",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:21.4370000Z",
	        "callingimsi": "466923101048691",
	        "callingnum1": "678901578",
	        "callingnum2": "345626404",
	        "switch1": "Germany",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "789037573",
	        "callingnum2": "789044691",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789044691",
	        "switch1": "US",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:24.2120000Z",
	        "callingimsi": "466922201102759",
	        "callingnum1": "345698602",
	        "callingnum2": "789097900",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:25.6320000Z",
	        "callingimsi": "466923300236137",
	        "callingnum1": "567850552",
	        "callingnum2": "789086133",
	        "switch1": "China",
	        "switch2": "Germany"
	    }
	]

### Esempio JsonFormat

Se si dispone di un file JSON con il contenuto seguente:

	{
		"Id": 1,
		"Name": {
			"First": "John",
			"Last": "Doe"
		},
		"Tags": ["Data Factory”, "Azure"]
	}

e si desidera copiarlo in una tabella SQL di Azure nel formato seguente:

ID | Name.First | Name.Middle | Name.Last | Tag
--- | ---------- | ----------- | --------- | ----
1 | John | Null | Doe | ["Data Factory”, "Azure"]

Il set di dati di input con il tipo JsonFormat è definito come segue: (definizione parziale che include solo le parti pertinenti)

	"properties": {
		"structure": [
			{"name": "Id", "type": "Int"},
			{"name": "Name.First", "type": "String"},
			{"name": "Name.Middle", "type": "String"},
			{"name": "Name.Last", "type": "String"},
			{"name": "Tags", "type": "string"}
		],
		"typeProperties":
		{
			"folderPath": "mycontainer/myfolder",
			"format":
			{
				"type": "JsonFormat",
				"filePattern": "setOfObjects",
				"encodingName": "UTF-8",
				"nestingSeparator": "."
			}
		}
	}

Se la struttura non è definita, l’attività di copia rende flat la struttura per impostazione predefinita e copia ogni elemento.

#### Struttura JSON supportata
Tenere presente quanto segue:

- Ogni oggetto con una raccolta di coppie nome/valore verrà mappato a una riga di dati in un formato tabulare. Gli oggetti possono essere nidificati ed è possibile impostare il modo predefinito di rendere flat la struttura di un set di dati con un separatore di annidamento (.). Per un esempio, vedere la sezione precedente [Esempio JsonFormat](#jsonformat-example).  
- Se la struttura non è definita nel set di dati della data factory, l'attività di copia rileva lo schema dal primo oggetto e rende flat l'intero oggetto. 
- Se l'input JSON presenta una matrice, l'attività di copia converte l’intero valore della matrice in una stringa. È possibile saltare il passaggio [mappando o filtrando le colonne](#column-mapping-with-translator-rules).
- Se ci sono nomi duplicati allo stesso livello, l'attività di copia sceglierà quello più recente.
- I nomi delle proprietà distinguono tra maiuscole e minuscole. Due proprietà con lo stesso nome ma con una combinazione differente di maiuscole e minuscole verranno considerate come due proprietà diverse. 

