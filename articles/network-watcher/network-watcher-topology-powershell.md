---
title: Visualizzare la topologia con Network Watcher di Azure - PowerShell | Documentazione Microsoft
description: Questo articolo descrive come usare PowerShell per eseguire una query relativa alla topologia di rete.
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: af1bb9618492827bfae5f55a30fd165dbd5be11e
ms.lasthandoff: 02/23/2017

---

# <a name="view-network-watcher-topology-with-powershell"></a>Visualizzare la topologia di Network Watcher con PowerShell

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

In questo scenario, il cmdlet `Get-AzureRmNetworkWatcherTopology` viene usato per recuperare le informazioni sulla topologia. È anche disponibile un articolo che illustra come [recuperare una topologia di rete con l'API REST](network-watcher-topology-rest.md).

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo consente di recuperare la risposta relativa alla topologia per un gruppo di risorse specificato.

## <a name="retrieve-network-watcher"></a>Recuperare Network Watcher

Il primo passaggio consente di recuperare l'istanza di Network Watcher. La variabile `$networkWatcher` viene passata al cmdlet `Get-AzureRmNetworkWatcherTopology`.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Recuperare una topologia

Il cmdlet `Get-AzureRmNetworkWatcherTopology` recupera la topologia per un gruppo di risorse specificato.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Risultati

I risultati restituiti hanno il nome proprietà "Resources", che contiene il corpo della risposta in formato JSON per il cmdlet `Get-AzureRmNetworkWatcherTopology`.  La risposta contiene le risorse nel gruppo di sicurezza di rete e le relative associazioni (vale a dire, Contains, Associated).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI, vedere [Visualize NSG flows logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) (Visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI)



