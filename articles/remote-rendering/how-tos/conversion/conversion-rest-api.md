---
title: API REST per la conversione di asset
description: Viene descritto come convertire un asset tramite l'API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 0a0feb6b638cb6e3a74fcd30baea5e8a04375699
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82857799"
---
# <a name="use-the-model-conversion-rest-api"></a>Usare l'API REST per la conversione di modelli

Il servizio di [conversione del modello](model-conversion.md) viene controllato tramite un' [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Questo articolo descrive i dettagli dell'API del servizio di conversione.

## <a name="regions"></a>Regioni

Vedere l' [elenco delle aree disponibili](../../reference/regions.md) per gli URL di base a cui inviare le richieste.

## <a name="common-headers"></a>Intestazioni comuni

### <a name="common-request-headers"></a>Intestazioni di richiesta comuni

È necessario specificare queste intestazioni per tutte le richieste:

- L'intestazione dell' **autorizzazione** deve avere il valore "Bearer [*token*]", dove [*token*] è un [token di accesso al servizio](../tokens.md).

### <a name="common-response-headers"></a>Intestazioni di risposta comuni

Tutte le risposte contengono le intestazioni seguenti:

- L'intestazione **MS-CV** contiene una stringa univoca che può essere usata per tracciare la chiamata all'interno del servizio.

## <a name="endpoints"></a>Endpoint

Il servizio di conversione fornisce tre endpoint API REST per:

- avviare la conversione del modello usando un account di archiviazione collegato all'account di rendering remoto di Azure. 
- avviare la conversione del modello usando le *firme di accesso condiviso (SAS)* fornite.
- eseguire query sullo stato di conversione

### <a name="start-conversion-using-a-linked-storage-account"></a>Avviare la conversione usando un account di archiviazione collegato
L'account di rendering remoto di Azure deve avere accesso all'account di archiviazione specificato seguendo i passaggi per collegare gli [account di archiviazione](../create-an-account.md#link-storage-accounts).

| Endpoint | Metodo |
|-----------|:-----------|
| /V1/Accounts/**AccountID**/Conversions/create | POST |

Restituisce l'ID della conversione in corso, di cui è stato eseguito il wrapper in un documento JSON. Il nome del campo è "conversionId".

#### <a name="request-body"></a>Corpo della richiesta


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Avviare la conversione usando le firme di accesso condiviso fornite
Se l'account ARR non è collegato all'account di archiviazione, questa interfaccia REST consente di fornire l'accesso tramite *firme di accesso condiviso (SAS)*.

| Endpoint | Metodo |
|-----------|:-----------|
| /V1/Accounts/**AccountID**/Conversions/createWithSharedAccessSignature | POST |

Restituisce l'ID della conversione in corso, di cui è stato eseguito il wrapper in un documento JSON. Il nome del campo è "conversionId".

#### <a name="request-body"></a>Corpo della richiesta

Il corpo della richiesta è identico a quello della precedente chiamata REST, ma l'input e l'output contengono *token di firma di accesso condiviso (SAS)*. Questi token forniscono l'accesso all'account di archiviazione per la lettura dell'input e la scrittura del risultato della conversione.

> [!NOTE]
> Questi token URI SAS sono le stringhe di query e non l'URI completo. 


```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Stato di conversione del polling
Lo stato di una conversione in corso avviata con una delle chiamate REST sopra riportate può essere sottoposto a query usando l'interfaccia seguente:


| Endpoint | Metodo |
|-----------|:-----------|
| /V1/Accounts/**AccountID**/Conversions/**conversionId** | GET |

Restituisce un documento JSON con un campo "status" che può avere i valori seguenti:

- Creato
- Esecuzione
- "Esito positivo"
- Errore

Se lo stato è "errore", sarà presente un ulteriore campo "Error" con un sottocampo "message" contenente le informazioni sull'errore. I log aggiuntivi verranno caricati nel contenitore di output.

## <a name="next-steps"></a>Passaggi successivi

- [Usare l'archiviazione BLOB di Azure per la conversione dei modelli](blob-storage.md)
- [Conversione di modelli](model-conversion.md)
