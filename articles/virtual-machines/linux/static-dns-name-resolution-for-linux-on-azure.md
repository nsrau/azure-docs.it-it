---
title: Usare il DNS interno per la risoluzione dei nomi di VM con l&quot;interfaccia della riga di comando di Azure 2.0 | Documentazione Microsoft
description: Come creare schede di interfaccia di rete virtuale e usare DNS interni per la risoluzione dei nomi di VM in Azure con l&quot;interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: af83156022fe7b7122b8fc8b71443592da4d3950
ms.lasthandoff: 04/03/2017


---

# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Creare schede di interfaccia di rete virtuale e usare DNS interni per la risoluzione dei nomi di VM in Azure
Questo articolo illustra come impostare nomi DNS interni statici per VM Linux usando schede di interfaccia di rete virtuale (vNic) e nomi di etichette DNS con l'interfaccia della riga di comando di Azure 2.0. È possibile anche eseguire questi passaggi tramite l'[interfaccia della riga di comando di Azure 1.0](static-dns-name-resolution-for-linux-on-azure-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Si ricorre ai nomi DNS statici per i servizi di infrastruttura permanenti, ad esempio per un server di compilazione Jenkins, usato per questo documento, o un server Git.

I requisiti sono:

* [Un account di Azure](https://azure.microsoft.com/pricing/free-trial/)
* [File di chiavi SSH pubbliche e private](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Comandi rapidi
Se si vuole eseguire rapidamente l'attività, la sezione seguente indica dettagliatamente i comandi necessari. Altre informazioni dettagliate e il contesto per ogni passaggio sono disponibili nelle sezioni successive del documento, a partire da [qui](#detailed-walkthrough). Per eseguire questi passaggi è necessario aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2) e aver effettuato l'accesso a un account Azure con il comando [az login](/cli/azure/#login).

Pre-requisiti: gruppo di risorse, rete virtuale e subnet, gruppo di sicurezza di rete con SSH in ingresso.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Creare una scheda di interfaccia di rete virtuale con un nome DNS interno statico
Creare la vNic con il comando [az network nic create](/cli/azure/network/nic#create). Il flag `--internal-dns-name` dell'interfaccia della riga di comando consente di impostare l'etichetta DNS, che indica il nome DNS statico per la scheda di interfaccia di rete virtuale (vNic). L'esempio seguente crea una scheda di rete virtuale denominata `myNic`, la connette alla rete virtuale `myVnet` e crea un record di nome DNS interno denominato `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>Distribuire una VM e connettere la scheda di interfaccia di rete virtuale
Creare una VM con il comando [az vm create](/cli/azure/vm#create). Il flag `--nics` consente di connette la scheda di interfaccia di rete virtuale alla VM durante la distribuzione in Azure. L'esempio seguente crea una VM `myVM` con Managed Disks di Azure e collega la scheda di interfaccia di rete virtuale denominata `myNic` ottenuta dal passaggio precedente:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Procedura dettagliata

In Azure, un'infrastruttura interamente basata sul modello CiCd (Continuous Integration and Continuous Deployment) richiede la disponibilità di server statici o di lunga durata. È consigliabile che gli asset di Azure, come le reti virtuali e i gruppi di sicurezza di rete, siano risorse statiche, ovvero di lunga durata e distribuite raramente. Dopo essere stata distribuita, una rete virtuale può essere usata in nuove distribuzioni senza alcun effetto negativo sull'infrastruttura. È possibile aggiungere in seguito un server repository Git e un server di automazione Jenkins, è possibile distribuire un modello CiCd in questa rete virtuale per un ambiente di test o di sviluppo.  

I nomi DNS interni sono risolvibili solo all'interno di una rete virtuale di Azure. Trattandosi di nomi interni, non sono risolvibili esternamente in Internet e garantiscono quindi una sicurezza aggiuntiva per l'infrastruttura.

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `myNic` e `myVM`.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.
Creare prima il gruppo di risorse con [az group create](/cli/azure/group#create). Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

Il passaggio successivo è creare una rete virtuale in cui avviare le VM. Ai fini di questa procedura, la rete virtuale contiene una subnet. Per altre informazioni sulle reti virtuali in Azure, vedere [Creare una rete virtuale usando l'interfaccia della riga di comando di Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Creare la rete virtuale con [az network vnet create](/cli/azure/network/vnet#create). L'esempio seguente creata una rete virtuale denominata `myVnet` e una sottorete denominata `mySubnet`:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Creare il gruppo di sicurezza di rete
I gruppi di sicurezza di rete di Azure sono analoghi a un firewall a livello di rete. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Come creare gruppi di sicurezza di rete nell'interfaccia della riga di comando di Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Creare il gruppo di sicurezza di rete con [az network nsg create](/cli/azure/network/nsg#create). Nell'esempio seguente viene creato un gruppo di sicurezza di rete denominato `myNetworkSecurityGroup`:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>Aggiungere una regola in entrata per consentire SSH
Aggiungere una regole in ingresso per il gruppo di sicurezza di rete con [az network nsg rule create](/cli/azure/network/nsg/rule#create). Nell'esempio seguente viene creata una regola denominata `myRuleAllowSSH`:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Associare la subnet con il gruppo di sicurezza di rete
Per associare la subnet con il gruppo di sicurezza di rete usare [az network vnet subnet update](/cli/azure/network/vnet/subnet#update). L'esempio seguente associa il nome di subnet `mySubnet` al gruppo di sicurezza di rete `myNetworkSecurityGroup`:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Creare la scheda di interfaccia di rete virtuale e i nomi DNS statici
Sebbene Azure sia molto flessibile, per poter usare nomi DNS per la risoluzione dei nomi di VM è necessario creare schede di interfaccia di rete virtuale (vNic) che includano un'etichetta DNS. Le schede di interfaccia di rete virtuale sono importanti in quanto è possibile riutilizzarle connettendole a VM differenti nel ciclo di vita dell'infrastruttura. In questo modo la scheda di interfaccia di rete virtuale diventa una risorsa statica, mentre le VM possono essere temporanee. Applicando l'etichettatura DNS a una scheda di interfaccia di rete virtuale, è possibile abilitare nella rete virtuale la risoluzione di nomi semplici provenienti da altre VM. L'uso di nomi risolvibili consente anche ad altre macchine virtuali di accedere al server di automazione in base al nome DNS `Jenkins` o al server Git come `gitrepo`.  

Creare la vNic con il comando [az network nic create](/cli/azure/network/nic#create). L'esempio seguente crea una scheda di rete virtuale denominata `myNic`, la connette alla rete virtuale `myVnet` denominata `myVnet` e crea un record di nome DNS interno denominato `jenkins`:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Distribuire la macchina virtuale nell'infrastruttura di rete virtuale
A questo punto sono disponibili una rete virtuale e subnet, un gruppo di sicurezza di rete che funge da firewall per proteggere la subnet bloccando tutto il traffico in ingresso tranne quello verso la porta 22 per SSH e una scheda di interfaccia di rete virtuale. Ora è possibile distribuire una VM all'interno di questa infrastruttura di rete esistente.

Creare una VM con il comando [az vm create](/cli/azure/vm#create). L'esempio seguente crea una VM `myVM` con Managed Disks di Azure e collega la scheda di interfaccia di rete virtuale denominata `myNic` ottenuta dal passaggio precedente:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Usando i flag dell'interfaccia della riga di comando per chiamare le risorse esistenti, si indica ad Azure di distribuire la macchina virtuale all'interno della rete esistente. Come già detto, dopo essere state distribuite la rete virtuale e la subnet possono essere usate come risorse statiche o permanenti nell'area di Azure.  

## <a name="next-steps"></a>Passaggi successivi

* [Usare un modello di Azure Resource Manager per creare una distribuzione specifica](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare un ambiente Linux completo mediante l'interfaccia della riga di comando di Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creare una VM Linux in Azure usando i modelli](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

