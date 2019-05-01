---
title: Creare un'istanza di Azure Network Watcher | Microsoft Docs
description: Informazioni su come abilitare Network Watcher in un'area di Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c97f6dff17896b8a58c17aed9063e0b2b5733503
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681570"
---
# <a name="create-an-azure-network-watcher-instance"></a>Creare un'istanza di Azure Network Watcher

Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Il monitoraggio a livello di scenario permette di diagnosticare i problemi in una visualizzazione completa a livello di rete. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure. Network Watcher viene abilitato tramite la creazione di una risorsa Network Watcher. Questa risorsa permette di sfruttare le funzionalità di Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher viene abilitato automaticamente
Quando si crea o si aggiorna una rete virtuale nella sottoscrizione, Network Watcher viene abilitato automaticamente nell'area della rete virtuale. Non è previsto alcun impatto sulle risorse o alcun addebito associato per l'abilitazione automatica di Network Watcher.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Rifiutare esplicitamente l'abilitazione automatica di Network Watcher
Se si vuole rifiutare esplicitamente l'abilitazione automatica di Network Watcher, è possibile eseguire i comandi seguenti:

> [!WARNING]
> Il rifiuto esplicito dell'abilitazione automatica di Network Watcher è una modifica permanente. Se in seguito si vuole acconsentire di nuovo esplicitamente, è necessario [contattare il supporto](https://azure.microsoft.com/support/options/).

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Creare un'istanza di Network Watcher nel portale

Passare ad **Tutti i servizi** > **Rete** > **Network Watcher**. È possibile selezionare tutte le sottoscrizioni per cui si vuole abilitare Network Watcher. Questa azione crea un'istanza di Network Watcher in ogni area in cui è disponibile.

![Creare un'istanza di Network Watcher](./media/network-watcher-create/figure1.png)

Quando si abilita Network Watcher tramite il portale, il nome dell'istanza di Network Watcher viene impostato automaticamente su *NetworkWatcher_region_name* dove *region_name* corrisponde all'area di Azure in cui l'istanza è abilitata. Ad esempio, un Network Watcher abilitato nell'area centro-occidentale degli Stati Uniti viene denominato *NetworkWatcher_westcentralus*.

L'istanza di Network Watcher viene creata automaticamente in un gruppo di risorse denominato *NetworkWatcherRG*. Questo gruppo di risorse viene creato se non esiste già.

Se si vuole personalizzare il nome di un'istanza di Network Watcher e il gruppo di risorse in cui è inserito, è possibile usare PowerShell, l'API REST, l'interfaccia della riga di comando di Azure o i metodi ARMClient descritti nelle sezioni seguenti. Per ogni opzione, il gruppo di risorse deve esistere prima di crearvi un Network Watcher all'interno.  

## <a name="create-a-network-watcher-with-powershell"></a>Creare un'istanza di Network Watcher con PowerShell

Per creare un'istanza di Network Watcher, eseguire questo esempio:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Creare un'istanza di Network Watcher con l'interfaccia della riga di comando di Azure

Per creare un'istanza di Network Watcher, eseguire questo esempio:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Creare un'istanza di Network Watcher con l'API REST

ARMClient viene usato per chiamare l'API REST con PowerShell. ARMClient è reperibile in Chocolatey in [ARMClient on Chocolatey](https://chocolatey.org/packages/ARMClient) (ARMClient in Chocolatey)

### <a name="log-in-with-armclient"></a>Accedere con ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Creare l'istanza di Network Watcher

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un'istanza di Network Watcher, è possibile approfondire le funzionalità disponibili, indicate di seguito:

* [Topologia](network-watcher-topology-overview.md)
* [Acquisizione pacchetti](network-watcher-packet-capture-overview.md)
* [Verifica del flusso IP](network-watcher-ip-flow-verify-overview.md)
* [Hop successivo](network-watcher-next-hop-overview.md)
* [Visualizzazione di un gruppo di sicurezza](network-watcher-security-group-view-overview.md)
* [Registrazione dei flussi dei gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md)
* [Risoluzione dei problemi del gateway di rete virtuale](network-watcher-troubleshoot-overview.md)

Dopo aver creato un'istanza di Network Watcher, è possibile abilitare l'acquisizione di pacchetti all'interno delle macchine virtuali. Per informazioni su come fare, vedere l'articolo su come [creare un'acquisizione di pacchetti attivata da un avviso](network-watcher-alert-triggered-packet-capture.md)
