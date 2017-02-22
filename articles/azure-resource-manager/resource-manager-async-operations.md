---
title: Operazioni asincrone in Azure |Microsoft Docs
description: Viene descritto come tenere traccia delle operazioni asincrone in Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5590ced0e7d2a68fd525178ee21590b0897b58d7
ms.openlocfilehash: 9fe3d98cd345aae45722295b6c1b7fc3e9036e95


---
# <a name="track-asynchronous-azure-operations"></a>Tenere traccia delle operazioni asincrone
Alcune operazioni REST in Azure vengono eseguite in modo asincrono perché non è possibile completarle rapidamente. In questo articolo viene descritto come tenere traccia dello stato delle operazioni asincrone tramite i valori restituiti nella risposta.  

## <a name="status-codes-for-asynchronous-operations"></a>Codici di stato per le operazioni asincrone
Inizialmente, un'operazione asincrona restituisce un codice di stato HTTP del tipo:

* 201 (Creato) oppure
* 202 (Accettato) 

Quando l'operazione viene completata correttamente, viene restituito uno dei codici seguenti:

* 200 (OK)
* 204 (No Content (Nessun contenuto)) 

Consultare la [documentazione dell'API REST](/rest/api/) per visualizzare le risposte dell'operazione in esecuzione. 

## <a name="monitor-status-of-operation"></a>Monitorare lo stato dell'operazione
Le operazioni REST asincrone restituiscono i valori di intestazione, che consentono di determinare lo stato dell'operazione. Potenzialmente esistono tre valori di intestazione da esaminare:

* `Azure-AsyncOperation` -URL per verificare lo stato attuale dell'operazione. Se l'operazione restituisce questo valore, usarlo sempre (al posto di Location) per tenere traccia dello stato dell'operazione.
* `Location` -URL per determinare quando un'operazione è stata completata. Usare questo valore solo quando non viene restituito Azure-AsyncOperation.
* `Retry-After` -Il numero di secondi di attesa prima di controllare lo stato dell'operazione asincrona.

Tuttavia, non tutte le operazioni asincrone restituiscono tutti questi valori. Ad esempio, potrebbe essere necessario valutare il valore d'intestazione Azure-AsyncOperation per un'operazione e il valore d'intestazione Location per un'altra. 

È possibile recuperare i valori d'intestazione eseguendo le stesse operazioni necessarie per il recupero di un valore d'intestazione qualsiasi di una richiesta. Ad esempio, in C# il valore d'intestazione viene recuperato da un oggetto `HttpWebResponse` denominato `response` con il codice seguente:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Richiesta e risposta di Azure AsyncOperation

Per ottenere lo stato dell'operazione asincrona, inviare una richiesta GET all'URL nel valore d'intestazione Azure-AsyncOperation.

Il corpo della risposta di questa operazione contiene le informazioni sull'operazione. L'esempio seguente mostra i possibili valori restituiti dall'operazione:

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

Solo `status` viene restituito per tutte le risposte. L'oggetto errore viene restituito quando lo stato è Operazione non riuscita oppure Operazione annullata. Tutti gli altri valori sono facoltativi, pertanto la risposta che si riceve potrebbe presentare alcune differenze rispetto all'esempio.

## <a name="provisioningstate-values"></a>Valori provisioningState

Le operazioni di creazione, aggiornamento o eliminazione (INSERISCI, PATCH, ELIMINA) di una risorsa restituiscono in genere un valore `provisioningState`. Quando un'operazione viene completata, viene restituito uno dei tre valori seguenti: 

* Operazione riuscita
* Operazione non riuscita
* Canceled

Tutti gli altri valori indicano che l'operazione è ancora in esecuzione. Il provider di risorse può restituire un valore personalizzato che indica lo stato. Ad esempio, potrebbe essere visualizzato **Accettato** quando la richiesta è stata ricevuta ed è in esecuzione.

## <a name="example-requests-and-responses"></a>Esempi di richieste e risposte

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Avviare la macchina virtuale (202 con Azure-AsyncOperation)
In questo esempio viene illustrato come determinare lo stato dell'operazione di **avvio** per le macchine virtuali. La richiesta iniziale è nel formato seguente:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Restituisce il codice di stato 202. Tra i valori di intestazione compare:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Per controllare lo stato dell'operazione asincrona, inviare un'altra richiesta all'URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
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

In questo esempio viene illustrato come determinare lo stato dell'operazione di **distribuzione** per la distribuzione delle risorse in Azure. La richiesta iniziale è nel formato seguente:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Restituisce il codice di stato 201. Il corpo della risposta include:

```json
"provisioningState":"Accepted",
```

Tra i valori di intestazione compare:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Per controllare lo stato dell'operazione asincrona, inviare un'altra richiesta all'URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Il corpo della risposta contiene lo stato dell'operazione:

```json
{"status":"Running"}
```

Al termine della distribuzione, la risposta contiene:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Creazione di un account di archiviazione (202 con Location e Retry-After)

In questo esempio viene illustrato come determinare lo stato dell'operazione di **creazione** per gli account di archiviazione. La richiesta iniziale è nel formato seguente:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

Il corpo della richiesta contiene le proprietà dell'account di archiviazione:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Restituisce il codice di stato 202. Tra i valori di intestazione, vengono visualizzati i due valori seguenti:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Dopo aver atteso il numero di secondi specificato in Retry-After, verificare lo stato dell'operazione asincrona inviando un'altra richiesta all'URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Se la richiesta è ancora in esecuzione, viene visualizzato il codice di stato 202. Se la richiesta è stata completata, viene visualizzato il codice di stato 200 e il corpo della risposta contiene le proprietà dell'account di archiviazione che è stato creato.

## <a name="next-steps"></a>Passaggi successivi

* Per la documentazione relativa a ogni operazione REST, consultare la [documentazione dell'API REST](/rest/api/).
* Per informazioni sulla gestione delle risorse tramite Gestione risorse dell'API REST, consultare [Using the Resource Manager REST API](resource-manager-rest-api.md) (Uso di Gestione risorse dell'API REST).
* Per informazioni sui modelli di distribuzione tramite la Gestione risorse dell'API REST, vedere [Distribuire le risorse con i modelli e l'API REST di Gestione risorse](resource-group-template-deploy-rest.md).


<!--HONumber=Jan17_HO2-->


