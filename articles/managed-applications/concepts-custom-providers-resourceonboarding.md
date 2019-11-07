---
title: Onboarding delle risorse del provider personalizzato di Azure
description: Informazioni sull'onboarding delle risorse con i provider personalizzati di Azure per applicare la gestione o la configurazione ad altri tipi di risorse di Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609151"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Panoramica sull'onboarding delle risorse del provider personalizzato di Azure

Il caricamento delle risorse dei provider personalizzati di Azure è un modello di estendibilità per i tipi di risorse di Azure. Consente di applicare operazioni o gestione tra le risorse di Azure esistenti su larga scala. Per altre informazioni, vedere [How Azure Custom providers can Extending Azure](./custom-providers-overview.md). Questa documentazione descrive:

- Cosa può fare l'onboarding delle risorse.
- Nozioni fondamentali sull'onboarding delle risorse e su come usarlo.
- Dove trovare guide ed esempi di codice per iniziare.

> [!IMPORTANT]
> I provider personalizzati sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-resource-onboarding-do"></a>Cosa può fare l'onboarding delle risorse

Analogamente alle [risorse personalizzate del provider personalizzato di Azure](./custom-providers-resources-endpoint-how-to.md), il caricamento delle risorse definisce un contratto che effettuerà il proxy delle richieste di onboarding a un endpoint. Diversamente dalle risorse personalizzate, l'onboarding delle risorse non crea un nuovo tipo di risorsa, ma consente l'estensione dei tipi di risorse esistenti. Inoltre, il caricamento delle risorse funziona con i criteri di Azure, quindi la gestione e la configurazione delle risorse possono essere eseguite su larga scala. Alcuni esempi di flussi di lavoro di onboarding delle risorse:

- Installare e gestire le estensioni delle macchine virtuali.
- Caricare e configurare le impostazioni predefinite negli account di archiviazione di Azure.
- Abilitare le impostazioni di diagnostica di base su larga scala.

## <a name="resource-onboarding-basics"></a>Nozioni fondamentali sull'onboarding delle risorse

Il caricamento delle risorse viene configurato tramite i provider personalizzati di Azure usando i tipi di risorse "Microsoft. CustomProviders/resourceProviders" e "Microsoft. CustomProviders/Associations". Per abilitare l'onboarding delle risorse per un provider personalizzato, durante il processo di configurazione creare un oggetto **ResourceType** denominato "Associations" con un **routingType** che includa "extension". Inoltre, non è necessario che "Microsoft. CustomProviders/Associations" e "Microsoft. CustomProviders/resourceProviders" appartengano allo stesso gruppo di risorse.

Esempio di provider personalizzato di Azure:

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

Proprietà | Obbligatorio | Descrizione
---|---|---
name | *sì* | Nome della definizione dell'endpoint. Per l'onboarding delle risorse il nome deve essere "Associations".
routingType | *sì* | Determina il tipo di contratto con l' **endpoint**. Per l'onboarding delle risorse, i **routingTypes** validi sono "proxy, cache, estensione" e "webhook, cache, estensione".
endpoint | *sì* | Endpoint a cui indirizzare le richieste. Gestirà la risposta, nonché eventuali effetti collaterali della richiesta.

Una volta creato il provider personalizzato con il tipo di risorsa "Associations", è possibile usare "Microsoft. CustomProviders/Associations" come destinazione. "Microsoft. CustomProviders/Associations" è una risorsa di estensione che può estendere qualsiasi altra risorsa di Azure. Quando viene creata un'istanza di "Microsoft. CustomProviders/Associations", viene accettata una proprietà **targetResourceId**, che deve essere un ID di risorsa "Microsoft. CustomProviders/resourceProviders" o "Microsoft. Solutions/Applications" valido. In questi casi, la richiesta verrà trasmessa al tipo di risorsa "Associations" nell'istanza "Microsoft. CustomProviders/resourceProviders" creata.

> [!Note]
> Se come **targetResourceId**viene specificato un ID di risorsa "Microsoft. Solutions/Applications", deve essere presente un oggetto "Microsoft. CustomProviders/resourceProviders" distribuito nel gruppo di risorse gestite con il nome "public".

Esempio di associazione del provider personalizzato di Azure:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Proprietà | Obbligatorio | Descrizione
---|---|---
targetResourceId | *sì* | ID risorsa di "Microsoft. CustomProviders/resourceProviders" o "Microsoft. Solutions/Applications".

## <a name="how-to-use-resource-onboarding"></a>Come usare l'onboarding delle risorse

L'onboarding delle risorse funziona estendendo altre risorse con la risorsa di estensione "Microsoft. CustomProviders/Associations". Nell'esempio seguente, la richiesta verrà eseguita per una macchina virtuale, ma è possibile estendere qualsiasi risorsa.

Prima di tutto è necessario creare una risorsa di provider personalizzata con un tipo di risorsa "Associations". Verrà dichiarata l'URL di callback che verrà usato quando viene creata una risorsa "Microsoft. CustomProviders/Associations" corrispondente, destinata al provider personalizzato.

Esempio di richiesta di creazione di "Microsoft. CustomProviders/resourceProviders":

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

Dopo la creazione del provider personalizzato, ora è possibile fare riferimento ad altre risorse e applicare gli effetti collaterali del provider personalizzato.

Esempio di richiesta di creazione di "Microsoft. CustomProviders/Associations":

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

Questa richiesta verrà quindi trasmessa all'endpoint specificato nel provider personalizzato creato inizialmente, a cui fa riferimento "targetResourceId" nel formato:

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

L'endpoint deve rispondere con "application/json" `Content-Type` e il corpo della risposta JSON valido. I campi restituiti nell'oggetto "Properties" di JSON verranno aggiunti alla risposta di restituzione dell'associazione.

## <a name="looking-for-help"></a>Assistenza

Per domande per lo sviluppo di provider di risorse personalizzati di Azure, provare a chiedere [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di pubblicarle. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sui provider personalizzati. Passare all'articolo successivo per creare un provider personalizzato.

- [Esercitazione: onboarding delle risorse con provider personalizzati](./tutorial-custom-providers-resource-onboarding.md)
- [Esercitazione: creare azioni e risorse personalizzate in Azure](./tutorial-custom-providers-101.md)
- [Guida introduttiva: creare un provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Procedura: aggiungere azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedura: aggiungere risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
