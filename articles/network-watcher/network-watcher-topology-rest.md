---
title: Visualizzare la topologia con Network Watcher di Azure - API REST | Documentazione Microsoft
description: Questo articolo descrive come usare l&quot;API REST per eseguire una query relativa alla topologia di rete.
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 568f3060da372f4a08cec342e04359172522cb69
ms.contentlocale: it-it
ms.lasthandoff: 05/26/2017

---

# <a name="view-network-watcher-topology-with-rest-api"></a>Visualizzare la topologia di Network Watcher con l'API REST

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [Interfaccia della riga di comando 1.0](network-watcher-topology-cli-nodejs.md)
> - [Interfaccia della riga di comando 2.0](network-watcher-topology-cli.md)
> - [API REST](network-watcher-topology-rest.md)

La funzionalità per la visualizzazione della topologia di Network Watcher offre una rappresentazione visiva delle risorse di rete in una sottoscrizione. Nel portale, questa visualizzazione è automatica. È possibile recuperare le informazioni sottostanti alla visualizzazione della topologia nel portale usando PowerShell.
Questa funzionalità rende le informazioni sulla topologia più versatili, poiché i dati possono essere usati anche da altri strumenti per compilare la visualizzazione.

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

In questo scenario, vengono recuperate le informazioni sulla topologia. ARMclient viene usato per chiamare l'API REST con PowerShell. ARMClient è reperibile in Chocolatey in [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient) (ARMClient in Chocolatey)

Questo scenario presuppone il completamento dei passaggi descritti in [Creare un servizio Network Watcher](network-watcher-create.md) per creare un servizio Network Watcher.

## <a name="scenario"></a>Scenario

Lo scenario illustrato in questo articolo consente di recuperare la risposta relativa alla topologia per un gruppo di risorse specificato.

## <a name="log-in-with-armclient"></a>Accedere con ARMClient

Accedere ad armclient con le credenziali di Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Recuperare una topologia

L'esempio seguente richiede la topologia dall'API REST.  Nell'esempio vengono impostati parametri per consentire flessibilità nella creazione di un esempio.  Sostituire tutti i valori con \< \> intorno a essi.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

La risposta seguente è un esempio di risposta abbreviata che viene restituita quando si recupera la topologia per un gruppo di risorse:

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI, vedere [Visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


