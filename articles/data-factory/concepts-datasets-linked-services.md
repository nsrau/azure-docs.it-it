---
title: Set di dati e servizi collegati in Azure Data Factory | Microsoft Docs
description: Informazioni sui set di dati e sui servizi collegati in Data Factory. I servizi collegati collegano archivi calcolo o dati a una data factory. I set di dati rappresentano dati di input o di output.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: 
ms.date: 09/05/2017
ms.author: shlo
ms.openlocfilehash: a13e19c7e1a22581b14d1a96e20b8a649c303fc3
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2017
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Set di dati e servizi collegati in Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-create-datasets.md)
> * [Versione 2 - Anteprima](concepts-datasets-linked-services.md)

Questo articolo descrive le caratteristiche dei set di dati, la procedura di definizione dei set in formato JSON e le modalità di uso nelle pipeline di Azure Data Factory V2. 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Datasets in Data Factory V1](v1/data-factory-create-datasets.md) (Set di dati in Data Factory V1).

Se non si ha dimestichezza con Data Factory, vedere [Introduzione al servizio Azure Data Factory](introduction.md). 

## <a name="overview"></a>Panoramica
Una data factory può comprendere una o più pipeline. Una **pipeline** è un raggruppamento logico di **attività** che insieme eseguono un compito. Le attività in una pipeline definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività di copia per copiare i dati da un Server SQL locale a un'archiviazione BLOB di Azure. Quindi, si può usare un'attività Hive che esegue uno script Hive in un cluster HDInsight di Azure per elaborare i dati dall'archiviazione BLOB per produrre dati di output. Infine, è possibile usare una seconda attività di copia per copiare i dati di output in Azure SQL Data Warehouse per la compilazione delle soluzioni di report di business intelligence (BI). Per altre informazioni su pipeline e attività, vedere [Pipeline e attività](concepts-pipelines-activities.md) in Azure Data Factory.

Un **set di dati** è una visualizzazione dati denominata che punta o fa riferimento ai dati usati come input e output nelle **attività**. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Un set di dati BLOB di Azure, ad esempio, specifica il contenitore BLOB e la cartella nell'archiviazione BLOB da cui l'attività deve leggere i dati.

Prima di creare un set di dati, è necessario creare un **servizio collegato** per collegare l'archivio dati alla data factory. I servizi collegati sono molto simili a stringhe di connessione e definiscono le informazioni necessarie per la connessione di Data Factory a risorse esterne. In altre parole, il set di dati rappresenta la struttura dei dati all'interno degli archivi dati collegati e il servizio collegato definisce la connessione all'origine dati. Il servizio collegato Archiviazione di Azure,ad esempio, collega l'account di archiviazione alla data factory. Un set di dati BLOB di Azure rappresenta il contenitore BLOB e la cartella all'interno dell'account di archiviazione di Azure che contiene i BLOB di input da elaborare.

Di seguito è riportato uno scenario di esempio. Per copiare i dati da un'archiviazione BLOB a un Database SQL, si creano due servizi collegati: Archiviazione di Azure e Database SQL di Azure. Quindi, si creano due set di dati: un set di dati BLOB di Azure, che si riferisce al servizio collegato Archiviazione di Azure, e un set di dati della tabella SQL di Azure, che si riferisce al servizio collegato Database SQL di Azure. I servizi collegati Archiviazione di Azure e Database SQL di Azure contengono stringhe di connessione usate da Data Factory in fase di runtime per connettersi rispettivamente all'archiviazione di Azure e al database SQL di Azure. Il set di dati BLOB di Azure specifica il contenitore e una cartella BLOB che contengono i BLOB di input presenti nell'archiviazione BLOB di Azure. Il set di dati della tabella SQL di Azure specifica la tabella SQL del database SQL in cui verranno copiati i dati.

Nel diagramma seguente viene illustrata la relazione tra pipeline, attività, set di dati e il servizio collegato in Data Factory:

![Relazione tra pipeline, attività, set di dati, i servizi collegati](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Set di dati JSON
Un set di dati in Data Factory viene definito in formato JSON come segue:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                 "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}

```
La tabella seguente descrive le proprietà nel codice JSON precedente:

Proprietà | Descrizione | Obbligatorio | Default
-------- | ----------- | -------- | -------
name | Nome del set di dati. | Vedere [Azure Data Factory - Regole di denominazione](naming-rules.md). | Sì | ND
type | Tipo del set di dati. | Specificare uno dei tipi supportati da Data Factory, ad esempio AzureBlob o AzureSqlTable. <br/><br/>Per informazioni dettagliate, vedere [Tipi di set di dati](#dataset-types). | Sì | ND
structure | Schema del set di dati. | Per informazioni dettagliate, vedere [Struttura del set di dati](#dataset-structure). | No | ND
typeProperties | Le proprietà del tipo sono diverse per ogni tipo, ad esempio: BLOB di Azure, tabella SQL di Azure. Per informazioni dettagliate sui tipi supportati e le relative proprietà, vedere la sezione [Tipo di set di dati](#dataset-type). | Sì | ND

## <a name="dataset-example"></a>Esempio di set di dati
Nell'esempio seguente il set di dati rappresenta la tabella MyTable in un database SQL.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                 "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Tenere presente quanto segue:

- type è impostato su AzureSqlTable.
- La proprietà del tipo tableName, specifica del tipo AzureSqlTable, è impostata su MyTable.
- linkedServiceName fa riferimento a un servizio collegato di tipo AzureSqlDatabase, che è definito nel frammento di codice JSON successivo.

## <a name="linked-service-example"></a>Esempio di servizio collegato
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
Nel frammento di codice JSON precedente:

- **type** è impostato su AzureSqlDatabase.
- La proprietà del tipo **connectionString** specifica le informazioni per la connessione a un database SQL.

Come si può notare, il servizio collegato definisce la modalità di connessione a un database SQL. Il set di dati indica quale tabella viene usata come input e output per l'attività in una pipeline.

## <a name="dataset-type"></a>Tipo di set di dati
Esistono molti tipi diversi di set di dati, a seconda dell'archivio dati usato. Vedere la tabella seguente per un elenco di archivi dati supportati da Data Factory. Fare clic su un archivio dati per informazioni su come creare un servizio collegato e un set di dati per tale archivio dati.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Nell'esempio della sezione precedente, il tipo di set di dati è impostato su **AzureSqlTable**. Analogamente, per un set di dati BLOB di Azure, il tipo di set di dati è impostato su **AzureBlob** come illustrato nel codice JSON seguente:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                 "type": "LinkedServiceReference",
        }, 
 
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```
## <a name="dataset-structure"></a>Struttura del set di dati
La sezione **structure** è facoltativa. Definisce lo schema del set di dati presentando una raccolta di nomi e tipi di dati delle colonne. Usare la sezione structure per inserire informazioni sul tipo da usare per convertire i tipi e mappare le colonne dall'origine alla destinazione. Nell'esempio seguente il set di dati ha tre colonne: timestamp, projectname e pageviews. Sono rispettivamente di tipo String, String e Decimal.

```json
[
    { "name": "timestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Ogni colonna della struttura contiene le proprietà seguenti:

Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
name | Nome della colonna. | Sì
type | Tipo di dati della colonna. | No
culture | Cultura basata su .NET da usare quando il tipo è un tipo .NET: `Datetime` o `Datetimeoffset`. Il valore predefinito è `en-us`. | No
format | Stringa di formato da usare quando il tipo è un tipo .NET: `Datetime` o `Datetimeoffset`. | No

Attenersi alle linee guida seguenti per decidere quando includere le informazioni sulla struttura e quali elementi inserire nella sezione **structure** .

- **Per le origini dati strutturate**, specificare la sezione structure solo se si vuole eseguire il mapping delle colonne di origine alle colonne del sink e i nomi delle colonne non sono uguali. Questa tipologia di origine dati strutturata archivia le informazioni relative al tipo e allo schema dei dati insieme ai dati stessi. Alcuni esempi di origini dati strutturate sono SQL Server, Oracle e il database SQL di Azure.<br/><br/>Poiché per le origini dati strutturate le informazioni sul tipo sono già disponibili, non includere le informazioni sul tipo quando si include la sezione structure.
- **Per lo schema delle origini dati di lettura, in particolare l'archiviazione BLOB**, è possibile scegliere di archiviare i dati senza aggiungere alcuna informazione sullo schema o sul tipo. Per questi tipi di origini dati, includere la sezione structure per eseguire il mapping delle colonne di origine alle colonne del sink. Includere la sezione structure anche quando il set di dati è un input per un'attività di copia e i tipi di dati del set di dati di origine devono essere convertiti in tipi nativi per il sink.<br/><br/> Per le informazioni sul tipo all'interno delle strutture, Data Factory supporta i valori seguenti: `Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset, and Timespan`. 

Altre informazioni sul modo in cui la data factory esegue il mapping dei dati di origine al sink e su quando specificare le informazioni sulla struttura sono disponibili in [Mapping dello schema e dei tipi]( copy-activity-schema-and-type-mapping.md).

## <a name="create-datasets"></a>Creare set di dati
È possibile creare set di dati tramite uno di questi strumenti o SDK: [API .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), modello di Azure Resource Manager e portale di Azure

## <a name="v1-vs-v2-datasets"></a>Set di dati della versione 1 e set di dati della versione 2

Di seguito sono riportate alcune differenze tra i set di dati di Data Factory della versione 1 e quelli della versione 2: 

- La proprietà external non è supportata nella versione 2 e viene sostituita da un [trigger](concepts-pipeline-execution-triggers.md).
- Le proprietà policy e availability non sono supportate nella versione 2. L'ora di inizio di una pipeline dipende da [trigger](concepts-pipeline-execution-triggers.md).
- I set di dati con ambito (set di dati definiti in una pipeline) non sono supportati nella versione 2. 

## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti per istruzioni dettagliate sulla creazione di pipeline e set di dati tramite uno di questi strumenti o SDK. 

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Avvio rapido: Creare una data factory tramite .NET)
- [Quickstart: create a data factory using PowerShell](quickstart-create-data-factory-powershell.md) (Avvio rapido: Creare una data factory tramite PowerShell)
- [Quickstart: create a data factory using REST API](quickstart-create-data-factory-rest-api.md) (Avvio rapido: Creare una data factory tramite API REST)
- Quickstart: create a data factory using Azure portal (Avvio rapido: Creare una data factory tramite il portale di Azure)
