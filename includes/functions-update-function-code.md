---
title: File di inclusione
description: File di inclusione
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987999"
---
## <a name="update-the-function"></a>Aggiornare la funzione

Per impostazione predefinita, il modello crea una funzione che richiede una chiave di funzione quando si effettuano richieste. Per rendere più semplice testare la funzione in Azure, è necessario aggiornare la funzione per consentire l'accesso anonimo. La modalità con cui si apporta questa modifica dipende dal linguaggio del progetto delle funzioni.

### <a name="c"></a>C\#

Aprire il file con codice MyHttpTrigger.cs che è la nuova funzione e aggiornare l'attributo **AuthorizationLevel** nella definizione di funzione con un valore di `anonymous` e salvare le modifiche.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Aprire il file function.json per la nuova funzione, aprirlo in un editor di testo, aggiornare la proprietà **authLevel** in **bindings.httpTrigger** su `anonymous` e salvare le modifiche.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

A questo punto è possibile chiamare la funzione in Azure senza la necessità di fornire la chiave della funzione. La chiave della funzione non è mai necessaria durante l'esecuzione in locale.
