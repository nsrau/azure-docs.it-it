---
title: Creare set di dati in Azure Data Factory | Microsoft Docs
description: "Informazioni su come creare set di dati in Azure Data Factory con esempi che usano proprietà quali anchorDateTime e offset."
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
ms.date: 08/08/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: f2af15189a7c8643d51e8567b4366726b4b8051c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="datasets-in-azure-data-factory"></a>Set di dati in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-create-datasets.md)
> * [Versione 2 - Anteprima](../concepts-datasets-linked-services.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, che è in anteprima, vedere le informazioni sui [set di dati nella versione 2](../concepts-datasets-linked-services.md).

In questo articolo vengono descritti i set di dati, la procedura di definizione dei set in formato JSON e le modalità di utilizzo nelle pipeline di Azure Data Factory. L'articolo contiene informazioni dettagliate su ogni sezione della definizione JSON del set di dati, ad esempio su struttura, disponibilità e criteri. L'articolo offre anche esempi per l'uso delle proprietà **offset**, **anchorDateTime** e **style** in una definizione JSON del set di dati.

> [!NOTE]
> Se non si ha dimestichezza con Data Factory, vedere [Introduzione al servizio Azure Data Factory](data-factory-introduction.md). Se non si ha esperienza diretta nella creazione di data factory, vedere l'[esercitazione sulla trasformazione dei dati](data-factory-build-your-first-pipeline.md) e [quella sullo spostamento dei dati](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per comprendere meglio questi argomenti. 

## <a name="overview"></a>Panoramica
Una data factory può comprendere una o più pipeline. Una **pipeline** è un raggruppamento logico di **attività** che insieme eseguono un compito. Le attività in una pipeline definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività di copia per copiare i dati da un Server SQL locale a un'archiviazione BLOB di Azure. Quindi, si può usare un'attività Hive che esegue uno script Hive in un cluster HDInsight di Azure per elaborare i dati dall'archiviazione BLOB per produrre dati di output. Infine, è possibile usare una seconda attività di copia per copiare i dati di output in Azure SQL Data Warehouse per la compilazione delle soluzioni di report di business intelligence (BI). Per altre informazioni su pipeline e attività, vedere [Pipeline e attività in Azure Data Factory](data-factory-create-pipelines.md).

Un'attività può non avere alcun **set di dati** di input o averne più di uno e generare uno o più set di dati di output. Un set di dati di input rappresenta l'input per un'attività nella pipeline, un set di dati di output rappresenta l'output dell'attività. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Un set di dati BLOB di Azure, ad esempio, specifica il contenitore BLOB e la cartella nell'archiviazione BLOB da cui la pipeline dovrà leggere i dati. 

Prima di creare un set di dati, creare un **servizio collegato** per collegare l'archivio dati alla data factory. I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti di SQL. Il servizio collegato Archiviazione di Azure,ad esempio, collega l'account di archiviazione alla data factory. Un set di dati BLOB di Azure rappresenta il contenitore e la cartella BLOB che contengono i BLOB di input da elaborare. 

Di seguito è riportato uno scenario di esempio. Per copiare i dati da un'archiviazione BLOB a un Database SQL, si creano due servizi collegati: Archiviazione di Azure e Database SQL di Azure. Quindi, si creano due set di dati: un set di dati BLOB di Azure, che si riferisce al servizio collegato Archiviazione di Azure, e un set di dati della tabella SQL di Azure, che si riferisce al servizio collegato Database SQL di Azure. I servizi collegati Archiviazione di Azure e Database SQL di Azure contengono stringhe di connessione usate da Data Factory in fase di runtime per connettersi rispettivamente all'archiviazione di Azure e al database SQL di Azure. Il set di dati BLOB di Azure specifica il contenitore e una cartella BLOB che contengono i BLOB di input presenti nell'archiviazione BLOB di Azure. Il set di dati della tabella SQL di Azure specifica la tabella SQL del database SQL in cui verranno copiati i dati.

Nel diagramma seguente viene illustrata la relazione tra pipeline, attività, set di dati e il servizio collegato in Data Factory: 

![Relazione tra pipeline, attività, set di dati, i servizi collegati](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Set di dati JSON
Un set di dati in Data Factory viene definito in formato JSON come segue:

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
| type |Tipo del set di dati. Specificare uno dei tipi supportati da Data Factory, ad esempio AzureBlob o AzureSqlTable. <br/><br/>Per informazioni dettagliate, vedere [Tipo di set di dati](#Type). |Sì |ND |
| structure |Schema del set di dati.<br/><br/>Per informazioni dettagliate, vedere [Struttura del set di dati](#Structure). |No |ND |
| typeProperties | Le proprietà del tipo sono diverse per ogni tipo, ad esempio: BLOB di Azure, tabella SQL di Azure. Per informazioni dettagliate sui tipi supportati e le relative proprietà, vedere la sezione [Tipo di set di dati](#Type). |Sì |ND |
| external | Flag booleano per specificare se un set di dati è generato o meno in modo esplicito da una pipeline della data factory. Se il set di dati di input per un'attività non viene generato dalla pipeline corrente, impostare questo flag su true. Impostare questo flag su true per il set di dati di input della prima attività nella pipeline.  |No |false |
| disponibilità | Definisce l'intervallo di elaborazione, ad esempio orario o giornaliero, o il modello di sezionamento per la produzione di set di dati. Ogni unità di dati usata e prodotta da un'esecuzione di attività prende il nome di sezione di dati. Se la disponibilità di un set di dati di output è impostata su giornaliera, ad esempio frequenza: giorno, intervallo: 1, viene prodotta una sezione ogni giorno. <br/><br/>Per informazioni dettagliate, vedere [Disponibilità dei set di dati](#Availability). <br/><br/>Per informazioni dettagliate sul modello di sezionamento dei set di dati, vedere l'articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md). |Sì |ND |
| policy |Definisce i criteri o la condizione che devono soddisfare i sezionamenti di set di dati. <br/><br/>Per informazioni dettagliate, vedere la sezione [Criteri di set di dati](#Policy). |No |ND |

## <a name="dataset-example"></a>Esempio di set di dati
Nell'esempio seguente il set di dati rappresenta la tabella **MyTable** in un database SQL.

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

* **type** è impostato su AzureSqlTable.
* La proprietà del tipo **tableName**, specifica del tipo AzureSqlTable, è impostata su MyTable.
* **linkedServiceName** fa riferimento a un servizio collegato di tipo AzureSqlDatabase, definito nel frammento di codice JSON successivo. 
* L'oggetto **availability frequency** è impostato su Day e **interval** è impostato su 1. Ciò significa che la sezione di set di dati viene prodotta ogni giorno.  

**AzureSqlLinkedService** è definito come segue:

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

Nel frammento di codice JSON precedente:

* **type** è impostato su AzureSqlDatabase.
* La proprietà del tipo **connectionString** specifica le informazioni per la connessione a un database SQL.  

Come si può notare, il servizio collegato definisce la modalità di connessione a un database SQL. Il set di dati indica quale tabella viene usata come input e output per l'attività in una pipeline.   

> [!IMPORTANT]
> A meno che un set di dati non sia generato dalla pipeline, deve essere contrassegnato come **esterno**. Questa impostazione vale in genere per gli input della prima attività in una pipeline.   


## <a name="Type"></a> Tipo di set di dati
Il tipo del set di dati dipende dall'archivio dati usato. Vedere la tabella seguente per un elenco di archivi dati supportati da Data Factory. Fare clic su un archivio dati per informazioni su come creare un servizio collegato e un set di dati per tale archivio dati.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gli archivi dati con * possono essere locali o in un'infrastruttura distribuita come servizio (IaaS) di Azure. Questi archivi di dati richiedono l'installazione del [gateway di gestione dati](data-factory-data-management-gateway.md).

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
La sezione **structure** è facoltativa. Definisce lo schema del set di dati presentando una raccolta di nomi e tipi di dati delle colonne. Usare la sezione structure per inserire informazioni sul tipo da usare per convertire i tipi e mappare le colonne dall'origine alla destinazione. Nell'esempio seguente il set di dati include tre colonne: `slicetimestamp`, `projectname` e `pageviews`. Sono rispettivamente di tipo String, String e Decimal.

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

Attenersi alle linee guida seguenti per decidere quando includere le informazioni sulla struttura e quali elementi inserire nella sezione **structure** .

* **Per le origini dati strutturate**, specificare la sezione structure solo se si vuole eseguire il mapping delle colonne di origine alle colonne del sink e i nomi delle colonne non sono uguali. Questa tipologia di origine dati strutturata archivia le informazioni relative al tipo e allo schema dei dati insieme ai dati stessi. Alcuni esempi di origini dati strutturate sono SQL Server, Oracle e la tabella di Azure. 
  
    Poiché per le origini dati strutturate le informazioni sul tipo sono già disponibili, non includere le informazioni sul tipo quando si include la sezione structure.
* **Per lo schema delle origini dati di lettura, in particolare l'archiviazione BLOB**, è possibile scegliere di archiviare i dati senza aggiungere alcuna informazione sullo schema o sul tipo. Per questi tipi di origini dati, includere la sezione structure per eseguire il mapping delle colonne di origine alle colonne del sink. Includere la sezione structure anche quando il set di dati è un input per un'attività di copia e i tipi di dati del set di dati di origine devono essere convertiti in tipi nativi per il sink. 
    
    Data Factory supporta i seguenti valori per specificare informazioni sul tipo nella sezione structure: **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset e Timespan**. Sono valori dei tipi basati su .NET e conformi a CLS (Common Language Specification).

La data factory esegue automaticamente le conversioni quando si spostano dati da un archivio dati di origine a un archivio dati di sink. 
  

## <a name="dataset-availability"></a>Disponibilità dei set di dati
La sezione **availability** di un set di dati definisce l'intervallo di elaborazione, ad esempio orario, giornaliero o settimanale, per il set di dati. Per altre informazioni sugli intervalli di attività, vedere [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md).

La sezione availability seguente specifica che il set di dati di output viene generato ogni ora oppure che il set di dati di input è disponibile ogni ora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Se la pipeline ha i seguenti orari di inizio e fine:  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Viene prodotto il set di dati di output ogni ora entro gli orari di inizio e fine della pipeline. Questa pipeline genera quindi cinque sezioni di set di dati, una per ogni intervallo di attività (00:00 - 01:00, 01: 00 - 02:00, 02:00 - 03:00, 03:00 - 04:00, 04:00 - 05:00). 

La tabella seguente descrive le proprietà che è possibile usare nella sezione availability:

| Proprietà | Descrizione | Obbligatorio | Default |
| --- | --- | --- | --- |
| frequency |Specifica l'unità di tempo per la produzione di sezioni di set di dati.<br/><br/><b>Frequenza supportata</b>: minuto, ora, giorno, settimana, mese |Sì |ND |
| interval |Specifica un moltiplicatore per la frequenza.<br/><br/>"Frequency x interval" determina la frequenza con cui viene generata la sezione. Se ad esempio è necessario suddividere il set di dati su base oraria, impostare <b>frequency</b> su <b>Hour</b> e <b>interval</b> su <b>1</b>.<br/><br/>Notare che se si specifica **frequency** come **Minute**, è necessario impostare interval su un valore non inferiore a 15. |Sì |ND |
| style |Specifica se la sezione deve essere generata all'inizio o alla fine dell'intervallo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Se **frequency** è impostata su **Month** e **style** è impostata su **EndOfInterval**, la sezione viene generata l'ultimo giorno del mese. Se **style** è impostata su **StartOfInterval**, la sezione viene generata il primo giorno del mese.<br/><br/>Se **frequency** è impostata su **Day** e **style** è impostata su **EndOfInterval**, la sezione viene generata nell'ultima ora del giorno.<br/><br/>Se **frequency** è impostata su **Hour** e **style** è impostata su **EndOfInterval**, la sezione viene generata alla fine dell'ora. Ad esempio, una sezione per il periodo 13.00 - 14.00 viene generata alle 14.00. |No |EndOfInterval |
| anchorDateTime |Definisce la posizione assoluta nel tempo usata dall'utilità di pianificazione per calcolare i limiti della sezione del set di dati. <br/><br/>Si noti che se questa proprietà include parti della data più granulari rispetto alla frequenza specificata, le parti più granulari vengono ignorate. Ad esempio, se l'**intervallo** è **orario** (frequency: hour e interval: 1) e la proprietà **anchorDateTime** contiene **minuti e secondi**, le parti di **anchorDateTime** relative a minuti e secondi vengono ignorate. |No |01/01/0001 |
| offset |Intervallo di tempo in base al quale l'inizio e la fine di tutte le sezioni dei set di dati vengono spostate. <br/><br/>Notare che se si specifica sia **anchorDateTime** che **offset**, il risultato sarà lo spostamento combinato. |No |ND |

### <a name="offset-example"></a>Esempio di offset
Per impostazione predefinita, le sezioni giornaliere (`"frequency": "Day", "interval": 1`) iniziano alle 00.00 (mezzanotte) UTC (Coordinated Universal Time). Se, invece, si desidera impostare l'ora di inizio alle 06:00 UTC, impostare l'offset come illustrato nel frammento riportato di seguito: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Esempio di anchorDateTime
Nell'esempio seguente, il set di dati viene prodotto ogni 23 ore. La prima sezione inizia all'ora specificata da **anchorDateTime**, che è impostata su `2017-04-19T08:00:00` (ora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Esempio di offset/style
Il seguente set di dati è mensile e viene generato il giorno 3 di ogni mese alle ore 08:00 (`3.08:00:00`):

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
| minimumSizeMB |Verifica che i dati presenti nell'**archiviazione BLOB di Azure** soddisfino i requisiti relativi alle dimensioni minime (in megabyte). |Archivio BLOB di Azure |No |ND |
| minimumRows |Verifica che i dati in un **database SQL di Azure** o in una **tabella di Azure** contengano il numero minimo di righe. |<ul><li>Database SQL di Azure</li><li>Tabella di Azure</li></ul> |No |ND |

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

**minimumRows:**

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

A meno che non sia generato da Data Factory, il set di dati deve essere contrassegnato come **external**. Questa impostazione si applica in genere agli input della prima attività di una pipeline, a meno che non si usi il concatenamento di attività o di pipeline.

| Nome | Descrizione | Obbligatorio | Valore predefinito |
| --- | --- | --- | --- |
| dataDelay |Tempo di ritardo del controllo della disponibilità dei dati esterni per la sezione specificata. Ad esempio, è possibile ritardare un controllo orario usando questa impostazione.<br/><br/>Si applica solo all'ora corrente.  Ad esempio, se in questo momento sono le 13:00 e questo valore è di 10 minuti, la convalida inizia alle 13:10.<br/><br/>Si noti che questa impostazione non influisce sulle sezioni passate. Le sezioni con **Slice End Time** + **dataDelay** < **Now** vengono elaborate senza alcun ritardo.<br/><br/>I valori orari superiori a 23:59 ore devono essere specificati nel formato `day.hours:minutes:seconds`. Per specificare 24 ore, ad esempio, non usare 24:00:00. Usare invece 1.00:00:00. Il valore 24:00:00 viene considerato 24 giorni (24.00:00:00). Per 1 giorno e 4 ore, specificare 1:04:00:00. |No |0 |
| retryInterval |Tempo di attesa tra un errore e il tentativo successivo. Questa impostazione si applica all'ora corrente. Se il tentativo precedente ha avuto esito negativo, il tentativo successivo si avvia dopo il periodo **retryInterval**. <br/><br/>Se in questo momento sono le 13:00, viene avviato il primo tentativo. Se la durata per completare il primo controllo di convalida è 1 minuto e l'operazione non è riuscita, il tentativo successivo è alle 13:00 + 1 min (durata) + 1 min (intervallo tentativi) = 13:02. <br/><br/>Per le sezioni passate, non si verifica alcun ritardo. La ripetizione avviene immediatamente. |No |00:01:00 (1 minute) |
| retryTimeout |Timeout per ogni nuovo tentativo.<br/><br/>Se questa proprietà è impostata su 10 minuti, la convalida deve essere completata entro 10 minuti. Se sono necessari più di 10 minuti per eseguire la convalida, il tentativo viene sospeso.<br/><br/>Se tutti i tentativi per la convalida raggiungono il timeout, la sezione viene contrassegnata come **TimedOut**. |No |00:10:00 (10 minutes) |
| maximumRetry |Numero di tentativi di controllo della disponibilità dei dati esterni. Il valore massimo consentito è 10. |No |3 |


## <a name="create-datasets"></a>Creare set di dati
È possibile creare set di dati usando uno di questi strumenti o SDK: 

- Copia guidata 
- Portale di Azure
- Visual Studio
- PowerShell
- Modello di Azure Resource Manager
- API REST
- API .NET

Vedere le esercitazioni seguenti per istruzioni dettagliate sulla creazione di pipeline e set di dati usando uno di questi strumenti o SDK:
 
- [Creare una pipeline con un'attività di trasformazione dati](data-factory-build-your-first-pipeline.md)
- [Creare una pipeline con un'attività di spostamento dati](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Dopo aver creato e distribuito una pipeline, è possibile gestire e monitorare le pipeline usando i pannelli del portale di Azure o l'app di gestione e monitoraggio. Vedere gli argomenti seguenti per le istruzioni dettagliate: 

- [Monitorare e gestire le pipeline di Azure Data Factory con il portale di Azure e PowerShell](data-factory-monitor-manage-pipelines.md)
- [Monitorare e gestire le pipeline di Azure Data Factory con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Set di dati con ambito
È possibile creare set di dati con ambito limitato a una pipeline usando la proprietà **datasets** . Questi set di dati possono essere usati solo dalle attività all'interno di questa pipeline, non da quelle in altre pipeline. L'esempio seguente definisce una pipeline con due set di dati, InputDataset-rdc e OutputDataset-rdc, da usare all'interno della pipeline.  

> [!IMPORTANT]
> I set di dati con ambito sono supportati solo con le pipeline monouso, in cui **pipelineMode** è impostata su **OneTime**. Per i dettagli vedere [Pipeline monouso](data-factory-create-pipelines.md#onetime-pipeline) .
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
- Per altre informazioni sulle pipeline, vedere [Pipeline e attività in Azure Data Factory](data-factory-create-pipelines.md). 
- Per altre informazioni sulle modalità di pianificazione ed esecuzione delle pipeline, vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md). 
