---
title: Aprire le porte per una VM Linux con l&quot;interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Informazioni su come aprire una porta o creare un endpoint per la VM Linux tramite il modello di distribuzione Azure Resource Manager e l&quot;interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 2e086311a509432454a7b5e188591605d27c6999
ms.lasthandoff: 03/08/2017


---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Aprire porte ed endpoint per una VM Linux con l'interfaccia della riga di comando di Azure
Aprire una porta o creare un endpoint in una macchina virtuale (VM) di Azure tramite la creazione di un filtro di rete su una subnet o un'interfaccia di rete di VM. Questi filtri, che consentono di controllare il traffico in ingresso e in uscita, vengono inseriti in un gruppo di sicurezza di rete e collegati alla risorsa che riceve il traffico. Si userà un esempio comune di traffico Web sulla porta 80. Questo articolo illustra come aprire una porta in una VM con l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](virtual-machines-linux-nsg-quickstart-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="quick-commands"></a>Comandi rapidi
Per creare un gruppo di sicurezza di rete e le regole è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure usando [az login](/cli/azure/#login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. Alcuni esempi di nomi dei parametri sono `myResourceGroup`, `myNetworkSecurityGroup` e `myVnet`.

Creare il gruppo di sicurezza di rete con [az network nsg create](/cli/azure/network/nsg#create). L'esempio seguente crea un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup` nella posizione `westus`:

```azurecli
az network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Aggiungere una regola con [az network nsg rule create](/cli/azure/network/nsg/rule#create) per consentire il traffico HTTP al server Web (o in base allo scenario specifico, come l'accesso SSH o la connettività al database). L'esempio seguente crea una regola denominata `myNetworkSecurityGroupRule` per consentire il traffico TCP sulla porta 80:

```azurecli
az network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 80 --access allow
```

Associare il gruppo di sicurezza di rete all'interfaccia di rete della VM (NIC) utilizzando [az network nic update](/cli/azure/network/nic#update). L'esempio seguente associa una scheda di interfaccia di rete denominata `myNic` con il gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
az network nic update --resource-group myResourceGroup --name myNic \
    --network-security-group myNetworkSecurityGroup
```

In alternativa, è possibile associare il gruppo di sicurezza di rete a una subnet di rete virtuale utilizzando [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) anziché solo all'interfaccia di rete di una singola VM. L'esempio seguente associa una subnet esistente denominata `mySubnet` nella rete virtuale `myVnet` con il gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update --resource-group myResourceGroup \
    --vnet-name myVnet --name mySubnet --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

È possibile definire le regole dell'elenco di controllo di accesso e i gruppi di sicurezza di rete come parte dei modelli di Azure Resource Manager. Per altre informazioni, leggere l'articolo [Come creare NSG utilizzando un modello](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se si deve usare il port forwarding per eseguire il mapping di una sola porta esterna verso una porta interna della VM, usare un servizio di bilanciamento del carico e le regole Network Address Translation (NAT). Ad esempio, si desidera esporre la porta TCP 8080 esternamente e che il traffico venga indirizzato sulla porta TCP 80 in una VM. Per altre informazioni, leggere l'articolo relativo alla [creazione di un servizio di bilanciamento del carico per Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

* [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md)
* [Panoramica di Azure Resource Manager per i servizi di bilanciamento del carico](../load-balancer/load-balancer-arm.md)


