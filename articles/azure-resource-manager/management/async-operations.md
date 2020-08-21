---
title: Stato delle operazioni asincrone
description: Viene descritto come tenere traccia delle operazioni asincrone in Azure. Mostra i valori usati per ottenere lo stato di un'operazione a esecuzione prolungata.
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: e2c5ba137d5277466cf1b382d2b0b1bc02259f00
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723453"
---
# <a name="track-asynchronous-azure-operations"></a>Tenere traccia delle operazioni asincrone

Alcune operazioni REST in Azure vengono eseguite in modo asincrono perché non è possibile completarle rapidamente. Questo articolo descrive come tenere traccia dello stato delle operazioni asincrone tramite i valori restituiti nella risposta.  

## <a name="status-codes-for-asynchronous-operations"></a>Codici di stato per le operazioni asincrone

Inizialmente, un'operazione asincrona restituisce un codice di stato HTTP del tipo:

* 201 (Creato) oppure
* 202 (Accettato)

Quando l'operazione viene completata correttamente, viene restituito uno dei codici seguenti:

* 200 (OK)
* 204 (No Content (Nessun contenuto))

Consultare la [documentazione dell'API REST](/rest/api/azure/) per visualizzare le risposte dell'operazione in esecuzione.

Dopo aver ricevuto il codice di risposta 201 o 202, si è pronti per monitorare lo stato dell'operazione.

## <a name="url-to-monitor-status"></a>URL per il monitoraggio dello stato

Esistono due modi diversi per monitorare lo stato dell'operazione asincrona. Per determinare l'approccio corretto, esaminare i valori dell'intestazione restituiti dalla richiesta originale. Per prima cosa, cercare:

* `Azure-AsyncOperation` -URL per verificare lo stato attuale dell'operazione. Se l'operazione restituisce questo valore, usarlo per tenere traccia dello stato dell'operazione.
* `Retry-After` -Il numero di secondi di attesa prima di controllare lo stato dell'operazione asincrona.

Se `Azure-AsyncOperation` non è uno dei valori di intestazione, cercare:

* `Location` -URL per determinare quando un'operazione è stata completata. Usare questo valore solo quando Azure-AsyncOperation non viene restituito.
* `Retry-After` -Il numero di secondi di attesa prima di controllare lo stato dell'operazione asincrona.

## <a name="azure-asyncoperation-request-and-response"></a>Richiesta e risposta di Azure AsyncOperation

Se si dispone di un URL dal `Azure-AsyncOperation` valore dell'intestazione, inviare una richiesta Get a tale URL. Usare il valore di `Retry-After` per pianificare la frequenza di controllo dello stato. Si otterrà un oggetto risposta che indica lo stato dell'operazione. Viene restituita una risposta diversa quando si verifica lo stato dell'operazione con l' `Location` URL. Per ulteriori informazioni sulla risposta da un URL località, vedere [creare un account di archiviazione (202 con percorso e riprovare-dopo)](#create-storage-account-202-with-location-and-retry-after).

Le proprietà della risposta possono variare, ma includono sempre lo stato dell'operazione asincrona.

```json
{
    "status": "{status-value}"
}
```

Nell'esempio seguente vengono illustrati altri valori che potrebbero essere restituiti dall'operazione:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}",
    "status" : "Succeeded | Failed | Canceled | {resource provider values}",
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : {
        "code": "{error code}",  
        "message": "{error description}"
    }
}
```

L'oggetto errore viene restituito quando lo stato è Operazione non riuscita oppure Operazione annullata. Tutti gli altri valori sono facoltativi. La risposta ricevuta potrebbe essere diversa dall'esempio.

## <a name="provisioningstate-values"></a>Valori provisioningState

Le operazioni di creazione, aggiornamento o eliminazione (INSERISCI, PATCH, ELIMINA) di una risorsa restituiscono in genere un valore `provisioningState`. Quando un'operazione viene completata, viene restituito uno dei tre valori seguenti:

* Completato
* Non riuscito
* Cancellati

Tutti gli altri valori indicano che l'operazione è ancora in esecuzione. Il provider di risorse può restituire un valore personalizzato che indica lo stato. Ad esempio, potrebbe essere visualizzato **Accettato** quando la richiesta è stata ricevuta ed è in esecuzione.

## <a name="example-requests-and-responses"></a>Esempi di richieste e risposte

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Avviare la macchina virtuale (202 con Azure-AsyncOperation)

Questo esempio illustra come determinare lo stato dell' [operazione di avvio per le macchine virtuali](/rest/api/compute/virtualmachines/start). La richiesta iniziale è nel formato seguente:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2019-12-01
```

Restituisce il codice di stato 202. Tra i valori di intestazione compare:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Per controllare lo stato dell'operazione asincrona, inviare un'altra richiesta all'URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2019-12-01
```

Il corpo della risposta contiene lo stato dell'operazione:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Distribuzione delle risorse (201 con Azure-AsyncOperation)

Questo esempio illustra come determinare lo stato dell' [operazione di distribuzione per la distribuzione di risorse](/rest/api/resources/deployments/createorupdate) in Azure. La richiesta iniziale è nel formato seguente:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2020-06-01
```

Restituisce il codice di stato 201. Il corpo della risposta include:

```json
"provisioningState":"Accepted",
```

Tra i valori di intestazione compare:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Per controllare lo stato dell'operazione asincrona, inviare un'altra richiesta all'URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2020-06-01
```

Il corpo della risposta contiene lo stato dell'operazione:

```json
{
    "status": "Running"
}
```

Al termine della distribuzione, la risposta contiene:

```json
{
    "status": "Succeeded"
}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Creazione di un account di archiviazione (202 con Location e Retry-After)

Questo esempio illustra come determinare lo stato dell'operazione di [creazione per gli account di archiviazione](/rest/api/storagerp/storageaccounts/create). La richiesta iniziale è nel formato seguente:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2019-06-01
```

Il corpo della richiesta contiene le proprietà dell'account di archiviazione:

```json
{
    "location": "South Central US",
    "properties": {},
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Restituisce il codice di stato 202. Tra i valori di intestazione, vengono visualizzati i due valori seguenti:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
Retry-After: 17
```

Dopo aver atteso il numero di secondi specificato in Retry-After, verificare lo stato dell'operazione asincrona inviando un'altra richiesta all'URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2019-06-01
```

Se la richiesta è ancora in esecuzione, viene visualizzato il codice di stato 202. Se la richiesta è stata completata, viene visualizzato il codice di stato 200 e il corpo della risposta contiene le proprietà dell'account di archiviazione che è stato creato.

## <a name="next-steps"></a>Passaggi successivi

* Per la documentazione relativa a ogni operazione REST, consultare la [documentazione dell'API REST](/rest/api/azure/).
* Per informazioni sulla distribuzione di modelli tramite l'API REST di Gestione risorse, vedere [distribuire risorse con modelli di gestione risorse e gestione risorse API REST](../templates/deploy-rest.md).