---
title: Aprire le porte per una VM Linux con l'interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Informazioni su come aprire una porta o creare un endpoint per la VM Linux tramite il modello di distribuzione Azure Resource Manager e l'interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: eef9842b-495a-46cf-99a6-74e49807e74e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 91908b03522788d470fdb93121f620bfcdef9085
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="open-ports-and-endpoints-to-a-linux-vm-with-the-azure-cli"></a>Aprire porte ed endpoint per una VM Linux con l'interfaccia della riga di comando di Azure
Aprire una porta o creare un endpoint in una macchina virtuale (VM) di Azure tramite la creazione di un filtro di rete su una subnet o un'interfaccia di rete di VM. Questi filtri, che consentono di controllare il traffico in ingresso e in uscita, vengono inseriti in un gruppo di sicurezza di rete e collegati alla risorsa che riceve il traffico. Si userà un esempio comune di traffico Web sulla porta 80. Questo articolo illustra come aprire una porta in una VM con l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](nsg-quickstart-nodejs.md).

Per creare un gruppo di sicurezza di rete e le regole è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver eseguito l'accesso a un account Azure usando [az login](/cli/azure/#az_login).

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myNetworkSecurityGroup* e *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Aprire rapidamente una porta per una macchina virtuale
Se è necessario aprire rapidamente una porta per una macchina virtuale in uno scenario di sviluppo/test, è possibile usare il comando [az vm open-port](/cli/azure/vm#az_vm_open_port). Questo comando crea un gruppo di sicurezza di rete, aggiunge una regola e la applica a una macchina virtuale o una subnet. L'esempio seguente apre la porta *80* sulla macchina virtuale denominata *myVM* nel gruppo di risorse denominato *myResourceGroup*.

```azure-cli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Per un maggiore controllo sulle regole, ad esempio per definire un intervallo di indirizzi IP di origine, continuare con le altre procedure illustrate in questo articolo.


## <a name="create-a-network-security-group-and-rules"></a>Creare un gruppo di sicurezza di rete e le regole corrispondenti
Creare il gruppo di sicurezza di rete con [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup* nella posizione *eastus*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Aggiungere una regola con [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) per consentire il traffico HTTP al server Web (o in base allo scenario specifico, come l'accesso SSH o la connettività al database). L'esempio seguente crea una regola denominata *myNetworkSecurityGroupRule* per consentire il traffico TCP sulla porta 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Applicare un gruppo di sicurezza di rete alla macchina virtuale
Associare il gruppo di sicurezza di rete all'interfaccia di rete della VM (NIC) utilizzando [az network nic update](/cli/azure/network/nic#az_network_nic_update). L'esempio seguente associa una scheda di interfaccia di rete esistente denominata *myNic* al gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

In alternativa, è possibile associare il gruppo di sicurezza di rete a una subnet di rete virtuale utilizzando [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) anziché solo all'interfaccia di rete di una singola VM. L'esempio seguente associa una subnet esistente denominata *mySubnet* nella rete virtuale *myVnet* al gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](tutorial-virtual-network.md#secure-network-traffic).

Per le applicazioni Web a disponibilità elevata, è consigliabile inserire le macchine virtuali dietro a un Azure Load Balancer. Il bilanciamento del carico distribuisce il traffico alle macchine virtuali, con un gruppo di sicurezza di rete che consente di filtrare il traffico. Per altre informazioni, vedere [Come bilanciare il carico per le macchine virtuali di Linux in Azure per creare un'applicazione a disponibilità elevata](tutorial-load-balancer.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

* [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Che cos'è un gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)
