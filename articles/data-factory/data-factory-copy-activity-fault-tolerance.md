---
title: "Tolleranza di errore dell'attività di copia di Azure Data Factory: ignorare le righe incompatibili | Microsoft Docs"
description: Informazioni sulla tolleranza di errore per ignorare le righe incompatibili durante la copia usando Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d613537657af3bbe379a53e92532bf6b184d762b
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="copy-activity-fault-tolerance---skip-incompatible-rows"></a>Tolleranza di errore dell'attività di copia: ignorare le righe incompatibili

L'[attività di copia](data-factory-data-movement-activities.md) offre diverse opzioni per le righe incompatibili durante la copia di dati tra gli archivi dati di origine e sink. È possibile scegliere di interrompere l'attività di copia quando vengono rilevati dati incompatibili (comportamento predefinito) oppure continuare a copiare tutti i dati ignorando le righe incompatibili. È anche possibile registrare le righe incompatibili nel BLOB di Azure per poter esaminare la causa dell'errore, correggere i dati nell'origine dati e riprovare.

## <a name="supported-scenarios"></a>Scenari supportati
Attualmente l'attività di copia consente di rilevare, ignorare e registrare la situazione di incompatibilità seguente durante la copia:

- **Incompatibilità dei tipi di dati tra i tipi nativi di origine e sink**

    Esempio: copia da un file con estensione csv nel BLOB di Azure al database SQL di Azure con lo schema definito nel database SQL di Azure con tre colone di tipo *INT*. Le righe con dati numerici (ad esempio `123,456,789`) nel file con estensione csv di origine vengono copiate correttamente, mentre le righe contenenti valori non numerici (ad esempio `123,456,abc`) vengono ignorate come righe incompatibili.

- **Numero di colonne non corrispondente tra origine e sink**

    Esempio: copia da un file con estensione csv nel BLOB di Azure al database SQL di Azure con lo schema definito nel database SQL di Azure con sei colonne. Le righe contenenti sei colonne nel file con estensione csv di origine vengono copiate correttamente, mentre le righe con un numero di colonne diverso vengono ignorate come righe incompatibili.

- **Violazione della chiave primaria durante la scrittura in un database relazionale**

    Esempio: copia da un server SQL al database SQL di Azure con chiave primaria definita nel sink nel database SQL di Azure ma non nel server SQL di origine. Le righe duplicate che possono essere presenti nell'origine non sono consentite durante la scrittura nel sink. L'attività copia solo la prima riga nel sink e ignora la seconda o le righe successive con valore di chiave primaria duplicato dall'origine al sink.

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| enableSkipIncompatibleRow | Specificare se ignorare o meno le righe incompatibili durante la copia. | True<br/>False (impostazione predefinita) | No |
| redirectIncompatibleRowSettings | Un gruppo di proprietà che può essere specificato quando si vuole registrare le righe incompatibili. | &nbsp; | No |
| linkedServiceName | Il servizio collegato dell'archiviazione di Azure per archiviare il log che contiene tutte le righe ignorate. | Specificare il nome del servizio collegato [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) che fa riferimento all'istanza di archiviazione usata per archiviare il file di log. | No |
| path | Il percorso del file di log che contiene tutte le righe ignorate. | Specificare il percorso dell'archiviazione BLOB che deve registrare i dati incompatibili. Se non si specifica un percorso, il servizio crea automaticamente un contenitore. | No |

## <a name="monitoring"></a>Monitoraggio
Al termine dell'esecuzione dell'attività di copia, è possibile visualizzare il numero di righe ignorate nella sezione di monitoraggio come illustrato di seguito:

![Monitoraggio delle righe incompatibili ignorate](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Se si configura la registrazione delle righe incompatibili, per determinare le righe ignorate e la causa principale dell'incompatibilità, vedere il file di log nel percorso seguente: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`.

Nel file vengono registrati sia i dati originali che l'errore corrispondente. Di seguito è riportato un esempio del contenuto del file di log:
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).,
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'attività di copia di Azure Data Factory, vedere [Spostare dati con l'attività di copia](data-factory-data-movement-activities.md).
