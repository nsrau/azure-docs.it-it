---
title: Aggiungere o rimuovere una delega di subnet in una rete virtuale di AzureAdd or remove a subnet delegation in an Azure virtual network
titlesuffix: Azure Virtual Network
description: Informazioni su come aggiungere o rimuovere una subnet delegata per un servizio in Azure.Learn how to add or remove a delegated subnet for a service in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2019
ms.author: kumud
ms.openlocfilehash: 6f767abdf8673e3adffc6c4e3748733054ba723d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201867"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Aggiungere o rimuovere una delega di subnetAdd or remove a subnet delegation

La delega di subnet offre esplicite autorizzazioni per creare le risorse specifiche del servizio nella subnet, tramite un identificatore univoco durante la distribuzione del servizio. Questo articolo descrive come aggiungere o rimuovere una subnet delegata per un servizio di Azure.This article describes how to add or remove a delegated subnet for an Azure service.

## <a name="portal"></a>Portale

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

### <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione viene creata una rete virtuale e la subnet che verrà successivamente delegata a un servizio di Azure.In this section, you create a virtual network and the subnet that you'll later delegate to an Azure service.

1. Nella parte superiore sinistra dello schermo selezionare **Crea una risorsa** > Rete**virtuale****di rete** > .
1. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome | Immettere *MyVirtualNetwork*. |
    | Spazio degli indirizzi | Immettere *10.0.0.0/16*. |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Location | Selezionare **EastUS**.|
    | Subnet - Nome | Immettere *mySubnet*. |
    | Subnet - Intervallo di indirizzi | Immettere *10.0.0.0/24*. |
    |||
1. Lasciare il resto come predefinito e quindi selezionare **Crea**.

### <a name="permissions"></a>Autorizzazioni

Se non è stata creata la subnet che si desidera delegare a `Microsoft.Network/virtualNetworks/subnets/write`un servizio di Azure, è necessaria l'autorizzazione seguente: .

Il ruolo [Collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) incorporato contiene anche le autorizzazioni necessarie.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegare una subnet a un servizio di AzureDelegate a subnet to an Azure service

In questa sezione si delega la subnet creata nella sezione precedente a un servizio di Azure.In this section, you delegate the subnet that you created in the preceding section to an Azure service.

1. Nella barra di ricerca del portale, immettere *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
2. Nei risultati della ricerca selezionare *myVirtualNetwork*.
3. Selezionare **Subnets**, in **SETTINGS**, quindi **mySubnet**.
4. Nella pagina *mySubnet,* per l'elenco **Delega subnet,** selezionare uno dei servizi elencati in **Subnet delegata a un servizio,** ad esempio **Microsoft.DBforPostgreSQL/serversv2**.  

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Rimuovere la delega della subnet da un servizio di AzureRemove subnet delegation from an Azure service

1. Nella barra di ricerca del portale, immettere *myVirtualNetwork*. Selezionare **myVirtualNetwork** quando viene visualizzato nei risultati della ricerca.
2. Nei risultati della ricerca selezionare *myVirtualNetwork*.
3. Selezionare **Subnets**, in **SETTINGS**, quindi **mySubnet**.
4. Nella pagina *mySubnet,* per l'elenco **Delega subnet,** selezionare **Nessuno** dai servizi elencati in **Delega subnet a servizio**. 

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se invece si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, questo articolo richiede l'uso dell'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Come prima cosa creare un gruppo di risorse con [az group create](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato myResourceGroup nella posizione **eastus:The** following example creates a resource group named **myResourceGroup** in the eastus location:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare una rete virtuale denominata **myVnet** con una subnet denominata **mySubnet** in **myResourceGroup** usando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.0.0.0/24
```
### <a name="permissions"></a>Autorizzazioni

Se non è stata creata la subnet che si desidera delegare a `Microsoft.Network/virtualNetworks/subnets/write`un servizio di Azure, è necessaria l'autorizzazione seguente: .

Il ruolo [Collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) incorporato contiene anche le autorizzazioni necessarie.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegare una subnet a un servizio di AzureDelegate a subnet to an Azure service

In questa sezione si delega la subnet creata nella sezione precedente a un servizio di Azure.In this section, you delegate the subnet that you created in the preceding section to an Azure service. 

Usare l'aggiornamento della subnet vnet di rete az per aggiornare la subnet denominata mySubnet con una delega a un servizio di Azure.Use az network [vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) to update the subnet named **mySubnet** with a delegation to an Azure service.  In questo esempio **Microsoft.DBforPostgreSQL/serversv2** viene utilizzato per la delega di esempio:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --delegations Microsoft.DBforPostgreSQL/serversv2
```

Per verificare che la delega sia stata applicata, utilizzare [az network vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Verificare che il servizio sia delegato alla subnet nella proprietà **serviceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```

```json
[
  {
    "actions": [
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
    "etag": "W/\"8a8bf16a-38cf-409f-9434-fe3b5ab9ae54\"",
    "id": "/subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/0",
    "name": "0",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "serviceName": "Microsoft.DBforPostgreSQL/serversv2",
    "type": "Microsoft.Network/virtualNetworks/subnets/delegations"
  }
]
```

### <a name="remove-subnet-delegation-from-an-azure-service"></a>Rimuovere la delega della subnet da un servizio di AzureRemove subnet delegation from an Azure service

Utilizzare [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) per rimuovere la delega dalla subnet denominata **mySubnet**:

```azurecli-interactive
  az network vnet subnet update \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --remove delegations
```
Per verificare che la delega sia stata rimossa, utilizzare [az network vnet subnet show](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-show). Verificare che il servizio venga rimosso dalla subnet nella proprietà **serviceName**:

```azurecli-interactive
  az network vnet subnet show \
  --resource-group myResourceGroup \
  --name mySubnet \
  --vnet-name myVnet \
  --query delegations
```
L'output dal comando è una parentesi null:
```json
[]
```

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="connect-to-azure"></a>Connettersi ad Azure

```azurepowershell-interactive
  Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Creare un gruppo di risorse con [New-AzResourceGroup](https://docs.microsoft.com/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite.

L'esempio seguente crea un gruppo di risorse denominato myResourceGroup nella posizione *eastus:The* following example creates a resource group named *myResourceGroup* in the eastus location:

```azurepowershell-interactive
  New-AzResourceGroup -Name myResourceGroup -Location eastus
```
### <a name="create-virtual-network"></a>Creare una rete virtuale

Creare una rete virtuale denominata **myVnet** con una subnet denominata **mySubnet** usando [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in **myResourceGroup** con [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest). Lo spazio di indirizzi IP per la rete virtuale è **10.0.0.0/16**. La subnet all'interno della rete virtuale è **10.0.0.0/24**.  

```azurepowershell-interactive
  $subnet = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix "10.0.0.0/24"

  New-AzVirtualNetwork -Name myVnet -ResourceGroupName myResourceGroup -Location eastus -AddressPrefix "10.0.0.0/16" -Subnet $subnet
```
### <a name="permissions"></a>Autorizzazioni

Se non è stata creata la subnet che si desidera delegare a `Microsoft.Network/virtualNetworks/subnets/write`un servizio di Azure, è necessaria l'autorizzazione seguente: .

Il ruolo [Collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) incorporato contiene anche le autorizzazioni necessarie.

### <a name="delegate-a-subnet-to-an-azure-service"></a>Delegare una subnet a un servizio di AzureDelegate a subnet to an Azure service

In questa sezione si delega la subnet creata nella sezione precedente a un servizio di Azure.In this section, you delegate the subnet that you created in the preceding section to an Azure service. 

Usare [Add-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/add-azdelegation?view=latest) per aggiornare la subnet denominata mySubnet con una delega denominata myDelegation a un servizio di Azure.Use Add-AzDelegation to update the subnet named **mySubnet** with a delegation named **myDelegation** to an Azure service.  In questo esempio **Microsoft.DBforPostgreSQL/serversv2** viene utilizzato per la delega di esempio:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Add-AzDelegation -Name "myDelegation" -ServiceName "Microsoft.DBforPostgreSQL/serversv2" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Utilizzare [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) per verificare la delega:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  ProvisioningState : Succeeded
  ServiceName       : Microsoft.DBforPostgreSQL/serversv2
  Actions           : {Microsoft.Network/virtualNetworks/subnets/join/action}
  Name              : myDelegation
  Etag              : W/"9cba4b0e-2ceb-444b-b553-454f8da07d8a"
  Id                : /subscriptions/3bf09329-ca61-4fee-88cb-7e30b9ee305b/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet/delegations/myDelegation

```
### <a name="remove-subnet-delegation-from-an-azure-service"></a>Rimuovere la delega della subnet da un servizio di AzureRemove subnet delegation from an Azure service

Utilizzare [Remove-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/remove-azdelegation?view=latest) per rimuovere la delega dalla subnet denominata **mySubnet**:

```azurepowershell-interactive
  $vnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
  $subnet = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $vnet
  $subnet = Remove-AzDelegation -Name "myDelegation" -Subnet $subnet
  Set-AzVirtualNetwork -VirtualNetwork $vnet
```
Utilizzare [Get-AzDelegation](https://docs.microsoft.com/powershell/module/az.network/get-azdelegation?view=latest) per verificare che la delega sia stata rimossa:

```azurepowershell-interactive
  $subnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup" | Get-AzVirtualNetworkSubnetConfig -Name "mySubnet"
  Get-AzDelegation -Name "myDelegation" -Subnet $subnet

  Get-AzDelegation: Sequence contains no matching element

```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [gestire le subnet in Azure.](virtual-network-manage-subnet.md)
