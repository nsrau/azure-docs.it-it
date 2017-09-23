---
title: Aprire le porte per una VM Linux con l'interfaccia della riga di comando di Azure 1.0 | Documentazione Microsoft
description: Informazioni su come aprire una porta o creare un endpoint per la VM Linux tramite il modello di distribuzione Azure Resource Manager e l'interfaccia della riga di comando di Azure versione 1.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 847bc76c37ed929851712ba1c12463a01032e267
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017

---

# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure-using-the-azure-cli-10"></a>Apertura di porte ed endpoint per una VM Linux in Azure con l'interfaccia della riga di comando di Azure 1.0
Aprire una porta o creare un endpoint in una macchina virtuale (VM) di Azure tramite la creazione di un filtro di rete su una subnet o un'interfaccia di rete di VM. Questi filtri, che consentono di controllare il traffico in ingresso e in uscita, vengono inseriti in un gruppo di sicurezza di rete e collegati alla risorsa che riceve il traffico. Si userà un esempio comune di traffico Web sulla porta 80. Questo articolo illustra come aprire una porta in una VM usando l'interfaccia della riga di comando di Azure versione 1.0.


## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività
È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](#quick-commands): l'interfaccia della riga di comando per i modelli di distribuzione classica e di gestione delle risorse (questo articolo)
- [Interfaccia della riga di comando di Azure 2.0](nsg-quickstart.md): interfaccia della riga di comando di prossima generazione per il modello di distribuzione di Gestione risorsa


## <a name="quick-commands"></a>Comandi rapidi
Per creare un gruppo di sicurezza di rete e le regole, è necessario che sia installata e usata l'[interfaccia della riga di comando di Azure](../../cli-install-nodejs.md) versione 1.0 in modalità Resource Manager:

```azurecli
azure config mode arm
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono *myResourceGroup*, *myNetworkSecurityGroup* e *myVnet*.

Creare il gruppo di sicurezza di rete immettendo nomi e percorso in modo appropriato. L'esempio seguente crea un gruppo di sicurezza di rete denominato *myNetworkSecurityGroup* nella posizione *eastus*:

```azurecli
azure network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Aggiungere una regola per consentire il traffico HTTP al server Web o regolare per scenario in uso, come l'accesso SSH o la connettività al database. L'esempio seguente crea una regola denominata *myNetworkSecurityGroupRule* per consentire il traffico TCP sulla porta 80:

```azurecli
azure network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --destination-port-range 80 \
    --access allow
```

Associare il gruppo di sicurezza di rete con l'interfaccia di rete (NIC) della VM. L'esempio seguente associa una scheda di interfaccia di rete esistente denominata *myNic* al gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
azure network nic set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --name myNic
```

In alternativa, è possibile associare il gruppo di sicurezza di rete con una subnet di rete virtuale invece della sola interfaccia di rete in una singola VM. L'esempio seguente associa una subnet esistente denominata *mySubnet* nella rete virtuale *myVnet* al gruppo di sicurezza di rete denominato *myNetworkSecurityGroup*:

```azurecli
azure network vnet subnet set \
    --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>Altre informazioni sui gruppi di sicurezza di rete
I comandi rapidi seguenti consentono di rendere operativo il traffico verso la VM. I gruppi di sicurezza di rete offrono numerose funzionalità efficienti e la necessaria granularità per controllare l'accesso alle risorse. Per altre informazioni, leggere l'articolo sulla [creazione di un gruppo di sicurezza di rete e di regole dell'elenco di controllo di accesso qui](../../virtual-network/virtual-networks-create-nsg-arm-cli.md).

È possibile definire le regole dell'elenco di controllo di accesso e i gruppi di sicurezza di rete come parte dei modelli di Azure Resource Manager. Per altre informazioni, leggere l'articolo [Come creare NSG utilizzando un modello](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se si deve usare il port forwarding per eseguire il mapping di una sola porta esterna verso una porta interna della VM, usare un servizio di bilanciamento del carico e le regole Network Address Translation (NAT). Ad esempio, si desidera esporre la porta TCP 8080 esternamente e che il traffico venga indirizzato sulla porta TCP 80 in una VM. Per altre informazioni, leggere l'articolo relativo alla [creazione di un servizio di bilanciamento del carico per Internet](../../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="next-steps"></a>Passaggi successivi
In questo esempio viene creata una regola semplice per consentire il traffico HTTP. È possibile trovare informazioni sulla creazione di ambienti più dettagliati negli articoli seguenti:

* [Panoramica di Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Che cos'è un gruppo di sicurezza di rete](../../virtual-network/virtual-networks-nsg.md)
* [Panoramica di Azure Resource Manager per i servizi di bilanciamento del carico](../../load-balancer/load-balancer-arm.md)


