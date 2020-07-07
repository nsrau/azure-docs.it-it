---
title: Panoramica dei provider personalizzati
description: Informazioni sui provider di risorse personalizzati di Azure e su come estendere il piano API di Azure per adattarsi ai flussi di lavoro.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 68b8bd187d58cd71778b8a922684cc3817a0715d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80398467"
---
# <a name="azure-custom-resource-providers-overview"></a>Panoramica di provider di risorse personalizzati di Azure

Provider di risorse personalizzati di Azure è una piattaforma di estendibilità di Azure. Consente di definire API personalizzate che possono essere usate per migliorare l'esperienza predefinita di Azure. Questa documentazione descrive:

- Come compilare e distribuire un provider di risorse personalizzato di Azure.
- Come usare i provider di risorse personalizzati di Azure per estendere i flussi di lavoro esistenti.
- Dove trovare guide ed esempi di codice per iniziare.

![Panoramica sui provider personalizzati](./media/overview/overview.png)

> [!IMPORTANT]
> I provider personalizzati sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Cosa possono fare i provider di risorse personalizzati

Di seguito sono riportati alcuni esempi di ciò che è possibile ottenere con i provider di risorse personalizzati di Azure:

- Estendere Azure Resource Manager API REST per includere servizi interni ed esterni.
- Abilita scenari personalizzati in base ai flussi di lavoro di Azure esistenti.
- Personalizzare Azure Resource Manager il controllo e l'effetto sui modelli.

## <a name="what-is-a-custom-resource-provider"></a>Che cos'è un provider di risorse personalizzato

I provider di risorse personalizzati di Azure vengono creati creando un contratto tra Azure e un endpoint. Questo contratto definisce un elenco di nuove risorse e azioni tramite una nuova risorsa, **Microsoft. CustomProviders/resourceProviders**. Il provider di risorse personalizzato esporrà quindi queste nuove API in Azure. I provider di risorse personalizzati di Azure sono costituiti da tre parti: provider di risorse personalizzato, **endpoint**e risorse personalizzate.

## <a name="how-to-build-custom-resource-providers"></a>Come compilare provider di risorse personalizzati

I provider di risorse personalizzati sono un elenco di contratti tra Azure ed endpoint. Questo contratto descrive il modo in cui Azure deve interagire con un endpoint. Il provider di risorse funge da proxy e trasmette le richieste e le risposte da e verso l' **endpoint**specificato. Un provider di risorse può specificare due tipi di contratti: [**resourceTypes**](./custom-providers-resources-endpoint-how-to.md) e [**Actions**](./custom-providers-action-endpoint-how-to.md). Queste sono abilitate tramite le definizioni degli endpoint. Una definizione di endpoint è costituita da tre campi: **Name**, **routingType**e **endpoint**.

Endpoint di esempio:

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
routingType | *No* | Determina il tipo di contratto con l' **endpoint**. Se non viene specificato, per impostazione predefinita sarà "Proxy".
endpoint | *Sì* | Endpoint a cui indirizzare le richieste. Gestirà la risposta, nonché eventuali effetti collaterali della richiesta.

### <a name="building-custom-resources"></a>Creazione di risorse personalizzate

**ResourceTypes** descrivono le nuove risorse personalizzate che vengono aggiunte ad Azure. Espongono i metodi CRUD RESTful di base. [Altre informazioni sulla creazione di risorse personalizzate](./custom-providers-resources-endpoint-how-to.md)

Esempio di provider di risorse personalizzato con **resourceTypes**:

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

API aggiunte ad Azure per l'esempio precedente:

HttpMethod | URI di esempio | Descrizione
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09-01-Preview | Chiamata all'API REST di Azure per creare una nuova risorsa.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09-01-Preview | Chiamata all'API REST di Azure per eliminare una risorsa esistente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}? API-Version = 2018-09-01-Preview | Chiamata all'API REST di Azure per recuperare una risorsa esistente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources? API-Version = 2018-09-01-anteprima | Chiamata all'API REST di Azure per recuperare l'elenco di risorse esistenti.

### <a name="building-custom-actions"></a>Creazione di azioni personalizzate

Le **azioni** descrivono le nuove azioni che vengono aggiunte ad Azure. Questi possono essere esposti al di sopra del provider di risorse o annidati in un oggetto **ResourceType**. [Altre informazioni sulla creazione di azioni personalizzate](./custom-providers-action-endpoint-how-to.md)

Esempio di provider di risorse personalizzato con **azioni**:

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

API aggiunte ad Azure per l'esempio precedente:

HttpMethod | URI di esempio | Descrizione
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction? API-Version = 2018-09-01-anteprima | Chiamata all'API REST di Azure per attivare l'azione.

## <a name="looking-for-help"></a>Assistenza

Per domande per lo sviluppo di provider di risorse personalizzati di Azure, provare a chiedere [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). È possibile che siano già state fornite risposte a domande simili, quindi verificare prima di pubblicarle. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni sui provider personalizzati. Passare all'articolo successivo per creare un provider personalizzato.

- [Guida introduttiva: creare un provider di risorse personalizzato di Azure e distribuire risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: creare azioni e risorse personalizzate in Azure](./tutorial-get-started-with-custom-providers.md)
- [Procedura: aggiungere azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedura: aggiungere risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
