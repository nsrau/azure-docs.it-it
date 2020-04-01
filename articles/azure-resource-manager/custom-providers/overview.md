---
title: Panoramica dei provider personalizzati
description: Informazioni sui provider di risorse personalizzate di Azure e su come estendere il piano dell'API di Azure per adattarlo ai flussi di lavoro.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398467"
---
# <a name="azure-custom-resource-providers-overview"></a>Panoramica dei provider di risorse personalizzate di AzureAzure Custom Resource Providers Overview

Azure Custom Resource Providers is an extensibility platform to Azure. Consente di definire API personalizzate che possono essere usate per arricchire l'esperienza predefinita di Azure.It allows you to define custom APIs that can be used to enrich the default Azure experience. Questa documentazione descrive:

- Come creare e distribuire un provider di risorse personalizzato di Azure.How to build and deploy an Azure Custom Resource Provider.
- Come usare i provider di risorse personalizzate di Azure per estendere i flussi di lavoro esistenti.
- Dove trovare guide ed esempi di codice per iniziare.

![Panoramica sui provider personalizzati](./media/overview/overview.png)

> [!IMPORTANT]
> Provider personalizzati è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Operazioni che i provider di risorse personalizzate possono eseguire

Ecco alcuni esempi di ciò che è possibile ottenere con i provider di risorse personalizzate di Azure:Here are some examples of what you can achieve with Azure Custom Resource Providers:

- Estendere l'API REST di Azure Resource Manager per includere servizi interni ed esterni.
- Abilitare scenari personalizzati in cima ai flussi di lavoro di Azure esistenti.
- Personalizzare il controllo e l'effetto dei modelli di Azure Resource Manager.Customize Azure Resource Manager Templates control and effect.

## <a name="what-is-a-custom-resource-provider"></a>Che cos'è un provider di risorse personalizzato

I provider di risorse personalizzate di Azure vengono create creando un contratto tra Azure e un endpoint. Questo contratto definisce un elenco di nuove risorse e azioni tramite una nuova risorsa, **Microsoft.CustomProviders/resourceProviders**. Il provider di risorse personalizzato esporrà quindi queste nuove API in Azure.The custom resource provider will then expose these new APIs in Azure. I provider di risorse personalizzate di Azure sono composti da tre parti: provider di risorse personalizzato, **endpoint**e risorse personalizzate.

## <a name="how-to-build-custom-resource-providers"></a>Come creare provider di risorse personalizzatiHow to build custom resource providers

I provider di risorse personalizzati sono un elenco di contratti tra Azure ed endpoint. Questo contratto descrive come Azure deve interagire con un endpoint. Il provider di risorse agisce come un proxy e inoltrerà richieste e risposte da e verso **l'endpoint**specificato. Un provider di risorse può specificare due tipi di contratti: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) e [**actions**](./custom-providers-action-endpoint-how-to.md). Questi sono abilitati tramite definizioni di endpoint. Una definizione di endpoint è costituita da tre campi: **nome**, **routingType**ed **endpoint**.

Endpoint di esempio:Sample Endpoint:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Proprietà | Obbligatoria | Descrizione
---|---|---
name | *Sì* | Nome della definizione dell'endpoint. Azure esporrà tale nome tramite l'API in '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *No* | Determina il tipo di contratto con **l'endpoint.** Se non viene specificato, per impostazione predefinita sarà "Proxy".
endpoint | *Sì* | Endpoint a cui indirizzare le richieste. Gestirà la risposta, nonché eventuali effetti collaterali della richiesta.

### <a name="building-custom-resources"></a>Creazione di risorse personalizzate

**ResourceTypes** descrivono le nuove risorse personalizzate che vengono aggiunte ad Azure.ResourceTypes describe new custom resources that are added to Azure. Questi espongono i metodi RESTful CRUD di base. Altre [informazioni sulla creazione di risorse personalizzate](./custom-providers-resources-endpoint-how-to.md)

Provider di risorse personalizzato di esempio con **resourceTypes:Sample**Custom Resource Provider with resourceTypes :

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API aggiunte ad Azure per l'esempio precedente:APIs added to Azure for the above sample:

HttpMethod | URI di esempio | Descrizione
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/'customResourceName'?api-version'2018-09-01-preview | Chiamata all'API REST di Azure per creare una nuova risorsa.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/'customResourceName'?api-version'2018-09-01-preview | Chiamata all'API REST di Azure per eliminare una risorsa esistente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/'customResourceName'?api-version'2018-09-01-preview | Chiamata all'API REST di Azure per recuperare una risorsa esistente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version-2018-09-01-preview | Chiamata all'API REST di Azure per recuperare l'elenco delle risorse esistenti.

### <a name="building-custom-actions"></a>Creazione di azioni personalizzate

**Le azioni** descrivono le nuove azioni aggiunte ad Azure.Actions describe new actions that are added to Azure. Questi possono essere esposti sopra il provider di risorse o annidati in un **resourceType**. Ulteriori [informazioni sulla creazione di azioni personalizzate](./custom-providers-action-endpoint-how-to.md)

Provider di risorse personalizzato di esempio con azioni:Sample Custom Resource Provider with **actions:**

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API aggiunte ad Azure per l'esempio precedente:APIs added to Azure for the above sample:

HttpMethod | URI di esempio | Descrizione
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version-2018-09-01-anteprima | Chiamata dell'API REST di Azure per attivare l'azione.

## <a name="looking-for-help"></a>Assistenza

In caso di domande per lo sviluppo di provider di risorse personalizzato di Azure, provare a porre [sull'overflow dello stack](https://stackoverflow.com/questions/tagged/azure-custom-providers). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di pubblicarle. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sui provider personalizzati. Passare all'articolo successivo per creare un provider personalizzato.

- [Guida introduttiva: Creare il provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: Creare azioni e risorse personalizzate in AzureTutorial: Create custom actions and resources in Azure](./tutorial-get-started-with-custom-providers.md)
- [Procedura: Aggiunta di azioni personalizzate all'API REST di AzureHow To: Adding Custom Actions to Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedura: Aggiunta di risorse personalizzate all'API REST di AzureHow To: Adding Custom Resources to Azure REST API](./custom-providers-resources-endpoint-how-to.md)
