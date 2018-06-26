---
title: Esempio di interfaccia della riga di comando - Servizio Load Balancer per il bilanciamento del carico delle macchine virtuali tra zone di disponibilità - Azure | Microsoft Docs
description: Questo esempio di script dell'interfaccia della riga di comando di Azure illustra come bilanciare il carico del traffico verso le macchine virtuali tra zone di disponibilità
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 92b7ebaeaa2c4bd32cf75307c10f22c272b99794
ms.sourcegitcommit: 5821eef990c26fa045e4beacce39f6b02b83156b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/15/2018
ms.locfileid: "35662801"
---
# <a name="azure-cli-script-example-load-balance-vms-across-availability-zones"></a>Esempio di interfaccia della riga di comando di Azure: bilanciare il carico delle macchine virtuali tra zone di disponibilità

Questo script di esempio dell'interfaccia della riga di comando di Azure crea tutti gli elementi necessari per eseguire più macchine virtuali Ubuntu configurate in una configurazione a disponibilità elevata e con bilanciamento del carico. Dopo aver eseguito lo script, si disporrà di tre macchine virtuali, in tutte le zone di disponibilità all'interno di un'area, accessibili tramite Azure Load Balancer Standard. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

```azurecli-interactive
  #!/bin/bash

  # Create a resource group.
   az group create \
    --name myResourceGroup \
    --location westeurope

  # Create a virtual network.
   az network vnet create \
    --resource-group myResourceGroup \
    --location westeurope \
    --name myVnet \
    --subnet-name mySubnet

  # Create a zonal Standard public IP address.
   az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --sku Standard    

  # Create an Azure Load Balancer.
   az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --sku Standard

  # Creates an LB probe on port 80.
   az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80

  # Creates an LB rule for port 80.
   az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRuleWeb \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe

  # Create three NAT rules for port 22.
   for i in `seq 1 3`; do
    az network lb inbound-nat-rule create \
     --resource-group myResourceGroup \
     --lb-name myLoadBalancer \
     --name myLoadBalancerRuleSSH$i \
     --protocol tcp \
     --frontend-port 422$i \
     --backend-port 22 \
     --frontend-ip-name myFrontEndPool
   done

  # Create a network security group
   az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup

  # Create a network security group rule for port 22.
   az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*'  \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 1000

  # Create a network security group rule for port 80.
    az network nsg rule create \
     --resource-group myResourceGroup \
     --nsg-name myNetworkSecurityGroup \
     --name myNetworkSecurityGroupRuleHTTP \
     --protocol tcp \
     --direction inbound \
     --source-address-prefix '*' \
     --source-port-range '*' \
     --destination-address-prefix '*' \
     --destination-port-range 80 \
     --access allow \
     --priority 2000

  # Create three virtual network cards and associate with load balancer and NSG.
  for i in `seq 1 3`; do
   az network nic create \
     --resource-group myResourceGroup \
     --name myNic$i \
     --vnet-name myVnet \
     --subnet mySubnet \
     --network-security-group myNetworkSecurityGroup \
     --lb-name myLoadBalancer \
     --lb-address-pools myBackEndPool \
     --lb-inbound-nat-rules myLoadBalancerRuleSSH$i
  done

# Create three virtual machines, this creates SSH keys if not present.
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroup \
    --name myVM$i \
    --zone $i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
done

```

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_create) | Consente di creare una rete virtuale e una subnet di Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_create) | Consente di creare un indirizzo IP pubblico con un indirizzo IP statico e un nome DNS associato. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az_network_lb_create) | Crea un servizio di bilanciamento del carico di Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az_network_lb_probe_create) | Crea un probe di bilanciamento del carico. Il probe di bilanciamento del carico viene usato per monitorare tutte le macchine virtuali nel set di bilanciamento del carico. Se una macchina virtuale diventa inaccessibile, il traffico non viene indirizzato sulla macchina virtuale. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az_network_lb_rule_create) | Crea una regola di bilanciamento del carico. In questo esempio viene creata una regola per la porta 80. Poiché il traffico HTTP arriva al bilanciamento del carico, viene indirizzato sulla porta 80 delle macchine virtuali incluse in un set di bilanciamento del carico. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Crea una regola Network Address Translation (NAT) di bilanciamento del carico.  Le regole NAT mappano una porta del bilanciamento del carico su una porta in una macchina virtuale. In questo esempio viene creata una regola NAT per il traffico SSH in ogni macchina virtuale disponibile nel set di bilanciamento del carico.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az_network_nsg_create) | Consente di creare un gruppo di sicurezza di rete, ovvero un confine di sicurezza tra Internet e la macchina virtuale. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Consente di creare una regola NSG per consentire il traffico in ingresso. In questo esempio, la porta 22 è aperta al traffico SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az_network_nic_create) | Consente di creare una scheda di rete virtuale e la collega alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. |
| [az vm create](/cli/azure/vm#az_vm_create) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando per la rete di Azure sono disponibili nella [documentazione con la panoramica delle reti di Azure](../cli-samples.md).