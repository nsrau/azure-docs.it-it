---
title: Aggiungere IPv6 a un'applicazione IPv4 nella rete virtuale di Azure - Interfaccia della riga di comando di AzureAdd IPv6 to an application IPv4 in Azure virtual network - Azure CLI
titlesuffix: Azure Virtual Network
description: Questo articolo illustra come distribuire indirizzi IPv6 in un'applicazione esistente nella rete virtuale di Azure usando l'interfaccia della riga di comando di Azure.This article shows how to deploy IPv6 addresses to an existing application in Azure virtual network using Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2019
ms.author: kumud
ms.openlocfilehash: 5dc231febc2e9b605b9e7f603f5d036b8a2c62eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240750"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli-preview"></a>Aggiungere IPv6 a un'applicazione IPv4 nella rete virtuale di Azure - Interfaccia della riga di comando di Azure (anteprima)Add IPv6 to an application iPv4 in Azure virtual network - Azure CLI (Preview)

Questo articolo illustra come aggiungere indirizzi IPv6 a un'applicazione che usa l'indirizzo IP pubblico IPv4 in una rete virtuale di Azure per un servizio di bilanciamento del carico standard usando l'interfaccia della riga di comando di Azure.This article shows you how to add IPv6 addresses to an application that is using IPv4 public IP address in an Azure virtual network for a Standard Load Balancer using Azure CLI. L'aggiornamento sul posto include una rete virtuale e una subnet, un servizio di bilanciamento del carico standard con configurazioni front-end IPv4 e IPV6, macchine virtuali con schede di interfaccia di rete con configurazioni IPv4, gruppo di sicurezza di rete e indirizzi IP pubblici.

> [!Important]
> Il supporto IPv6 per la rete virtuale di Azure è attualmente in anteprima pubblica. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si decide di installare e usare l'interfaccia della riga di comando di Azure in locale, per questa guida introduttiva è necessario usare l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Per informazioni sull'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti

### <a name="register-the-service"></a>Registrare il servizio

Prima di distribuire un'applicazione dual stack in Azure, è necessario configurare la sottoscrizione per questa funzionalità di anteprima usando l'interfaccia della riga di comando di Azure seguente:Before you deploy a dual stack application in Azure, you must configure your subscription for this preview feature using the following Azure CLI:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. È possibile controllare lo stato della registrazione eseguendo il comando dell'interfaccia della riga di comando di Azure seguente:You can check your registration status by running the following Azure CLI command:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Al termine della registrazione eseguire questo comando:

```azurecli
az provider register --namespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard
Questo articolo presuppone che sia stato distribuito un servizio di bilanciamento del carico Standard come descritto in Guida introduttiva: Creare un servizio di bilanciamento del carico standard - Interfaccia della riga di comando di Azure.This article assumes that you deployed a Standard Load Balancer as described in [Quickstart: Create a Standard Load Balancer - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>Creare indirizzi IPv6

Creare un indirizzo IPv6 pubblico con az network public-ip create per il servizio di bilanciamento del carico Standard.Create public IPv6 address with with [az network public-ip create](/cli/azure/network/public-ip) for your Standard Load Balancer. Nell'esempio seguente viene creato un indirizzo IP pubblico IPv6 denominato PublicIP_v6 nel gruppo di risorse *myResourceGroupSLB:The* following example creates an IPv6 public IP address named *PublicIP_v6* in the myResourceGroupSLB resource group:

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Configurare il front-end del servizio di bilanciamento del carico IPv6Configure IPv6 load balancer frontend

COnfigure il servizio di bilanciamento del carico con il nuovo indirizzo IP IPv6 che usa [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) come segue:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Configurare il pool back-end del servizio di bilanciamento del carico IPv6Configure IPv6 load balancer backend pool

Creare il pool back-end per le schede di interfaccia di rete con indirizzi IPv6 usando [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) come segue:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Configurare le regole di bilanciamento del carico IPv6Configure IPv6 load balancer rules

Creare regole di bilanciamento del carico IPv6 con [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create).

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Aggiungere intervalli di indirizzi IPv6

Aggiungere intervalli di indirizzi IPv6 alla rete virtuale e alla subnet che ospita il servizio di bilanciamento del carico come indicato di seguito:Add IPv6 address ranges to the virtual network and subnet hosting the load balancer as follows:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Aggiungere la configurazione IPv6 alle schede di interfaccia di rete

Configurare le schede di interfaccia di rete della macchina virtuale con un indirizzo IPv6 usando [az network nic ip-config create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) come segue:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Visualizzare la rete virtuale dual stack IPv6 nel portale di AzureView IPv6 dual stack virtual network in Azure portal
È possibile visualizzare la rete virtuale dual stack IPv6 nel portale di Azure come segue:You can view the IPv6 dual stack virtual network in Azure portal as follows:
1. Nella barra di ricerca del portale, immettere *myVnet*.
2. Quando **myVnet** viene visualizzato nei risultati della ricerca, selezionarlo. Verrà avviata la pagina **Panoramica** della rete virtuale dual stack denominata *myVNet*. La rete virtuale dual stack mostra le tre schede di interfaccia di rete con configurazioni IPv4 e IPv6 situate nella subnet dello stack doppio denominata *mySubnet*.

  ![Rete virtuale dual stack IPv6 in Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> La rete virtuale IPv6 per Azure è disponibile nel portale di Azure in sola lettura per questa versione di anteprima.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato aggiornato un servizio di bilanciamento del carico Standard esistente con una configurazione IP front-end IPv4 a una configurazione dual stack (IPv4 e IPv6). Sono state inoltre aggiunte configurazioni IPv6 alle schede di interfaccia di rete delle macchine virtuali nel pool back-end. Per altre informazioni sul supporto IPv6 nelle reti virtuali di Azure, vedere [Che cos'è IPv6 per](ipv6-overview.md) la rete virtuale di Azure?
