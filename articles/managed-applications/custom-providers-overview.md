---
title: Panoramica dell'anteprima di Azure provider personalizzati
description: Vengono descritti i concetti per la creazione di un provider di risorse personalizzato con Azure Resource Manager
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159856"
---
# <a name="azure-custom-providers-preview-overview"></a>Panoramica di anteprima di provider personalizzato di Azure

Con i provider personalizzati di Azure, è possibile estendere Azure per funzionare con il servizio. Creare il proprio provider di risorse, inclusi i tipi di risorsa personalizzata e le azioni. Il provider personalizzato è integrato con Azure Resource Manager. È possibile usare le funzionalità del gestore delle risorse, ad esempio le distribuzioni di modelli e controllo degli accessi in base al ruolo, distribuire e proteggere il servizio.

Questo articolo offre una panoramica dei provider personalizzati e le relative funzionalità. L'immagine seguente mostra il flusso di lavoro per le risorse e le azioni definite in un provider personalizzato.

![Cenni preliminari sul provider personalizzati](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Provider personalizzati è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="define-your-custom-provider"></a>Definire il provider personalizzato

Per iniziare, lasciare che Azure Resource Manager a conoscenza del provider personalizzato. Distribuire in Azure una risorsa provider personalizzato, che usa il tipo di risorsa **Microsoft.CustomProviders/resourceProviders**. In tale risorsa, si definiscono le risorse e le azioni per il servizio.

Ad esempio, se il servizio richiede un tipo di risorsa denominato **utenti**, si include di quel tipo di risorsa nella definizione del provider personalizzato. Per ogni tipo di risorsa, si forniscono un endpoint che offre le operazioni REST (PUT, GET, DELETE) per tale tipo di risorsa. L'endpoint può essere ospitata in qualsiasi ambiente e contiene la logica per la modalità del servizio gestisce le operazioni sul tipo di risorsa.

È anche possibile definire le azioni personalizzate per il provider di risorse. Azioni rappresentano operazioni POST. Usare azioni per operazioni quali avvio, arresto o riavvio. È fornire un endpoint che gestisce la richiesta.

Nell'esempio seguente viene illustrato come definire un provider personalizzato con un'azione e un tipo di risorsa.

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

Per la **routingType**, i valori accettati sono `Proxy` e `Cache`. Proxy significa che le richieste per il tipo di risorsa o azione vengono gestite dall'endpoint. L'impostazione della cache è supportata solo per tipi di risorsa, non azioni. Per specificare la cache, è necessario specificare anche proxy. Cache indica le risposte dall'endpoint vengono archiviate per ottimizzare le operazioni di lettura. Usando l'impostazione della cache rende più semplice implementare un'API coerenti e conformi con altri servizi di Resource Manager.

## <a name="deploy-your-resource-types"></a>Distribuire i tipi di risorse

Dopo aver definito il provider personalizzato, è possibile distribuire i tipi di risorsa personalizzata. Nell'esempio seguente viene illustrato il codice JSON che includono nel modello per distribuire il tipo di risorsa per il provider personalizzato. Questo tipo di risorsa può essere distribuito nello stesso modello con altre risorse di Azure.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>Gestire l'accesso

Uso di Azure [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) per gestire l'accesso al provider di risorse. È possibile assegnare [ruoli predefiniti](../role-based-access-control/built-in-roles.md) , ad esempio proprietario, collaboratore o lettore agli utenti. In alternativa, è possibile definire [ruoli personalizzati](../role-based-access-control/custom-roles.md) specifici per le operazioni nel provider di risorse.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si appreso come provider personalizzati. Passare all'articolo successivo per creare un provider personalizzato.

> [!div class="nextstepaction"]
> [Esercitazione: Creare provider personalizzati e distribuire le risorse personalizzate](create-custom-provider.md)
