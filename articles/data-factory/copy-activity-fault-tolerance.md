---
title: "Tolleranza di errore dell'attività di copia in Azure Data Factory | Microsoft Docs"
description: "Informazioni su come aggiungere la tolleranza di errore all'attività di copia in Azure Data Factory ignorando le righe incompatibili."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jingwang
ms.openlocfilehash: d96c89ed3650c09ac6465e30754ef1155b06d601
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Tolleranza di errore dell'attività di copia in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Versione 2 - Anteprima](copy-activity-fault-tolerance.md)

L'attività di copia di Azure Data Factory offre due modi per gestire le righe incompatibili durante la copia di dati tra gli archivi dati di origine e sink:

- È possibile interrompere l'attività di copia quando vengono rilevati dati incompatibili (comportamento predefinito).
- È possibile continuare a copiare tutti i dati aggiungendo la tolleranza di errore e ignorando le righe di dati incompatibili. È anche possibile registrare le righe incompatibili nell'archivio BLOB di Azure. È quindi possibile esaminare il log per conoscere la causa dell'errore, correggere i dati nell'origine dati e ripetere l'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [tolleranza di errore dell'attività di copia della versione 1](v1/data-factory-copy-activity-fault-tolerance.md).


 ## <a name="supported-scenarios"></a>Scenari supportati
L'attività di copia supporta tre scenari per rilevare, ignorare e registrare i dati incompatibili:

- **Incompatibilità tra il tipo di dati di origine e il tipo nativo sink**. <br/><br/> Esempio: si vogliono copiare dati da un file CSV nell'archiviazione BLOB a un database SQL con una definizione di schema che contiene tre colonne di tipo INT. Le righe del file CSV contenenti dati numerici, ad esempio 123, 456, 789, vengono copiate nell'archivio sink. Tuttavia, le righe che contengono valori non numerici, ad esempio 123, 456, abc, vengono rilevate come incompatibili e vengono ignorate.
- **Mancata corrispondenza nel numero di colonne tra l'origine e il sink**. <br/><br/> Esempio: si vogliono copiare dati da un file CSV nell'archivio BLOB a un database SQL con una definizione di schema che contiene sei colonne. Le righe del file CSV che contengono sei colonne vengono copiate nell'archivio sink. Le righe del file CSV che contengono più o meno di sei colonne vengono rilevate come incompatibili e vengono ignorate.
- **Violazione della chiave primaria durante la scrittura in un database relazionale**.<br/><br/> Esempio: si vogliono copiare dati da un'istanza di SQL Server a un database SQL. Il database SQL del sink contiene la definizione di una chiave primaria, che invece manca nell'istanza di SQL Server di origine. Non è possibile copiare nel sink le righe duplicate presenti nell'origine. L'attività di copia copierà nel sink solo la prima riga dei dati di origine. Le righe di origine successive che contengono il valore della chiave primaria duplicato vengono rilevate come incompatibili e vengono ignorate.

## <a name="configuration"></a>Configurazione
L'esempio seguente offre la definizione JSON per specificare di ignorare le righe incompatibili nell'attività di copia:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "AzureBlobLinkedService",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```
Proprietà | Descrizione | Valori consentiti | Obbligatorio
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Specifica se ignorare o meno le righe incompatibili durante la copia. | True <br/>False (impostazione predefinita) | No
redirectIncompatibleRowSettings | Un gruppo di proprietà che può essere specificato quando si vuole registrare le righe incompatibili. | &nbsp; | No
linkedServiceName | Servizio collegato di Archiviazione di Azure con cui archiviare il log che contiene le righe ignorate. | Nome di un servizio collegato AzureStorage o AzureStorageSas che fa riferimento all'istanza di archiviazione da usare per archiviare il file di log. | No
path | Percorso del file di log che contiene le righe ignorate. | Specificare il percorso dell'archivio BLOB da usare per registrare i dati incompatibili. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No

## <a name="monitor-skipped-rows"></a>Monitorare le righe ignorate
Al termine dell'esecuzione dell'attività di copia, è possibile visualizzare il numero di righe ignorate nell'output dell'attività di copia:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Se si configura la registrazione delle righe incompatibili, vedere il file di log al percorso seguente: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`. 

Il file di log contiene le righe ignorate e la causa radice dell'incompatibilità.

Nel file vengono registrati sia i dati originali che l'errore corrispondente. Di seguito è riportato un esempio del contenuto del file di log:

```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Prestazioni dell'attività di copia](copy-activity-performance.md)


