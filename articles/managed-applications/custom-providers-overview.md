---
title: Panoramica del provider di risorse personalizzati di Azure
description: Informazioni sui provider di risorse di Azure personalizzati e come estendere il piano API di Azure per adattare i flussi di lavoro.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d25ef00adc307bae57da2c04d4472874f8d67bcd
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67796076"
---
# <a name="azure-custom-resource-providers-overview"></a>Cenni preliminari sui provider di risorse personalizzati di Azure

I provider di risorse di Azure personalizzato è una piattaforma di estendibilità in Azure. Consente che alle API personalizzate che possono essere utilizzate per arricchire il valore predefinito è definire esperienza di Azure. Questa documentazione descrive:

- Come compilare e distribuire un Provider di risorse personalizzato di Azure.
- Viene descritto come utilizzare i provider di risorse di Azure personalizzato per estendere i flussi di lavoro esistente.
- Dove trovare esempi di codice e guide per iniziare.

![Cenni preliminari sul provider personalizzati](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> Provider personalizzati è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-can-custom-resource-providers-do"></a>Cosa possono fare i provider di risorse personalizzato

Di seguito sono riportati alcuni esempi di ciò che è possibile ottenere con i provider di risorse di Azure personalizzato:

- Estendere il REST API di Azure Resource Manager per includere i servizi interni ed esterni.
- Abilitare scenari personalizzati nella parte superiore di flussi di lavoro di Azure esistenti.
- Personalizzare il controllo di modelli di Resource Manager di Azure e l'effetto.

## <a name="what-is-a-custom-resource-provider"></a>Che cos'è un provider di risorse personalizzato

I provider di risorse di Azure personalizzato vengono effettuati tramite la creazione di un contratto tra Azure e un endpoint. Questo contratto definisce un elenco di nuove risorse e le azioni tramite una nuova risorsa **Microsoft.CustomProviders/resourceProviders**. Il provider di risorse personalizzato esporrà quindi queste nuove API in Azure. Azure provider di risorse personalizzati sono costituiti da tre parti: provider di risorse personalizzato **endpoint**e le risorse personalizzate.

## <a name="how-to-build-custom-resource-providers"></a>Come creare provider di risorse personalizzati

Provider di risorse personalizzati sono un elenco di contratti tra Azure e gli endpoint. Questo contratto viene descritto come Azure deve interagire con un endpoint. Gli atti di provider di risorse come un proxy e inoltra le richieste e risposte da e verso l'oggetto specificato **endpoint**. Un provider di risorse è possibile specificare due tipi di contratti: [ **i tipi di risorsa** ](./custom-providers-resources-endpoint-how-to.md) e [ **azioni**](./custom-providers-action-endpoint-how-to.md). Queste opzioni sono abilitate le definizioni di endpoint. Una definizione di endpoint è costituita da tre campi: **name**, **routingType**, e **endpoint**.

Endpoint di esempio:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Proprietà | Obbligatorio | Descrizione
---|---|---
name | *Sì* | Il nome della definizione dell'endpoint. Azure verrà esposto tramite l'API con questo nome ' /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *no* | Determina il tipo di contratto con il **endpoint**. Se non specificato, per impostazione predefinita sarà "Proxy".
endpoint | *Sì* | L'endpoint per indirizzare le richieste. Ciò gestirà la risposta, nonché tutti gli effetti collaterali della richiesta.

### <a name="building-custom-resources"></a>Creazione di risorse personalizzate

**I tipi di risorsa** descrivono nuove risorse personalizzate che vengono aggiunti ad Azure. Questi espongono metodi RESTful CRUD di base. Vedere [ulteriori informazioni sulla creazione di risorse personalizzate](./custom-providers-resources-endpoint-how-to.md)

Provider di risorse personalizzato di esempio **i tipi di risorsa**:

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

Le API aggiunte ad Azure per l'esempio precedente:

HttpMethod | URI di esempio | Descrizione
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | La chiamata all'API REST di Azure per creare una nuova risorsa.
DELETE | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | La chiamata all'API REST di Azure per eliminare una risorsa esistente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources / {customResourceName}? api-version = 2018-09-01-preview | La chiamata di API REST di Azure per recuperare una risorsa esistente.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | La chiamata all'API REST di Azure per recuperare l'elenco delle risorse esistenti.

### <a name="building-custom-actions"></a>Creazione di azioni personalizzate

**Azioni** descrivono nuove azioni che vengono aggiunti ad Azure. Questi possono essere esposte nella parte superiore del provider di risorse o annidati in un **resourceType**. Vedere [ulteriori informazioni sulla creazione di azioni personalizzate](./custom-providers-action-endpoint-how-to.md)

Provider di risorse personalizzato di esempio **azioni**:

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

Le API aggiunte ad Azure per l'esempio precedente:

HttpMethod | URI di esempio | Descrizione
---|---|---
INSERISCI | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | La chiamata all'API REST di Azure per attivare l'azione.

## <a name="looking-for-help"></a>Per informazioni su

Se hai domande per lo sviluppo di Provider di risorse di Azure personalizzato, provare a porre [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Una domanda simile potrebbe già frequenti e risposta, verificare prima di registrazione. Aggiungere il tag ```azure-custom-providers``` per ottenere una risposta rapida.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si appreso come provider personalizzati. Passare all'articolo successivo per creare un provider personalizzato.

- [Avvio rapido: Creare il Provider di risorse personalizzati di Azure e distribuire le risorse personalizzate](./create-custom-provider.md)
- [Esercitazione: Creare azioni personalizzate e le risorse in Azure](./tutorial-custom-providers-101.md)
- [Procedure: Aggiunta di azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedure: Aggiunta di risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
