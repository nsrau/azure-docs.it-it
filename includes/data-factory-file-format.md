## <a name="specifying-formats"></a>Specifica dei formati
Azure Data Factory supporta i tipi di formato seguenti: 

* [Formato testo](#specifying-textformat)
* [Formato JSON](#specifying-jsonformat)
* [Formato Avro](#specifying-avroformat)
* [Formato ORC](#specifying-orcformat)
* [Formato Parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Specifica di TextFormat
Se il formato è impostato su **TextFormat**, è possibile specificare le proprietà **facoltative** seguenti nella sezione **Format**.

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| columnDelimiter |Il carattere usato per separare le colonne in un file. |È consentito un solo carattere. Il valore predefinito è la virgola (","). <br/><br/>Per usare un carattere Unicode, vedere i [caratteri Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) per ottenere il codice corrispondente. Ad esempio, è possibile prendere in considerazione la possibilità di usare un carattere raro non stampabile che probabilmente non esiste nei dati: specificare "\u0001" che rappresenta Start of Heading (SOH). |No |
| rowDelimiter |Il carattere usato per separare le righe in un file. |È consentito un solo carattere. Sono consentiti i seguenti valori predefiniti in lettura: ["\r\n", "\r", "\n"] e "\r\n" in scrittura. |No |
| escapeChar |Carattere speciale usato per eseguire l'escape di un delimitatore di colonna nel contenuto del file di input. <br/><br/>Per una tabella, è possibile specificare sia escapeChar che quoteChar. |È consentito un solo carattere. Nessun valore predefinito. <br/><br/>Ad esempio, se è presente una virgola (",") come delimitatore di colonna, ma si desidera inserire un carattere virgola nel testo (ad esempio: "Hello, world"), è possibile definire "$" come carattere di escape e usare la stringa "Hello$, world" nell'origine. |No |
| quoteChar |Carattere usato per delimitare tra virgolette un valore stringa. I delimitatori di colonne e righe tra virgolette sono considerati parte del valore stringa. Questa proprietà è applicabile sia ai set di dati di input che a quelli di output.<br/><br/>Per una tabella, è possibile specificare sia escapeChar che quoteChar. |È consentito un solo carattere. Nessun valore predefinito. <br/><br/>Ad esempio, se è presente una virgola (",") come delimitatore di colonna, ma si desidera inserire un carattere virgola nel testo (ad esempio: <Hello, world>), è possibile definire " (virgolette doppie) come carattere di virgolette e usare la stringa "Hello, world" nell'origine. |No |
| nullValue |Uno o più caratteri usati per rappresentare un valore null. |Uno o più caratteri. I valori predefiniti sono "\N" e "NULL" in lettura e "\N" in scrittura. |No |
| encodingName |Specificare il nome della codifica. |Un nome di codifica valido. Vedere [Proprietà Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Esempio: windows-1250 o shift_jis. Il valore predefinito è UTF-8. |No |
| firstRowAsHeader |Specifica se considerare la prima riga come intestazione. In un set di dati di input Data factory legge la prima riga come intestazione. In un set di dati di output Data factory scrive la prima riga come intestazione. <br/><br/>Vedere gli esempi descritti in [Scenari di utilizzo di **firstRowAsHeader** e **skipLineCount**](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/>False (impostazione predefinita) |No |
| skipLineCount |Indica il numero di righe da ignorare durante la lettura di dati da file di input. Se sono specificati sia skipLineCount che firstRowAsHeader, le righe vengono ignorate e le informazioni di intestazione vengono lette dal file di input. <br/><br/>Vedere gli esempi descritti in [Scenari di utilizzo di firstRowAsHeader e skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) . |Integer |No |
| treatEmptyAsNull |Specifica se considerare una stringa vuota o null come valore null durante la lettura di dati da un file di input. |True (impostazione predefinita)<br/>False |No |

#### <a name="textformat-example"></a>Esempio di TextFormat
L'esempio seguente illustra alcune delle proprietà del formato per TextFormat.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Per usare un escapeChar anziché un quoteChar, sostituire la riga con quoteChar con il seguente escapeChar:

```json
"escapeChar": "$",
```


### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenari di utilizzo di firstRowAsHeader e skipLineCount
* Si desidera copiare da un'origine non basata su file in un file di testo e aggiungere una riga di intestazione contenente i metadati dello schema (ad esempio: schema SQL). Per questo scenario specificare **firstRowAsHeader** come true nel set di dati di output. 
* Si desidera copiare da un file di testo contenente una riga di intestazione a un sink non basato su file ed eliminare tale riga. Specificare **firstRowAsHeader** come true nel set di dati di input.
* Si desidera copiare da un file di testo e ignorare alcune righe all'inizio che non contengono né dati né un'intestazione. Specificare **skipLineCount** per indicare il numero di righe da ignorare. Se il resto del file contiene una riga di intestazione, è inoltre possibile specificare **firstRowAsHeader**. Se sono specificati sia **skipLineCount** che **firstRowAsHeader**, le righe vengono ignorate e le informazioni di intestazione vengono lette dal file di input.

### <a name="specifying-avroformat"></a>Specifica di AvroFormat
Se il formato è impostato su AvroFormat, non è necessario specificare proprietà nella sezione Format all'interno della sezione typeProperties. Esempio:

```json
"format":
{
    "type": "AvroFormat",
}
```

Per usare il formato Avro in una tabella Hive, fare riferimento all' [esercitazione su Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenere presente quanto segue:  

* I [tipi di dati complessi](http://avro.apache.org/docs/current/spec.html#schema_complex) non sono supportati (record, enumerazioni, matrici, mappe, unioni e fissi)

### <a name="specifying-jsonformat"></a>Impostazione di JsonFormat
Per importare/esportare i file JSON senza modifiche in/da DocumentDB, vedere la sezione [Importare/Esportare documenti JSON](../articles/data-factory/data-factory-azure-documentdb-connector.md#importexport-json-documents) nell'articolo sul connettore DocumentDB per ottenere informazioni dettagliate.

Se il formato è impostato su **JsonFormat**, è possibile specificare le proprietà **facoltative** seguenti nella sezione **Format**.

| Proprietà | Descrizione | Obbligatoria |
| --- | --- | --- |
| filePattern |Indicare il modello dei dati archiviati in ogni file JSON. I valori consentiti sono: **setOfObjects** e **arrayOfObjects**. Il valore **predefinito** è: **setOfObjects**. Per dettagli su questi modelli, vedere le prossime sezioni. |No |
| encodingName |Specificare il nome della codifica. Per l'elenco di nomi di codifica validi, vedere: Proprietà [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) . Ad esempio: windows-1250 o shift_jis. Il valore **predefinito** è **UTF-8**. |No |
| nestingSeparator |Carattere utilizzato per separare i livelli di nidificazione. Il valore predefinito è "." (punto). |No |

#### <a name="setofobjects-file-pattern"></a>Modello di file setOfObjects
Ogni file contiene un solo oggetto o più oggetti con delimitatori di riga/concatenati. Quando si sceglie questa opzione in un set di dati di output, l'attività di copia produce un singolo file JSON con un oggetto per riga (delimitato da riga).

**singolo oggetto** 

```json
{
    "time": "2015-04-29T07:12:20.9100000Z",
    "callingimsi": "466920403025604",
    "callingnum1": "678948008",
    "callingnum2": "567834760",
    "switch1": "China",
    "switch2": "Germany"
}
```

**JSON con delimitatori di riga** 

```json
    {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
    {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
    {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
    {"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}
```

**JSON concatenati**

```json
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
```

#### <a name="arrayofobjects-file-pattern"></a>Modello di file arrayOfObjects.
Ogni file contiene una matrice di oggetti. 

```json
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
```
### <a name="jsonformat-example"></a>Esempio JsonFormat
Se si dispone di un file JSON con il contenuto seguente:  

```json
{
    "Id": 1,
    "Name": {
        "First": "John",
        "Last": "Doe"
    },
    "Tags": ["Data Factory”, "Azure"]
}
```
e si desidera copiarlo in una tabella SQL di Azure nel formato seguente: 

| ID | Name.First | Name.Middle | Name.Last | Tag |
| --- | --- | --- | --- | --- |
| 1 |John |Null |Doe |["Data Factory”, "Azure"] |

Il set di dati di input con il tipo JsonFormat è definito come segue: (definizione parziale che include solo le parti pertinenti)

```json
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
```
Se la struttura non è definita, l’attività di copia rende flat la struttura per impostazione predefinita e copia ogni elemento. 

#### <a name="supported-json-structure"></a>Struttura JSON supportata
Tenere presente quanto segue: 

* Ogni oggetto con una raccolta di coppie nome/valore viene mappato a una riga di dati in un formato tabulare. Gli oggetti possono essere nidificati ed è possibile impostare il modo predefinito di rendere flat la struttura di un set di dati con un separatore di annidamento (.). Per un esempio, vedere la sezione [Esempio JsonFormat](#jsonformat-example)precedente.  
* Se la struttura non è definita nel set di dati della data factory, l'attività di copia rileva lo schema dal primo oggetto e rende flat l'intero oggetto. 
* Se l'input JSON presenta una matrice, l'attività di copia converte l’intero valore della matrice in una stringa. È possibile saltare il passaggio [mappando o filtrando le colonne](#column-mapping-with-translator-rules).
* Se ci sono nomi duplicati allo stesso livello, l'attività di copia sceglie quello più recente.
* I nomi delle proprietà distinguono tra maiuscole e minuscole. Due proprietà con lo stesso nome ma con una combinazione differente di maiuscole e minuscole vengono considerate come due proprietà diverse. 

### <a name="specifying-orcformat"></a>Impostazione di OrcFormat
Se il formato è impostato su OrcFormat, non è necessario specificare le proprietà nella sezione Format all'interno della sezione typeProperties. Esempio:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Se non si esegue una copia **identica** dei file ORC tra l'archivio dati locale e quello nel cloud, nel computer gateway è necessario installare JRE 8 (Java Runtime Environment). Per un gateway a 64 bit è necessario JRE a 64 bit, mentre per un gateway a 32 bit è necessario JRE a 32 bit. Entrambe le versioni sono disponibili [qui](http://go.microsoft.com/fwlink/?LinkId=808605). Scegliere la versione appropriata.
> 
> 

Tenere presente quanto segue:

* Tipi di dati complessi non sono supportati (STRUCT, MAP, LIST, UNION)
* Il file ORC dispone di tre [opzioni relative alla compressione](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory supporta la lettura dei dati dal file ORC in uno di questi formati compressi. Per leggere i dati, Data Factoy usa la compressione codec dei metadati. Tuttavia, durante la scrittura in un file ORC, Data Factory sceglie ZLIB che è il valore predefinito per ORC. Al momento non esiste alcuna opzione per ignorare tale comportamento. 

### <a name="specifying-parquetformat"></a>Specificare ParquetFormat
Se il formato è impostato su ParquetFormat, non è necessario specificare le proprietà nella sezione Format all'interno della sezione typeProperties. Esempio:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Se non si esegue una copia **identica** dei file Parquet tra l'archivio dati locale e quello nel cloud, nel computer gateway è necessario installare JRE 8 (Java Runtime Environment). Per un gateway a 64 bit è necessario JRE a 64 bit, mentre per un gateway a 32 bit è necessario JRE a 32 bit. Entrambe le versioni sono disponibili [qui](http://go.microsoft.com/fwlink/?LinkId=808605). Scegliere la versione appropriata.
> 
> 

Tenere presente quanto segue:

* Tipi di dati complessi non sono supportati (MAP, LIST)
* Un file Parquet ha le seguenti opzioni relative alla compressione: NONE, SNAPPY, GZIP e LZO. Data Factory supporta la lettura dei dati dal file ORC in uno di questi formati compressi. Per la lettura dei dati usa il codec di compressione nei metadati. Tuttavia, durante la scrittura in un file Parquet, Data Factory sceglie SNAPPY, cioè il valore predefinito per il formato Parquet. Al momento non esiste alcuna opzione per ignorare tale comportamento. 



<!--HONumber=Nov16_HO3-->


