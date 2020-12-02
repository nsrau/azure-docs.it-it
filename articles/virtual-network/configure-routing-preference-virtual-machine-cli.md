---
title: Configurare la preferenza di routing per una macchina virtuale - Interfaccia della riga di comando di Azure
description: Informazioni su come creare una macchina virtuale con un indirizzo IP pubblico scegliendo il tipo di routing con l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: ccd04a43e6781e8d58234cc382b2739d800e5fe7
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510676"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Configurare la preferenza di routing per una macchina virtuale con l'interfaccia della riga di comando di Azure

Questo articolo illustra come configurare la preferenza di routing per una macchina virtuale. Quando si sceglie **Internet** come preferenza di routing, il traffico associato a Internet proveniente dalla macchina virtuale viene instradato tramite la rete dell'ISP. L'opzione di routing predefinita è tramite la rete globale Microsoft.

Questo articolo illustra come creare una macchina virtuale con un indirizzo IP pubblico impostato per instradare il traffico tramite Internet pubblico con l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> La preferenza di routing è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
1. Se si usa Cloud Shell, andare al passaggio 2. Aprire una sessione di comando e accedere ad Azure con `az login`.
2. Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse nell'area di Azure Stati Uniti orientali:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico
Per accedere alla propria macchina virtuale da Internet, è necessario creare un indirizzo IP pubblico. Creare un indirizzo IP pubblico con [az network public-ip create](/cli/azure/network/public-ip). L'esempio seguente crea un indirizzo IP pubblico con il tipo di preferenza di routing *Internet* nell'area *Stati Uniti orientali*:

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Creare risorse di rete

Prima di distribuire una macchina virtuale è necessario creare le risorse di rete virtuale di supporto, ovvero il gruppo di sicurezza di rete, la rete virtuale e il NIC virtuale.

### <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Creare un gruppo di sicurezza di rete per le regole che regolamenteranno le comunicazioni in ingresso e in uscita della rete virtuale con [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Creare una rete virtuale con [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create). L'esempio seguente crea una rete virtuale denominata *myVnet* con la subnet *mySubNet*:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Creare una scheda di interfaccia di rete

Creare una scheda di interfaccia di rete virtuale per la VM con [az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create). L'esempio seguente crea una scheda di interfaccia di rete virtuale che verrà collegata alla VM.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create). L'esempio seguente crea una VM Windows Server 2019 e i componenti di rete virtuale necessari, se non esistono già.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [az group delete](/cli/azure/group#az-group-delete) per rimuoverli:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [preferenze di routing negli indirizzi IP pubblici](routing-preference-overview.md).
- Altre informazioni sugli [indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
- Altre informazioni sull'[impostazione dell'indirizzo IP pubblico](virtual-network-public-ip-address.md#create-a-public-ip-address).
