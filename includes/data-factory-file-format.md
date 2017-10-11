## <a name="specifying-formats"></a>Specifica dei formati
Azure Data Factory supporta i tipi di formato seguenti:

* [Formato testo](#specifying-textformat)
* [Formato JSON](#specifying-jsonformat)
* [Formato Avro](#specifying-avroformat)
* [Formato ORC](#specifying-orcformat)
* [Formato Parquet](#specifying-parquetformat)

### <a name="specifying-textformat"></a>Specifica di TextFormat
Per analizzare i file di testo o scrivere i dati in formato testo, impostare la proprietà `format` `type` su **TextFormat**. È anche possibile specificare le proprietà **facoltative** seguenti nella sezione `format`. Vedere la sezione [Esempio di TextFormat](#textformat-example) sulla configurazione.

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| columnDelimiter |Il carattere usato per separare le colonne in un file. È possibile prendere in considerazione la possibilità di usare un carattere raro non stampabile che probabilmente non esiste nei dati: ad esempio, specificare "\u0001" che rappresenta Start of Heading (SOH). |È consentito un solo carattere. Il valore **predefinito** è la **virgola (",")**. <br/><br/>Per usare un carattere Unicode, vedere i [caratteri Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) per ottenere il codice corrispondente. |No |
| rowDelimiter |Il carattere usato per separare le righe in un file. |È consentito un solo carattere. Sono consentiti i seguenti valori **predefiniti** in lettura: **["\r\n", "\r", "\n"]** e **"\r\n"** in scrittura. |No |
| escapeChar |Carattere speciale usato per eseguire l'escape di un delimitatore di colonna nel contenuto del file di input. <br/><br/>Per una tabella, è possibile specificare sia escapeChar che quoteChar. |È consentito un solo carattere. Nessun valore predefinito. <br/><br/>Ad esempio, se è presente una virgola (",") come delimitatore di colonna, ma si desidera inserire un carattere virgola nel testo (ad esempio: "Hello, world"), è possibile definire "$" come carattere di escape e usare la stringa "Hello$, world" nell'origine. |No |
| quoteChar |Carattere usato per delimitare tra virgolette un valore stringa. I delimitatori di colonne e righe tra virgolette sono considerati parte del valore stringa. Questa proprietà è applicabile sia ai set di dati di input che a quelli di output.<br/><br/>Per una tabella, è possibile specificare sia escapeChar che quoteChar. |È consentito un solo carattere. Nessun valore predefinito. <br/><br/>Ad esempio, se è presente una virgola (",") come delimitatore di colonna, ma si desidera inserire un carattere virgola nel testo (ad esempio: <Hello, world>), è possibile definire " (virgolette doppie) come carattere di virgolette e usare la stringa "Hello, world" nell'origine. |No |
| nullValue |Uno o più caratteri usati per rappresentare un valore null. |Uno o più caratteri. I valori **predefiniti** sono **"\N" e "NULL"** in lettura e **"\N"** in scrittura. |No |
| encodingName |Specificare il nome della codifica. |Un nome di codifica valido. Vedere [Proprietà Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Esempio: windows-1250 o shift_jis. Il valore **predefinito** è **UTF-8**. |No |
| firstRowAsHeader |Specifica se considerare la prima riga come intestazione. In un set di dati di input Data factory legge la prima riga come intestazione. In un set di dati di output Data factory scrive la prima riga come intestazione. <br/><br/>Vedere [Scenari per l'uso di `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) per gli scenari di esempio. |True<br/>**False (impostazione predefinita)** |No |
| skipLineCount |Indica il numero di righe da ignorare durante la lettura di dati da file di input. Se sono specificati sia skipLineCount che firstRowAsHeader, le righe vengono ignorate e le informazioni di intestazione vengono lette dal file di input. <br/><br/>Vedere [Scenari per l'uso di `firstRowAsHeader` e `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount) per gli scenari di esempio. |Integer |No |
| treatEmptyAsNull |Specifica se considerare una stringa vuota o null come valore null durante la lettura di dati da un file di input. |**True (impostazione predefinita)**<br/>False |No |

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

Per usare `escapeChar` invece di `quoteChar`, sostituire la riga con `quoteChar` con l'elemento escapeChar seguente:

```json
"escapeChar": "$",
```

#### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scenari di utilizzo di firstRowAsHeader e skipLineCount
* Si desidera copiare da un'origine non basata su file in un file di testo e aggiungere una riga di intestazione contenente i metadati dello schema (ad esempio: schema SQL). Per questo scenario specificare `firstRowAsHeader` come true nel set di dati di output.
* Si desidera copiare da un file di testo contenente una riga di intestazione a un sink non basato su file ed eliminare tale riga. Specificare `firstRowAsHeader` come true nel set di dati di input.
* Si desidera copiare da un file di testo e ignorare alcune righe all'inizio che non contengono né dati né un'intestazione. Specificare `skipLineCount` per indicare il numero di righe da ignorare. Se il resto del file contiene una riga di intestazione, è anche possibile specificare `firstRowAsHeader`. Se sono specificati sia `skipLineCount` che `firstRowAsHeader`, le righe vengono ignorate e le informazioni di intestazione vengono lette dal file di input.

### <a name="specifying-jsonformat"></a>Impostazione di JsonFormat
Per **importare/esportare i file JSON senza modifiche in/da Azure Cosmos DB**, vedere la sezione [Importare/esportare documenti JSON](../articles/data-factory/v1/data-factory-azure-documentdb-connector.md#importexport-json-documents) nell'articolo sul connettore di Azure Cosmos DB per ottenere informazioni dettagliate.

Per analizzare i file JSON o scrivere i dati in formato JSON, impostare la proprietà `format` `type` su **JsonFormat**. È anche possibile specificare le proprietà **facoltative** seguenti nella sezione `format`. Vedere la sezione [Esempio JsonFormat](#jsonformat-example) sulla configurazione.

| Proprietà | Descrizione | Obbligatoria |
| --- | --- | --- |
| filePattern |Indicare il modello dei dati archiviati in ogni file JSON. I valori consentiti sono: **setOfObjects** e **arrayOfObjects**. Il valore **predefinito** è **setOfObjects**. Vedere la sezione [Modelli di file JSON](#json-file-patterns) per i dettagli su questi modelli. |No |
| jsonNodeReference | Per eseguire l'iterazione dei dati ed estrarli dagli oggetti presenti nel campo di una matrice con lo stesso modello, specificare il percorso JSON di tale matrice. Questa proprietà è supportata solo quando si copiano i dati dai file JSON. | No |
| jsonPathDefinition | Specificare l'espressione del percorso JSON per ogni mapping colonne con un nome di colonna personalizzato. Iniziare con una lettera minuscola. Questa proprietà è supportata solo quando si copiano i dati dai file JSON ed è possibile estrarre i dati dall'oggetto o dalla matrice. <br/><br/> Per i campi sotto l'oggetto radice, iniziare con la radice $. Per i campi nella matrice scelta dalla proprietà `jsonNodeReference`, iniziare dall'elemento matrice. Vedere la sezione [Esempio JsonFormat](#jsonformat-example) sulla configurazione. | No |
| encodingName |Specificare il nome della codifica. Per l'elenco di nomi di codifica validi, vedere: Proprietà [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) . Ad esempio: windows-1250 o shift_jis. Il valore **predefinito** è **UTF-8**. |No |
| nestingSeparator |Carattere utilizzato per separare i livelli di nidificazione. Il valore predefinito è "." (punto). |No |

#### <a name="json-file-patterns"></a>Modelli di file JSON

L'attività di copia può eseguire l'analisi al di sotto dei modelli di file JSON:

- **Tipo I: setOfObjects**

    Ogni file contiene un solo oggetto o più oggetti con delimitatori di riga/concatenati. Quando si sceglie questa opzione in un set di dati di output, l'attività di copia produce un singolo file JSON con un oggetto per riga (delimitato da riga).

    * **Esempio di JSON a oggetto singolo**

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

    * **Esempio di JSON con delimitatori di riga**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Esempio di JSON concatenati**

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

- **Tipo II: arrayOfObjects**

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
        }
    ]
    ```

#### <a name="jsonformat-example"></a>Esempio JsonFormat

**Caso 1: Copia di dati dai file JSON**

Vedere sotto due tipi di esempi di quando si copiano dati dai file JSON e i punti generici da notare:

**Esempio 1: Estrarre i dati dall'oggetto e dalla matrice**

In questo esempio si prevede che un oggetto JSON radice esegua il mapping a un singolo record in un risultato tabulare. Se si dispone di un file JSON con il contenuto seguente:  

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagmentProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```
e lo si vuole copiare in una tabella SQL di Azure nel formato seguente, estraendo i dati sia dagli oggetti che dalla matrice:

| id | deviceType | targetResourceType | resourceManagmentProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Il set di dati di input con il tipo **JsonFormat** è definito come segue (definizione parziale che include solo le parti pertinenti). Più in particolare:

- La sezione `structure` definisce i nomi di colonna personalizzati e il tipo di dati corrispondente durante la conversione in dati tabulari. Questa sezione è **facoltativa** a meno che non sia necessario eseguire il mapping colonne. Vedere la sezione [Definizione della struttura per i set di dati rettangolari](#specifying-structure-definition-for-rectangular-datasets) per altri dettagli.
- `jsonPathDefinition` specifica il percorso JSON per ogni colonna indicante da dove estrarre i dati. Per copiare i dati dalla matrice, è possibile usare **array[x].property** per estrarre il valore della proprietà specificata dall'oggetto xth oppure è possibile usare **array[*].property** per trovare il valore in qualsiasi oggetto contenente tale proprietà.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagmentProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagmentProcessRunId": "$.context.custom.dimensions[1].ResourceManagmentProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}      
        }
    }
}
```

**Esempio 2: applicazione incrociata di più oggetti con lo stesso modello dalla matrice**

In questo esempio si prevede di trasformare un oggetto JSON radice in più record in risultato tabulare. Se si dispone di un file JSON con il contenuto seguente:  

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```
e lo si vuole copiare in una tabella SQL di Azure nel formato seguente, rendendo flat i dati nella matrice e nel crossjoin con le informazioni radice comuni:

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

Il set di dati di input con il tipo **JsonFormat** è definito come segue (definizione parziale che include solo le parti pertinenti). Più in particolare:

- La sezione `structure` definisce i nomi di colonna personalizzati e il tipo di dati corrispondente durante la conversione in dati tabulari. Questa sezione è **facoltativa** a meno che non sia necessario eseguire il mapping colonne. Vedere la sezione [Definizione della struttura per i set di dati rettangolari](#specifying-structure-definition-for-rectangular-datasets) per altri dettagli.
- `jsonNodeReference` indica di seguire l'iterazione dei dati e di estrarli dagli oggetti con lo stesso modello sotto le righe ordine della **matrice**.
- `jsonPathDefinition` specifica il percorso JSON per ogni colonna indicante da dove estrarre i dati. In questo esempio "ordernumber", "orderdate" e "city" sono sotto l'oggetto radice con il percorso JSON che inizia con "$.", mentre "order_pd" e "order_price" sono definiti con il percorso derivato dall'elemento matrice senza "$.".

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}         
        }
    }
}
```

**Tenere presente quanto segue:**

* Se `structure` e `jsonPathDefinition` non sono definite nel set di dati della data factory, l'attività di copia rileva lo schema dal primo oggetto e rende flat l'intero oggetto.
* Se l'input JSON presenta una matrice, per impostazione predefinita, l'attività di copia converte l'intero valore della matrice in una stringa. È possibile scegliere di estrarre i dati usando `jsonNodeReference` e/o `jsonPathDefinition` oppure di ignorarlo non specificandolo in `jsonPathDefinition`.
* Se ci sono nomi duplicati allo stesso livello, l'attività di copia sceglie quello più recente.
* I nomi delle proprietà distinguono tra maiuscole e minuscole. Due proprietà con lo stesso nome ma con una combinazione differente di maiuscole e minuscole vengono considerate come due proprietà diverse.

**Caso 2: Scrittura dei dati nel file JSON**

Se nel database SQL è presente la tabella seguente:

| id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

e per ogni record di prevede di scrivere in un oggetto JSON nel formato seguente:
```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Il set di dati di output con il tipo **JsonFormat** è definito come segue (definizione parziale che include solo le parti pertinenti). Più in particolare, la sezione `structure` definisce i nomi di proprietà personalizzati nel file di destinazione e verrà usato `nestingSeparator` (il valore predefinito è ".") per identificare il livello di annidamento dal nome. Questa sezione è **facoltativa** a meno che non si voglia modificare il nome della proprietà confrontandolo con il nome della colonna di origine o annidare alcune delle proprietà.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

### <a name="specifying-avroformat"></a>Specifica di AvroFormat
Per analizzare i file Avro o scrivere i dati in formato Avro, impostare la proprietà `format` `type` su **AvroFormat**. Non è necessario specificare le proprietà nella sezione Format all'interno della sezione typeProperties. Esempio:

```json
"format":
{
    "type": "AvroFormat",
}
```

Per usare il formato Avro in una tabella Hive, fare riferimento all' [esercitazione su Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Tenere presente quanto segue:  

* I [tipi di dati complessi](http://avro.apache.org/docs/current/spec.html#schema_complex) non sono supportati (record, enumerazioni, matrici, mappe, unioni e dati fissi).

### <a name="specifying-orcformat"></a>Impostazione di OrcFormat
Per analizzare i file ORC o scrivere i dati in formato ORC, impostare la proprietà `format` `type` su **OrcFormat**. Non è necessario specificare le proprietà nella sezione Format all'interno della sezione typeProperties. Esempio:

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
Per analizzare i file Parquet o scrivere i dati in formato Parquet, impostare la proprietà `format` `type` su **ParquetFormat**. Non è necessario specificare le proprietà nella sezione Format all'interno della sezione typeProperties. Esempio:

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
