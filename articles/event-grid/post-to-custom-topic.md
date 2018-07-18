---
title: Pubblicare gli eventi in argomenti Griglia di eventi di Azure personalizzati
description: Descrive come pubblicare un evento in un argomento personalizzato per la Griglia di eventi di Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: tomfitz
ms.openlocfilehash: e4256de1d9112d785b6d1cd52067fc99144a0a04
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303335"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Pubblicare in un argomento personalizzato per la Griglia di eventi di Azure

Questo articolo descrive come pubblicare un evento in un argomento personalizzato. Illustra il formato dei dati post ed evento. The [Contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) si applica solo ai post che corrispondono al formato richiesto.

## <a name="endpoint"></a>Endpoint

Quando si invia il POST HTTP a un argomento personalizzato, usare il formato URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Ad esempio, `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` è un URI valido.

Per ottenere l'endpoint per un argomento personalizzato con l'interfaccia della riga di comando di Azure, usare:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Per ottenere l'endpoint per un argomento personalizzato con Azure PowerShell, usare:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Intestazione

Nella richiesta includere un valore intestazione denominato `aeg-sas-key` che contiene una chiave per l'autenticazione.

Ad esempio, `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==` è un valore intestazione valido.

Per ottenere la chiave per un argomento personalizzato con l'interfaccia della riga di comando di Azure, usare:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Per ottenere la chiave per un argomento personalizzato con Azure PowerShell, usare:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dati dell'evento

Per gli argomenti personalizzati, i dati di livello principale contengono gli stessi campi degli eventi standard definiti dalle risorse. Una di queste proprietà è una proprietà dei dati che contiene le proprietà specifiche dell'argomento personalizzato. L'autore dell'evento stabilisce le proprietà dell'oggetto dati. Usare il seguente schema:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Per una descrizione di ogni proprietà, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). Durante la pubblicazione degli eventi in un argomento della griglia di eventi, le dimensioni totali della matrice possono raggiungere 1 MB. Per ogni evento nella matrice il limite è 64 KB.

Ad esempio, uno schema di dati evento valido è:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Risposta

Dopo la pubblicazione nell'endpoint dell'argomento, si riceve una risposta. La risposta è un codice di risposta HTTP standard. Alcune risposte comuni sono:

|Risultato  |Risposta  |
|---------|---------|
|Success  | 200 - OK  |
|I dati di evento hanno un formato non corretto | 400 - Richiesta non valida |
|Chiave di accesso non valida | 401 - Non autorizzato |
|Endpoint non corretto | 404 - Non trovato |
|Una matrice o un evento supera i limiti delle dimensioni | 413 Payload Too Large (413 Payload troppo grande) |

Per gli errori, il corpo del messaggio ha il formato seguente:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul monitoraggio dei recapiti degli eventi, vedere [Monitorare il recapito dei messaggi di Griglia di eventi di Azure](monitor-event-delivery.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
