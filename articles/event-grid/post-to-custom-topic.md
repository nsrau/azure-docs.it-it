---
title: Pubblicare gli eventi in argomenti Griglia di eventi di Azure personalizzati
description: Descrive come pubblicare un evento in un argomento personalizzato per la Griglia di eventi di Azure
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 43dcdf9ab0fee5f7e61ecdc42aaf40430e272d92
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
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

Per una descrizione di ogni proprietà, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

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

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione al routing degli eventi personalizzati, vedere [Creare e instradare eventi personalizzati con l'interfaccia della riga di comando di Azure e Griglia di eventi](custom-event-quickstart.md) o [Creare e instradare eventi personalizzati con Azure PowerShell e Griglia di eventi](custom-event-quickstart-powershell.md).
* Per altre informazioni sulla chiave di autenticazione, vedere [Sicurezza e autenticazione di Griglia di eventi](security-authentication.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
