## <a name="fileshare-dataset-type-properties"></a>Proprietà del tipo di set di dati FileShare
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](../articles/data-factory/data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati. 

La sezione **typeProperties** è diversa per ogni tipo di set di dati. Fornisce informazioni specifiche del tipo di set di dati. La sezione typeProperties per un set di dati di tipo **FileShare** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Sottopercorso alla cartella. Usare il carattere di escape "\" per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) .<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio/fine delle sezioni. |Sì |
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato sarà nel formato seguente: <br/><br/>Data.<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy può essere usato per specificare un valore folderPath dinamico e un nome file per i dati di una serie temporale. Ad esempio, folderPath con parametri per ogni ora di dati. |No |
| Format |Sono supportati i tipi di formato seguenti: **TextFormat**, **AvroFormat**, **JsonFormat** e **OrcFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per informazioni dettagliate, vedere le sezioni [Specifica di TextFormat](#specifying-textformat), [Specifica di AvroFormat](#specifying-avroformat), [Specifica di JsonFormat](#specifying-jsonformat) e [Specifica di OrcFormat](#specifying-orcformat). Se si desidera copiare i file così come sono tra archivi basati su file (copia binaria), è possibile saltare la sezione formato sia nella definizione del set di dati di output che in quella di input. |No |
| fileFilter |Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file.<br/><br/>I valori consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempi 1: `"fileFilter": "*.log"`<br/>Esempio 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter è applicabile per un set di dati di input FileShare. Questa proprietà non è supportata con HDFS. |No |
| compressione |Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono: **GZip**, **Deflate** e **BZip2** e i livelli supportati sono: **Ottimale** e **Più veloce**. Le impostazioni di compressione non sono attualmente supportate per i dati **AvroFormat** oppure **OrcFormat**. Per maggiori informazioni, vedere la sezione [Supporto della compressione](#compression-support) . |No |
| useBinaryTransfer |Specificare se usare la modalità di trasferimento binario. True per la modalità binaria e false per ASCII. Valore predefinito: True. Questa proprietà può essere usata solo quando il tipo di servizio collegato associato è di tipo: FtpServer. |No |

> [!NOTE]
> filename e fileFilter non possono essere usati contemporaneamente.
> 
> 

### <a name="using-partionedby-property"></a>Uso della proprietà partionedBy
Come indicato nella sezione precedente, partitionedBy può essere usato per specificare un valore folderPath dinamico e un nome file per i dati di una serie temporale. È possibile eseguire questa operazione con le macro della data factory e le variabili di sistema SliceStart, SliceEnd, che indicano il periodo di tempo logico per una sezione di dati specificata. 

Per informazioni sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Creazione di set di dati](../articles/data-factory/data-factory-create-datasets.md), [Pianificazione ed esecuzione](../articles/data-factory/data-factory-scheduling-and-execution.md) e [Creazione di pipeline](../articles/data-factory/data-factory-create-pipelines.md). 

#### <a name="sample-1"></a>Esempio 1.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy": 
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH) specificato. SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. Esempio: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Esempio 2:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy": 
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
],
```
In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà folderPath e fileName.



<!--HONumber=Nov16_HO3-->


