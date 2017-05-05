---
title: Creare set di dati in Azure Data Factory | Microsoft Docs
description: "Informazioni su come creare set di dati in Azure Data Factory con esempi che usano proprietà quali anchorDateTime e offset."
keywords: creare set di dati, esempio di set di dati, esempio di offset
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: e0c999b2bf1dd38d8a0c99c6cdd4976cc896dd99
ms.openlocfilehash: 88e653f6e46f3e8eb72e620b495d1769f17bdfbf
ms.lasthandoff: 04/20/2017


---
# <a name="datasets-in-azure-data-factory"></a>Set di dati in Azure Data Factory
Questo articolo descrive i set di dati in Azure Data Factory e include esempi di database offset/style, anchorDateTime e offset.

> [!NOTE]
> Chi non conosce bene Azure Data Factory può consultare [Introduzione ad Azure Data Factory](data-factory-introduction.md) per una panoramica sul servizio. Se non si ha esperienza diretta nella creazione di data factory, l'[esercitazione sulla trasformazione dei dati](data-factory-build-your-first-pipeline.md) e/o [quella sullo spostamento dei dati](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) può essere utile per comprendere meglio questo articolo. 

## <a name="overview"></a>Panoramica
Una data factory può comprendere una o più pipeline. Una **pipeline** è un raggruppamento logico di **attività** che insieme eseguono un compito. Le attività in una pipeline definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività di copia per copiare i dati da un Server SQL locale a un'archiviazione BLOB di Azure. Quindi, usare un'attività Hive che esegue uno script Hive in un cluster HDInsight di Azure per elaborare o trasformare i dati dall'archivio BLOB per produrre dati di output. Infine, usare una seconda attività di copia per copiare i dati di output in un Azure SQL Data Warehouse in cui vengono compilate le soluzioni di report di business intelligence, BI. Per altre informazioni su pipeline e attività, vedere l'articolo [Pipeline e attività in Azure Data Factory](data-factory-create-pipelines.md).

Un'attività può non avere alcun **set di dati** di input o può averne più di uno e generare uno o più set di dati di output. Un set di dati di input rappresenta l'input per un'attività nella pipeline, un set di dati di output rappresenta l'output dell'attività. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Un set di dati BLOB di Azure, ad esempio, specifica il contenitore BLOB e la cartella nell'archiviazione BLOB di Azure da cui la pipeline dovrà leggere i dati. 

Prima di creare un set di dati, è necessario creare un **servizio collegato** per collegare l'archivio dati alla data factory. I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti di SQL. Il servizio collegato Archiviazione di Azure,ad esempio, collega l'account di archiviazione di Azure alla data factory. Un set di dati BLOB di Azure rappresenta il contenitore e la cartella BLOB che contengono i BLOB di input da elaborare. 

Ecco un esempio di scenario: per copiare i dati da un'Archiviazione BLOB di Azure a un Database SQL di Azure, si creano due servizi collegati: Archiviazione di Azure e Database SQL di Azure. Quindi, creare due set di dati: set di dati BLOB di Azure, che si riferisce al servizio collegato Archiviazione di Azure, set di dati delle tabelle di SQL Azure, che si riferisce al servizio collegato di Database SQL di Azure. I servizi collegati di Archiviazione di Azure e Database di SQL Azure contengono stringhe di connessione usate da Data Factory in fase di runtime per connettersi rispettivamente al Database SQL di Azure e all'Archiviazione di Azure. Il set di dati BLOB di Azure specifica il contenitore e una cartella BLOB che contengono i BLOB di input nell'Archiviazione BLOB di Azure. Il set di dati delle tabelle di SQL di Azure specifica la tabella SQL nel database SQL di Azure in cui verranno copiati i dati.

Nel diagramma seguente viene illustrata la relazione tra servizi collegati, attività, set di dati e pipeline in Data Factory: 

![Relazione tra pipeline, attività, set di dati, i servizi collegati](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Set di dati JSON
Un set di dati in Azure Data Factory viene definito nel formato JSON come segue:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

La tabella seguente descrive le proprietà nel codice JSON precedente:   

| Proprietà | Descrizione | Obbligatorio | Default |
| --- | --- | --- | --- |
| name |Nome del set di dati. Per le regole di denominazione, vedere [Azure Data Factory: regole di denominazione](data-factory-naming-rules.md) . |Sì |ND |
| type |Tipo del set di dati. Specificare uno dei tipi supportati da Azure Data Factory, ad esempio AzureBlob, AzureSqlTable. <br/><br/>Per informazioni dettagliate, vedere [Tipo di set di dati](#Type). |Sì |ND |
| structure |Schema del set di dati.<br/><br/>Per informazioni dettagliate, vedere [Struttura del set di dati](#Structure). |No. |ND |
| typeProperties | Le proprietà del tipo sono diverse per ogni tipo, ad esempio: BLOB di Azure, tabella SQL di Azure. Per informazioni dettagliate sui tipi supportati e sulle relative proprietà, vedere la sezione [Tipo di set di dati](#Type) . |Sì |ND |
| external | Flag booleano per specificare se un set di dati è generato o meno in modo esplicito da una pipeline della data factory. Se il set di dati di input per un'attività non viene generato dalla pipeline corrente, impostare questo flag su true. Impostare questo flag su true per il set di dati di input della prima attività nella pipeline.  |No |false |
| disponibilità | Definisce la finestra di elaborazione, oraria, giornaliera e così via, o il modello di sezionamento per la produzione di set di dati. Ogni unità di dati usata e prodotta da un'esecuzione di attività prende il nome di sezione di dati. Se la disponibilità di un set di dati di output è impostata su giornaliera, ad esempio frequenza: giorno, intervallo: 1, viene prodotta una sezione ogni giorno. <br/><br/>Per informazioni dettagliate, vedere [Disponibilità dei set di dati](#Availability). <br/><br/>Per informazioni dettagliate sul modello di sezionamento dei set di dati, vedere l'articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md). |Sì |ND |
| policy |Definisce i criteri o la condizione che devono soddisfare i sezionamenti di set di dati. <br/><br/>Per informazioni dettagliate, vedere la sezione [Criteri di set di dati](#Policy) . |No |ND |

## <a name="dataset-example"></a>Esempio di set di dati
Nell'esempio di seguito, il set di dati rappresenta una tabella chiamata **MyTable** in un **database SQL di Azure**.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Tenere presente quanto segue:

* type è impostato su AzureSqlTable.
* La proprietà del tipo tableName, specifica del tipo AzureSqlTable, è impostata su MyTable.
* linkedServiceName fa riferimento a un servizio collegato di tipo AzureSqlDatabase, che è definito nel frammento di codice JSON successivo. 
* In disponibilità frequenza è impostata su Giorno e intervallo è impostato su 1: ciò significa che la sezione del set di dati viene generata ogni giorno.  

AzureSqlLinkedService è definito come segue:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Nel codice JSON precedente:

* type è impostato su AzureSqlDatabase.
* La proprietà del tipo connectionString specifica le informazioni per connettersi a un database SQL di Azure.  

Come si può notare, il servizio collegato definisce come connettersi a un database SQL di Azure. Il set di dati definisce quale tabella viene usata come input/output per l'attività in una pipeline.   

> [!IMPORTANT]
> A meno che un set di dati non sia generato dalla pipeline, deve essere contrassegnato come **esterno**. Questa impostazione vale in genere per gli input della prima attività in una pipeline.   


## <a name="Type"></a> Tipo di set di dati
Il tipo del set di dati dipende dall'archivio dati usato. Vedere la tabella seguente per un elenco di archivi dati supportati da Data Factory. Fare clic su un archivio dati per informazioni su come creare un servizio collegato e un set di dati per tale archivio dati.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gli archivi dati contrassegnati da un asterisco (*) possono essere locali o in IaaS di Azure e richiederanno l'installazione del [Gateway di gestione dati](data-factory-data-management-gateway.md) in un computer IaaS locale o in Azure.

Nell'esempio della sezione precedente, il tipo di set di dati è impostato su **AzureSqlTable**. Analogamente, per un set di dati BLOB di Azure, il tipo di set di dati è impostato su **AzureBlob** come illustrato nel codice JSON seguente:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>Struttura del set di dati
La sezione **structure** è una sezione **opzionale** che definisce lo schema del set di dati. Contiene una raccolta di nomi e tipi di dati delle colonne. Usare la sezione sulla struttura per dare informazioni sul tipo usato per convertire tipi e mappare le colonne dall'origine alla destinazione. Nell'esempio seguente il set di dati ha tre colonne, ovvero `slicetimestamp`, `projectname` e `pageviews`, che sono rispettivamente di tipo String, String e Decimal.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Ogni colonna della struttura contiene le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome della colonna. |Sì |
| type |Tipo di dati della colonna.  |No |
| culture |Cultura basata su .NET da usare quando il tipo è un tipo .NET: `Datetime` o `Datetimeoffset`. Il valore predefinito è `en-us`. |No |
| format |Stringa di formato da usare quando il tipo è un tipo .NET: `Datetime` o `Datetimeoffset`. |No |

Attenersi alle linee guida seguenti per decidere il momento in cui includere informazioni su "structure" e gli elementi da inserire nella sezione **structure** .

* **Per le origini dati strutturate** in cui, oltre a informazioni sullo schema e sul tipo di dati, sono archiviati i dati stessi, ovvero origini come tabelle di SQL Server, Oracle e Azure, è necessario specificare la sezione "struttura" solo se si vuole eseguire il mapping delle colonne di origine alle colonne del sink e i relativi nomi non sono uguali. 
  
    Poiché per le origini dati strutturate le informazioni sul tipo sono già disponibili, non si deve includere le informazioni sul tipo quando si include la sezione "structure".
* **Per lo schema delle origini dati di lettura, in particolare BLOB di Azure**, è possibile scegliere di archiviare i dati senza aggiungere alcuna informazione sullo schema o sul tipo. Per questi tipi di origini dati, includere "structure" quando si desidera eseguire il mapping delle colonne di origine alle colonne del sink (o) quando il set di dati è un set di dati di input per un'attività di copia e i tipi di dati del set di dati di origine devono essere convertiti nei tipi nativi per il sink. 
    
    Data Factory supporta i valori di tipo basati su .NET conformi a CLS per specificare informazioni sul tipo nella sezione "structure" in relazione allo schema su origini dati di lettura come BLOB di Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan.

La data factory esegue automaticamente le conversioni quando si spostano dati da un archivio dati di origine a un archivio dati di sink. 
  

## <a name="Availability"></a> Disponibilità dei set di dati
La sezione **disponibilità** in un set di dati definisce l'intervallo di elaborazione, vale a dire oraria, giornaliera, settimanale e così via, per il set di dati. Per altre informazioni sugli intervalli delle attività, vedere l'articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md).

La sezione availability seguente specifica che il set di dati di output viene generato ogni ora oppure che il set di dati di input è disponibile ogni ora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Se la pipeline dispone dei seguenti orari di inizio e fine:  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Viene prodotto il set di dati di output ogni ora entro gli orari di inizio e fine della pipeline. Pertanto, cinque sezioni di set di dati generate da questa pipeline, uno per ogni intervallo di attività (00:00 - 01:00, 01: 00 - 02:00, 02:00 - 03:00, 03:00 - 04:00, 04:00 - 05:00). 

La tabella seguente descrive le proprietà che è possibile usare nella sezione availability:

| Proprietà | Descrizione | Obbligatorio | Default |
| --- | --- | --- | --- |
| frequency |Specifica l'unità di tempo per la produzione di sezioni di set di dati.<br/><br/><b>Frequenza supportata</b>: minuto, ora, giorno, settimana, mese |Sì |ND |
| interval |Specifica un moltiplicatore per la frequenza.<br/><br/>"Intervallo di frequenza x" determina la frequenza con cui viene generata la sezione.<br/><br/>Se è necessario suddividere il set di dati su base oraria, impostare l'opzione <b>Frequenza</b> su <b>Ora</b> e <b>Intervallo</b> su <b>1</b>.<br/><br/><b>Nota</b> : se si specifica frequency come Minute, è consigliabile impostare interval su un valore non inferiore a 15 |Sì |ND |
| style |Specifica se la sezione deve essere generata all'inizio o alla fine dell'intervallo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se frequency è impostato su Month e style è impostato su EndOfInterval, la sezione viene generata l'ultimo giorno del mese. Se style è impostato su StartOfInterval, la sezione viene generata il primo giorno del mese.<br/><br/>Se l'opzione Frequnza è impostata su Mese e l'opzione Stile è impostata su EndOfInterval, la sezione viene generata l'ultima ora del giorno.<br/><br/>Se frequency è impostato su Hour e style è impostato su EndOfInterval, la sezione viene generata alla fine dell'ora. Ad esempio, una sezione per il periodo 13.00 - 14.00 viene generata alle 14.00. |No |EndOfInterval |
| anchorDateTime |Definisce la posizione assoluta nel tempo usata dall'utilità di pianificazione per calcolare i limiti della sezione del set di dati. <br/><br/><b>Nota:</b> se AnchorDateTime include parti della data più granulari rispetto alla frequenza, le parti più granulari vengono ignorate. <br/><br/>Ad esempio, se l'<b>intervallo</b> è <b>orario</b> (ovvero frequenza: ora e intervallo: 1) e <b>AnchorDateTime</b> contiene <b>minuti e secondi</b>, le parti <b>minuti e secondi</b> di AnchorDateTime vengono ignorate. |No |01/01/0001 |
| offset |Intervallo di tempo in base al quale l'inizio e la fine di tutte le sezioni dei set di dati vengono spostate. <br/><br/><b>Nota:</b> se vengono specificati sia anchorDateTime che offset, il risultato sarà lo spostamento combinato. |No |ND |

### <a name="offset-example"></a>Esempio di offset
Per impostazione predefinita, le sezioni giornaliere (`"frequency": "Day", "interval": 1`) iniziano alle 00:00, mezzanotte, ora UTC. Se, invece, si desidera impostare l'ora di inizio alle 06:00 UTC, impostare l'offset come illustrato nel frammento riportato di seguito: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Esempio di anchorDateTime
Nell'esempio seguente, il set di dati viene prodotto ogni 23 ore. La prima sezione inizia all'ora specificata da anchorDateTime, che è impostato su `2017-04-19T08:00:00` (ora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Esempio di offset/style
Il seguente set di dati è un set di dati mensile e viene generato il 3 di ogni mese alle ore 08:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00",    
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Criteri di set di dati
La sezione **policy** nella definizione del set di dati stabilisce i criteri o la condizione che le sezioni del set di dati devono soddisfare.

### <a name="validation-policies"></a>Criteri di convalida
| Nome criterio | Descrizione | Applicato a | Obbligatorio | Default |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Verifica che i dati in un **BLOB di Azure** soddisfino i requisiti relativi alle dimensioni minime (in megabyte). |BLOB di Azure |No |ND |
| minimumRows |Verifica che i dati in un **database SQL di Azure** o in una **tabella di Azure** contengano il numero minimo di righe. |<ul><li>Database SQL di Azure</li><li>tabella di Azure</li></ul> |No |ND |

#### <a name="examples"></a>esempi
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Set di dati esterni
I set di dati esterni sono quelli non prodotti da una pipeline in esecuzione nella data factory. Se il set di dati è contrassegnato come **external**, è possibile definire i criteri di **ExternalData** in modo da influenzare il comportamento della disponibilità di sezioni dei set di dati.

A meno che un set di dati non sia generato da Azure Data Factory, deve essere contrassegnato come **external**. Questa impostazione si applica in genere agli input della prima attività in una pipeline, a meno che non si usi il concatenamento di attività o di pipeline.

| name | Descrizione | Obbligatorio | Default Value |
| --- | --- | --- | --- |
| dataDelay |Tempo di attesa per il controllo sulla disponibilità dei dati esterni per il sezionamento specificato. Ad esempio, se i dati sono disponibili ogni ora, il controllo per vedere se i dati esterni sono disponibili e la sezione corrispondente è Ready può essere ritardato usando dataDelay.<br/><br/>Si applica solo all'ora corrente.  Ad esempio, se in questo momento sono le 13:00 e questo valore è di 10 minuti, la convalida inizia alle 13:10.<br/><br/>Questa impostazione non influisce sulle sezioni nel passato; le sezioni con i parametri Ora di fine sezione + dataDelay < Ora vengono elaborate senza alcun ritardo.<br/><br/>I valori orari superiori a 23:59 ore devono essere specificati nel formato `day.hours:minutes:seconds`. Per specificare 24 ore, ad esempio, non utilizzare 24:00:00; utilizzare invece 1.00:00:00. Il valore 24:00:00 viene considerato 24 giorni (24.00:00:00). Per 1 giorno e 4 ore, specificare 1:04:00:00. |No |0 |
| retryInterval |Il tempo di attesa tra un errore e il successivo tentativo. Si applica all'ora corrente; se il precedente tentativo non è riuscito, il tentativo successivo si avvia dopo il periodo di retryInterval. <br/><br/>Se in questo momento sono le 13:00, viene avviato il primo tentativo. Se la durata per completare il primo controllo di convalida è 1 minuto e l'operazione non è riuscita, il tentativo successivo è alle 13:00 + 1 min (durata) + 1 min (intervallo tentativi) = 13:02. <br/><br/>Per le sezioni passate, non si verifica alcun ritardo. La ripetizione avviene immediatamente. |No |00:01:00 (1 minute) |
| retryTimeout |Timeout per ogni nuovo tentativo.<br/><br/>Se questa proprietà è impostata su 10 minuti, la convalida deve essere completata entro 10 minuti. Se sono necessari più di 10 minuti per eseguire la convalida, il tentativo viene sospeso.<br/><br/>Se tutti i tentativi per la convalida scadono, la sezione viene contrassegnata come TimedOut. |No |00:10:00 (10 minutes) |
| maximumRetry |Numero di volte per controllare la disponibilità dei dati esterni. Il valore massimo consentito è 10. |No |3 |


## <a name="create-datasets"></a>Creare set di dati
È possibile creare set di dati usando uno di questi strumenti o SDK. 

- Copia guidata. 
- Portale di Azure
- Visual Studio
- Azure PowerShell
- Modello di Azure Resource Manager
- API REST
- API .NET

Vedere le esercitazioni seguenti per istruzioni dettagliate sulla creazione di pipeline e set di dati tramite uno di questi strumenti o SDK.
 
- [Creare una pipeline con un'attività di trasformazione dati](data-factory-build-your-first-pipeline.md)
- [Creare una pipeline con un'attività di spostamento dati](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Dopo aver creato o distribuito una pipeline, è possibile gestire e monitorare le pipeline usando i pannelli del portale di Azure o con un'app di gestione e monitoraggio. Vedere l'argomento successivo per le istruzioni dettagliate. 

- [Monitorare e gestire le pipeline con i pannelli del portale di Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorare e gestire le pipeline con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Set di dati con ambito
È possibile creare set di dati con ambito limitato a una pipeline usando la proprietà **datasets** . Questi set di dati possono essere usati solo dalle attività all'interno di questa pipeline, ma non da quelle in altre pipeline. L'esempio seguente definisce una pipeline con due set di dati, InputDataset-rdc and OutputDataset-rdc, da usare all'interno della pipeline:  

> [!IMPORTANT]
> I set di dati con ambito sono supportati solo con pipeline monouso, con valore di pipelineMode impostato su OneTime. Per i dettagli vedere [Pipeline monouso](data-factory-scheduling-and-execution.md#onetime-pipeline) .
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sulle pipeline, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md). 
- Per altre informazioni sulle modalità di pianificazione ed esecuzione delle pipeline, vedere l'articolo [Pianificazione ed esecuzione in Azure Data Factory](data-factory-scheduling-and-execution.md). 
