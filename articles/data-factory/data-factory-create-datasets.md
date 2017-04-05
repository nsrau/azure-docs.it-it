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
ms.date: 1/30/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 34148a8fe2fe5b9ebd2ff4a01ff523f7f5a74c67
ms.lasthandoff: 03/24/2017


---
# <a name="datasets-in-azure-data-factory"></a>Set di dati in Azure Data Factory
Questo articolo descrive i set di dati in Azure Data Factory e include esempi di database offset/style, anchorDateTime e offset.

Con la creazione di un set di dati si crea un puntatore ai dati che si vuole elaborare. I dati di input e di output vengono elaborati in un'attività, che a sua volta è contenuta in una pipeline. Un set di dati di input rappresenta l'input per un'attività nella pipeline, un set di dati di output rappresenta l'output dell'attività.

I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Dopo aver creato un set di dati, è possibile usarlo con le attività in una pipeline. Ad esempio, un set di dati può essere configurato come set di dati di input o di output di un'attività di copia o un'attività HDInsightHive. Il portale di Azure offre un layout visivo di tutte le pipeline, oltre che dell'input e dell'output dei dati. Questo permette di visualizzare a colpo d'occhio tutte le relazioni e le dipendenze delle pipeline tra tutte le origini, per conoscere sempre la provenienza e la destinazione dei dati.

Azure Data Factory permette di ottenere dati da un set di dati usando l'attività di copia in una pipeline.

> [!NOTE]
> Chi non conosce bene Azure Data Factory può consultare [Introduzione ad Azure Data Factory](data-factory-introduction.md) per una panoramica sul servizio. Per un'esercitazione su come creare la prima data factory, vedere [Creare la prima data factory](data-factory-build-your-first-pipeline.md) . Questi due articoli forniscono le informazioni generali necessarie per comprendere meglio questo articolo.
>
>

## <a name="define-datasets"></a>Definire i set di dati
Un set di dati in Azure Data Factory viene definito come segue:

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
| type |Tipo del set di dati. Specificare uno dei tipi supportati da Azure Data Factory, ad esempio AzureBlob, AzureSqlTable. <br/><br/>Per informazioni dettagliate, vedere la sezione [Tipo di set di dati](#Type) . |Sì |ND |
| structure |Schema del set di dati.<br/><br/>Per informazioni dettagliate, vedere la sezione [Struttura del set di dati](#Structure) . |di serie |ND |
| typeProperties |Proprietà che corrispondono al tipo selezionato. Per informazioni dettagliate sui tipi supportati e sulle relative proprietà, vedere la sezione [Tipo di set di dati](#Type) . |Sì |ND |
| external |Flag booleano per specificare se un set di dati è generato o meno in modo esplicito da una pipeline della data factory. |No |false |
| disponibilità |Definisce la finestra di elaborazione o il modello di sezionamento per la produzione di set di dati <br/><br/>Per informazioni dettagliate, vedere la sezione [Disponibilità dei set di dati](#Availability) . <br/><br/>Per informazioni dettagliate sul modello di sezionamento dei set di dati, vedere l'articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md). |Sì |ND |
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
* linkedServiceName fa riferimento a un servizio collegato di tipo AzureSqlDatabase, che è definito nel seguente frammento di codice JSON.
* availability frequency è impostata su Day e interval è impostato su 1 e significa che la sezione viene generata ogni giorno.  

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

Come si può notare, il servizio collegato definisce come connettersi a un database SQL di Azure. Il set di dati definisce quale tabella viene usata come input/output per l'attività in una pipeline. La sezione activity nel codice JSON della [pipeline](data-factory-create-pipelines.md) specifica se il set di dati viene usato come set di dati di input o di output.

> [!IMPORTANT]
> A meno che un set di dati non sia generato da Azure Data Factory, deve essere contrassegnato come **external**. Questa impostazione vale in genere per gli input della prima attività in una pipeline.   
>
>

## <a name="Type"></a> Tipo di set di dati
Le origini dati supportate e i tipi di set di dati sono allineati. Per altre informazioni sui tipi e sulla configurazione dei set di dati, vedere gli argomenti citati nell'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Ad esempio, se si usano dati da un database SQL di Azure, fare clic su Database SQL di Azure nell'elenco di archivi dati supportati per visualizzare informazioni dettagliate.  

## <a name="Structure"></a>Struttura del set di dati
La sezione **structure** è una sezione **opzionale** che definisce lo schema del set di dati. Contiene una raccolta di nomi e tipi di dati delle colonne. Si usa la sezione della struttura per fornire informazioni sul tipo per le **conversioni di tipi** o per eseguire il **mapping di colonne**. Nell'esempio seguente il set di dati ha tre colonne, ovvero `slicetimestamp`, `projectname` e `pageviews`, che sono rispettivamente di tipo String, String e Decimal.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Ogni colonna contiene le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome della colonna. |Sì |
| type |Tipo di dati della colonna.  |No |
| culture |Impostazioni cultura basate su .NET da usare quando il tipo è specificato ed un tipo .NET `Datetime` o `Datetimeoffset`. Il valore predefinito è "en-us". |No |
| format |Stringa di formato da usare quando il tipo è specificato ed è un tipo .NET `Datetime` o `Datetimeoffset`. |No |

Attenersi alle linee guida seguenti per decidere il momento in cui includere informazioni su "structure" e gli elementi da inserire nella sezione **structure** .

* **Per le origini dati strutturate** in cui, oltre a informazioni sullo schema e sul tipo di dati, sono archiviati i dati stessi, ovvero origini come tabelle di SQL Server, Oracle, Azure e così via, è necessario specificare la sezione "structure" solo se si vuole eseguire il mapping delle colonne di origine alle colonne del sink e i relativi nomi non sono uguali. 
  
    Poiché per le origini dati strutturate le informazioni sul tipo sono già disponibili, non si deve includere le informazioni sul tipo quando si include la sezione "structure".
* **Per lo schema delle origini dati di lettura, in particolare BLOB di Azure**, è possibile scegliere di archiviare i dati senza aggiungere alcuna informazione sullo schema o sul tipo. Per questi tipi di origini dati, includere "structure" quando si desidera eseguire il mapping delle colonne di origine alle colonne del sink (o) quando il set di dati è un set di dati di input per un'attività di copia e i tipi di dati del set di dati di origine devono essere convertiti nei tipi nativi per il sink. 
    
    Data Factory supporta i valori di tipo basati su .NET conformi a CLS per specificare informazioni sul tipo nella sezione "structure" in relazione allo schema su origini dati di lettura come BLOB di Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan.

La data factory esegue automaticamente le conversioni quando si spostano dati da un archivio dati di origine a un archivio dati di sink. 
  

## <a name="Availability"></a> Disponibilità dei set di dati
La sezione **availability** in un set di dati definisce la frequenza di elaborazione, vale a dire oraria, giornaliera, settimanale e così via, oppure il modello di sezionamento per il set di dati. Vedere l'articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) per altre informazioni sul modello di sezionamento e dipendenza di set di dati.

La sezione availability seguente specifica che il set di dati di output viene generato ogni ora oppure che il set di dati di input è disponibile ogni ora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

La tabella seguente descrive le proprietà che è possibile usare nella sezione availability:

| Proprietà | Descrizione | Obbligatorio | Default |
| --- | --- | --- | --- |
| frequency |Specifica l'unità di tempo per la produzione di sezioni di set di dati.<br/><br/><b>Frequenza supportata</b>: minuto, ora, giorno, settimana, mese |Sì |ND |
| interval |Specifica un moltiplicatore per la frequenza.<br/><br/>"Intervallo di frequenza x" determina la frequenza con cui viene generata la sezione.<br/><br/>Se è necessario suddividere il set di dati su base oraria, impostare l'opzione <b>Frequenza</b> su <b>Ora</b> e <b>Intervallo</b> su <b>1</b>.<br/><br/><b>Nota</b> : se si specifica frequency come Minute, è consigliabile impostare interval su un valore non inferiore a 15 |Sì |ND |
| style |Specifica se la sezione deve essere generata all'inizio o alla fine dell'intervallo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se frequency è impostato su Month e style è impostato su EndOfInterval, la sezione viene generata l'ultimo giorno del mese. Se style è impostato su StartOfInterval, la sezione viene generata il primo giorno del mese.<br/><br/>Se l'opzione Frequnza è impostata su Mese e l'opzione Stile è impostata su EndOfInterval, la sezione viene generata l'ultima ora del giorno.<br/><br/>Se frequency è impostato su Hour e style è impostato su EndOfInterval, la sezione viene generata alla fine dell'ora. Ad esempio, una sezione per il periodo 13.00 - 14.00 viene generata alle 14.00. |No |EndOfInterval |
| anchorDateTime |Definisce la posizione assoluta nel tempo usata dall'utilità di pianificazione per calcolare i limiti della sezione del set di dati. <br/><br/><b>Nota:</b> se AnchorDateTime include parti della data più granulari rispetto alla frequenza, le parti più granulari vengono ignorate. <br/><br/>Ad esempio, se l'<b>intervallo</b> è <b>orario</b> (ovvero frequenza: ora e intervallo: 1) e <b>AnchorDateTime</b> contiene <b>minuti e secondi</b>, le parti <b>minuti e secondi</b> di AnchorDateTime vengono ignorate. |No |01/01/0001 |
| offset |Intervallo di tempo in base al quale l'inizio e la fine di tutte le sezioni dei set di dati vengono spostate. <br/><br/><b>Nota:</b> se vengono specificati sia anchorDateTime che offset, il risultato sarà lo spostamento combinato. |No |ND |

### <a name="offset-example"></a>Esempio di offset
Sezioni giornaliere che iniziano alle 6.00 invece che alla mezzanotte predefinita.

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```

La proprietà **frequency** è impostata su **Day** e la proprietà **interval** su **1** (una volta al giorno), in modo che la sezione venga generata alle 6.00 anziché all'ora predefinita, ovvero mezzanotte. Tenere presente che questo orario è espresso in base all'ora UTC.

## <a name="anchordatetime-example"></a>Esempio di anchorDateTime
**Esempio:** sezioni dei set di dati di 23 ore che iniziano il 19-04-2007 alle 08:00:00

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2007-04-19T08:00:00"    
}
```

## <a name="offsetstyle-example"></a>Esempio di offset/style
Se è necessario un set di dati su base mensile in una data e a un'ora specifiche, ad esempio il 3 di ogni mese alle ore 8.00, usare il tag **offset** per impostare la data e l'ora di esecuzione.

```json
{
  "name": "MyDataset",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "availability": {
      "frequency": "Month",
      "interval": 1,
      "offset": "3.08:00:00",
      "style": "StartOfInterval"
    }
  }
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
| dataDelay |Tempo di attesa per il controllo sulla disponibilità dei dati esterni per il sezionamento specificato. Ad esempio, se i dati devono essere disponibili ogni ora, il controllo per vedere se i dati esterni sono disponibili e la sezione corrispondente è Ready può essere ritardato usando dataDelay.<br/><br/>Si applica solo all'ora corrente.  Ad esempio, se in questo momento sono le 13:00 e questo valore è di 10 minuti, la convalida inizia alle 13:10.<br/><br/>Questa impostazione non influisce sulle sezioni nel passato; le sezioni con i parametri Ora di fine sezione + dataDelay < Ora vengono elaborate senza alcun ritardo.<br/><br/>Valori superiori a 23:59 ore devono essere specificati nel formato giorno.ore:minuti:secondi. Per specificare 24 ore, ad esempio, non utilizzare 24:00:00; utilizzare invece 1.00:00:00. Il valore 24:00:00 viene considerato 24 giorni (24.00:00:00). Per 1 giorno e 4 ore, specificare 1:04:00:00. |No |0 |
| retryInterval |Il tempo di attesa tra un errore e il successivo tentativo. Si applica al tempo presente; Se il precedente tentativo non è riuscito, dopo di esso si aspetta tale tempo. <br/><br/>Se in questo momento sono le 13:00, viene avviato il primo tentativo. Se la durata per completare il primo controllo di convalida è 1 minuto e l'operazione non è riuscita, il tentativo successivo è alle 13:00 + 1 min (durata) + 1 min (intervallo tentativi) = 13:02. <br/><br/>Per le sezioni passate, non si verifica alcun ritardo. La ripetizione avviene immediatamente. |No |00:01:00 (1 minute) |
| retryTimeout |Timeout per ogni nuovo tentativo.<br/><br/>Se questa proprietà è impostata su 10 minuti, la convalida deve essere completata entro 10 minuti. Se sono necessari più di 10 minuti per eseguire la convalida, il tentativo viene sospeso.<br/><br/>Se tutti i tentativi per la convalida scadono, la sezione viene contrassegnata come TimedOut. |No |00:10:00 (10 minutes) |
| maximumRetry |Numero di volte per controllare la disponibilità dei dati esterni. Il valore massimo consentito è 10. |No |3 |

## <a name="scoped-datasets"></a>Set di dati con ambito
È possibile creare set di dati con ambito limitato a una pipeline usando la proprietà **datasets** . Questi set di dati possono essere usati solo dalle attività all'interno di questa pipeline, ma non da quelle in altre pipeline. L'esempio seguente definisce una pipeline con due set di dati, InputDataset-rdc and OutputDataset-rdc, da usare all'interno della pipeline:  

> [!IMPORTANT]
> I set di dati con ambito sono supportati solo con pipeline monouso, con valore di **pipelineMode** impostato su **OneTime**. Per i dettagli vedere [Pipeline monouso](data-factory-scheduling-and-execution.md#onetime-pipeline) .
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

