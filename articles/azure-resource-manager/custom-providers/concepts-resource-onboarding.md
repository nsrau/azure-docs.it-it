---
title: Onboarding delle risorse
description: Informazioni sull'esecuzione dell'onboarding delle risorse tramite I provider personalizzati di Azure per applicare la gestione o la configurazione ad altri tipi di risorse di Azure.Learn about performing resource onboarding by using Azure Custom Providers to apply management or configuration to other Azure resource types.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650409"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Panoramica dell'onboarding delle risorse dei provider personalizzati di AzureAzure Custom Providers resource onboarding overview

L'onboarding delle risorse dei provider personalizzati di Azure è un modello di estensibilità per i tipi di risorse di Azure.Azure Custom Providers resource onboarding is an extensibility model for Azure resource types. Consente di applicare operazioni o gestione tra le risorse di Azure esistenti su larga scala. Per altre informazioni, vedere Come i provider personalizzati di Azure possono estendere Azure.For more information, see [How Azure Custom Providers can extend Azure.](overview.md) L'articolo illustra:

- Cosa può fare l'onboarding delle risorse.
- Nozioni di base sull'onboarding delle risorse e come usarlo.
- Dove trovare guide ed esempi di codice per iniziare.

> [!IMPORTANT]
> Provider personalizzati è attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliabile per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o avere funzionalità vincolate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Cosa può fare l'onboarding delle risorse?

Analogamente alle risorse personalizzate dei provider personalizzati di [Azure,](./custom-providers-resources-endpoint-how-to.md)l'onboarding delle risorse definisce un contratto che proxyrà le richieste di "onboarding" a un endpoint. A differenza delle risorse personalizzate, l'onboarding delle risorse non crea un nuovo tipo di risorsa. Al contrario, consente l'estensione dei tipi di risorse esistenti. Inoltre, l'onboarding delle risorse funziona con Criteri di Azure, pertanto la gestione e la configurazione delle risorse possono essere eseguite su larga scala. Alcuni esempi di flussi di lavoro di onboarding delle risorse:Some examples of resource onboarding workflows:

- Installare e gestire le estensioni delle macchine virtuali.
- Caricare e configurare le impostazioni predefinite in Account di archiviazione di Azure.Upload and configure defaults on Azure storage accounts.
- Abilitare le impostazioni di diagnostica di base su larga scala.

## <a name="resource-onboarding-basics"></a>Nozioni di base sull'onboarding delle risorse

Configurare l'onboarding delle risorse tramite i provider personalizzati di Azure utilizzando Microsoft.CustomProviders/resourceProviders e Microsoft.CustomProviders/associations tipi di risorse. Per abilitare l'onboarding delle risorse per un provider personalizzato, durante il processo di configurazione, creare un **resourceType** denominato "associations" con un **routingType** che include "Extension". Microsoft.CustomProviders/associations e Microsoft.CustomProviders/resourceProviders non devono appartenere allo stesso gruppo di risorse.

Ecco un provider personalizzato di Azure di esempio:Here's a sample Azure custom provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Proprietà | Obbligatorio? | Descrizione
---|---|---
name | Sì | Nome della definizione dell'endpoint. Per l'onboarding delle risorse, il nome deve essere "associations".
routingType | Sì | Determina il tipo di contratto con l'endpoint. Per l'onboarding delle risorse, i **routingType** validi sono "Proxy,Cache,Extension" e "Webhook,Cache,Extension".
endpoint | Sì | Endpoint a cui indirizzare le richieste. Questo gestirà la risposta e gli eventuali effetti collaterali della richiesta.

Dopo aver creato il provider personalizzato con il tipo di risorsa associazioni, è possibile utilizzare Microsoft.CustomProviders/associations. Microsoft.CustomProviders/associations è una risorsa di estensione che può estendere qualsiasi altra risorsa di Azure.Microsoft.CustomProviders/associations is an extension resource that can extend any other Azure resource. Quando viene creata un'istanza di Microsoft.CustomProviders/associations, verrà accettata una proprietà **targetResourceId**, che deve essere un ID di risorsa Microsoft.CustomProviders/resourceProviders valido o Microsoft.Solutions/applications. In questi casi, la richiesta verrà inoltrata al tipo di risorsa associazioni nell'istanza Microsoft.CustomProviders/resourceProviders creata.

> [!NOTE]
> Se viene fornito un ID risorsa Microsoft.Solutions/applications come **targetResourceId**, deve essere presente un Microsoft.CustomProviders/resourceProviders distribuito nel gruppo di risorse gestite con il nome "public".

Associazione di provider personalizzati di Azure di esempio:Sample Azure Custom Providers association:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Proprietà | Obbligatorio? | Descrizione
---|---|---
targetResourceId | Sì | ID di risorsa di Microsoft.CustomProviders/resourceProviders o Microsoft.Solutions/applications.

## <a name="how-to-use-resource-onboarding"></a>Come usare l'onboarding delle risorseHow to use resource onboarding

L'onboarding delle risorse funziona estendendo altre risorse con la risorsa di estensione Microsoft.CustomProviders/associations.Resource onboarding works by extending other resources with the Microsoft.CustomProviders/associations extension resource. Nell'esempio seguente viene effettuata la richiesta per una macchina virtuale, ma qualsiasi risorsa può essere estesa.

In primo luogo, è necessario creare una risorsa provider personalizzata con un tipo di risorsa di associazione. Verrà dichiarato l'URL di callback che verrà utilizzato quando viene creata una risorsa Microsoft.CustomProviders/associations corrispondente, destinata al provider personalizzato.

Esempio Microsoft.CustomProviders/resourceProviders creare richiesta:Sample Microsoft.CustomProviders/resourceProviders create request:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Dopo aver creato il provider personalizzato, è possibile scegliere come destinazione altre risorse e applicarvi gli effetti collaterali del provider personalizzato.

Esempio Microsoft.CustomProviders/associations creare richiesta:Sample Microsoft.CustomProviders/associations create request:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Questa richiesta verrà quindi inoltrata all'endpoint specificato nel provider personalizzato creato, a cui fa riferimento targetResourceId nel formato seguente:This request will then be forwarded to the endpoint specified in the custom provider you created, which is referenced by the **targetResourceId** in this form:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

L'endpoint deve rispondere con `Content-Type` un'applicazione/json e un corpo di risposta JSON valido. I campi restituiti nell'oggetto **proprietà** di JSON verranno aggiunti alla risposta di restituzione dell'associazione.

## <a name="getting-help"></a>Risorse della Guida

In caso di domande sullo sviluppo di provider di risorse personalizzati di Azure, provare a chiedere loro [sull'overflow dello stack](https://stackoverflow.com/questions/tagged/azure-custom-providers). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di inserirle. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sui provider personalizzati. Per ulteriori informazioni, consulta questi articoli:

- [Esercitazione: Onboarding delle risorse con provider personalizzatiTutorial: Resource onboarding with custom providers](./tutorial-resource-onboarding.md)
- [Esercitazione: Creare azioni e risorse personalizzate in AzureTutorial: Create custom actions and resources in Azure](./tutorial-get-started-with-custom-providers.md)
- [Guida introduttiva: Creare un provider di risorse personalizzato e distribuire risorse personalizzate](./create-custom-provider.md)
- [Procedura: Aggiungere azioni personalizzate a un'API REST di AzureHow to: Adding custom actions to an Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedura: Aggiungere risorse personalizzate a un'API REST di AzureHow to: Adding custom resources to an Azure REST API](./custom-providers-resources-endpoint-how-to.md)
