---
title: Visualizzare la topologia con Network Watcher di Azure - Interfaccia della riga di comando di Azure | Documentazione Microsoft
description: Questo articolo descrive come usare l&quot;interfaccia della riga di comando di Azure per eseguire una query relativa alla topologia di rete.
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 404e621bc1d16d15aeb705a1055ed4c2b9e251a2
ms.lasthandoff: 02/23/2017

---

# <a name="view-network-watcher-topology-with-azure-cli"></a>Visualizzare la topologia di Network Watcher con l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI](network-watcher-topology-cli.md)
> - [API REST](network-watcher-topology-rest.md)

La funzionalità per la visualizzazione della topologia di Network Watcher offre una rappresentazione visiva delle risorse di rete in una sottoscrizione. Nel portale, questa visualizzazione è automatica. È possibile recuperare le informazioni sottostanti alla visualizzazione della topologia nel portale usando PowerShell.
Questa funzionalità rende le informazioni sulla topologia più versatile, poiché i dati possono essere usati anche da altri strumenti.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

L'interconnessione è modellata in due relazioni.

- **Contenimento** - Esempio: la rete virtuale contiene una subnet che contiene una scheda NIC
- **Associazione** - Esempio: la scheda NIC è associata a una macchina virtuale

L'elenco include le proprietà restituite quando si esegue una query all'API REST della topologia.

* **name** - Il nome della risorsa.
* **id** - L'URI della risorsa.
* **location** - La località in cui si risiede la risorsa.
* **associations** - Un elenco di associazioni all'oggetto di riferimento.
    * **name** - Il nome della risorsa di riferimento.
    * **resourceId** - ResourceId è l'URI della risorsa di riferimento nell'associazione.
    * **associationType** - Il valore fa riferimento alla relazione tra l'oggetto figlio e l'oggetto padre. I valori validi sono **Contains** o **Associated**.

## <a name="before-you-begin"></a>Prima di iniziare

In questo scenario, il cmdlet `network watcher topology` viene usato per recuperare le informazioni sulla topologia. È anche disponibile un articolo che illustra come [recuperare una topologia di rete con l'API REST](network-watcher-topology-rest.md).

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo consente di recuperare la risposta relativa alla topologia per un gruppo di risorse specificato.

## <a name="retrieve-topology"></a>Recuperare una topologia

Il cmdlet `network watcher topology` recupera la topologia per un gruppo di risorse specificato. Aggiungere l'argomento "-json" per visualizzare l'output in formato JSON.

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Risultati

I risultati restituiti hanno il nome proprietà "Resources", che contiene il corpo della risposta in formato JSON per il cmdlet `network watcher topology`.  La risposta contiene le risorse nel gruppo di sicurezza di rete e le relative associazioni (vale a dire, Contains, Associated).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle regole di sicurezza applicate alle risorse di rete, leggere la [panoramica sulla visualizzazione di gruppo di sicurezza](network-watcher-security-group-view-overview.md).

