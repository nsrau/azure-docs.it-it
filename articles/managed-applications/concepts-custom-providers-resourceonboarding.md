---
title: Onboarding delle risorse del provider personalizzato di Azure
description: Informazioni su come eseguire l'onboarding delle risorse usando i provider personalizzati di Azure per applicare la gestione o la configurazione ad altri tipi di risorse di Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 65e0f795a2b0efa327aec02c01cdb3706bf91d29
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818787"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Panoramica sull'onboarding delle risorse del provider personalizzato di Azure

Il caricamento delle risorse dei provider personalizzati di Azure è un modello di estendibilità per i tipi di risorse di Azure. Consente di applicare operazioni o gestione tra le risorse di Azure esistenti su larga scala. Per altre informazioni, vedere [How Azure Custom providers can Extending Azure](./custom-providers-overview.md). L'articolo illustra:

- Il caricamento delle risorse può essere possibile.
- Nozioni fondamentali sull'onboarding delle risorse e su come usarlo.
- Dove trovare guide ed esempi di codice per iniziare.

> [!IMPORTANT]
> I provider personalizzati sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero avere funzionalità vincolate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Cosa può fare l'onboarding delle risorse?

Analogamente alle [risorse personalizzate del provider personalizzato di Azure](./custom-providers-resources-endpoint-how-to.md), il caricamento delle risorse definisce un contratto che effettuerà il proxy delle richieste di "onboarding" a un endpoint. Diversamente dalle risorse personalizzate, l'onboarding delle risorse non crea un nuovo tipo di risorsa. Consente invece l'estensione dei tipi di risorse esistenti. E il caricamento delle risorse funziona con i criteri di Azure, quindi la gestione e la configurazione delle risorse possono essere eseguite su larga scala. Alcuni esempi di flussi di lavoro di onboarding delle risorse:

- Installare e gestire le estensioni delle macchine virtuali.
- Caricare e configurare le impostazioni predefinite negli account di archiviazione di Azure.
- Abilitare le impostazioni di diagnostica di base su larga scala.

## <a name="resource-onboarding-basics"></a>Nozioni fondamentali sull'onboarding delle risorse

Per configurare il caricamento delle risorse tramite i provider personalizzati di Azure, è possibile usare i tipi di risorse Microsoft. CustomProviders/resourceProviders e Microsoft. CustomProviders/Associations. Per abilitare l'onboarding delle risorse per un provider personalizzato, durante il processo di configurazione, creare un oggetto **ResourceType** denominato "Associations" con un **routingType** che includa "extension". Microsoft. CustomProviders/Associations e Microsoft. CustomProviders/resourceProviders non devono appartenere allo stesso gruppo di risorse.

Ecco un provider personalizzato di Azure di esempio:

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
name | Sì | Nome della definizione dell'endpoint. Per l'onboarding delle risorse, il nome deve essere "Associations".
routingType | Sì | Determina il tipo di contratto con l'endpoint. Per l'onboarding delle risorse, i **routingTypes** validi sono "proxy, cache, estensione" e "webhook, cache, estensione".
endpoint | Sì | Endpoint a cui indirizzare le richieste. Questa operazione gestirà la risposta e tutti gli effetti collaterali della richiesta.

Dopo aver creato il provider personalizzato con il tipo di risorsa associazioni, è possibile usare Microsoft. CustomProviders/Associations come destinazione. Microsoft. CustomProviders/Associations è una risorsa di estensione che può estendere qualsiasi altra risorsa di Azure. Quando viene creata un'istanza di Microsoft. CustomProviders/associations, verrà accettata una proprietà **targetResourceId**, che deve essere un ID di risorsa Microsoft. CustomProviders/ResourceProviders o Microsoft. Solutions/Applications valido. In questi casi, la richiesta verrà trasmessa al tipo di risorsa associazioni nell'istanza Microsoft. CustomProviders/resourceProviders creata.

> [!NOTE]
> Se un ID di risorsa Microsoft. Solutions/Applications viene fornito come **targetResourceId**, è necessario che nel gruppo di risorse gestite sia presente un oggetto Microsoft. CustomProviders/resourceProviders distribuito con il nome "public".

Esempio di associazione di provider personalizzati di Azure:

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
targetResourceId | Sì | ID risorsa di Microsoft. CustomProviders/resourceProviders o Microsoft. Solutions/Applications.

## <a name="how-to-use-resource-onboarding"></a>Come usare l'onboarding delle risorse

Il caricamento delle risorse funziona estendendo altre risorse con la risorsa di estensione Microsoft. CustomProviders/Associations. Nell'esempio seguente la richiesta viene effettuata per una macchina virtuale, ma è possibile estendere qualsiasi risorsa.

In primo luogo, è necessario creare una risorsa di provider personalizzata con un tipo di risorsa associazioni. Verrà dichiarata l'URL di callback che verrà usato quando viene creata una risorsa Microsoft. CustomProviders/Association corrispondente, destinata al provider personalizzato.

Esempio di richiesta di creazione Microsoft. CustomProviders/resourceProviders:

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

Dopo aver creato il provider personalizzato, è possibile fare riferimento ad altre risorse e applicare gli effetti collaterali del provider personalizzato.

Richiesta di creazione di Microsoft. CustomProviders/Associations di esempio:

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

Questa richiesta verrà quindi trasmessa all'endpoint specificato nel provider personalizzato creato, a cui fa riferimento **targetResourceId** nel formato seguente:

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

L'endpoint deve rispondere con un `Content-Type` Application/JSON e un corpo della risposta JSON valido. I campi restituiti nell'oggetto **Properties** di JSON verranno aggiunti alla risposta return dell'associazione.

## <a name="getting-help"></a>Risorse della Guida

In caso di domande sullo sviluppo di provider di risorse personalizzati di Azure, provare a richiederli [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Una domanda simile potrebbe essere già stata risposta, quindi controllare prima di tutto la pubblicazione. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sui provider personalizzati. Per ulteriori informazioni, vedere questi articoli:

- [Esercitazione: onboarding delle risorse con provider personalizzati](./tutorial-custom-providers-resource-onboarding.md)
- [Esercitazione: creare azioni e risorse personalizzate in Azure](./tutorial-custom-providers-101.md)
- [Guida introduttiva: creare un provider di risorse personalizzato e distribuire risorse personalizzate](./create-custom-provider.md)
- [Procedura: aggiungere azioni personalizzate a un'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedura: aggiungere risorse personalizzate a un'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
