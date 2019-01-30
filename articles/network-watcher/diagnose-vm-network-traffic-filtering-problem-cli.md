---
title: Diagnosticare un problema di filtro del traffico di rete di una macchina virtuale - Guida introduttiva - Interfaccia della riga di comando di Azure | Microsoft Docs
description: In questa guida introduttiva si apprende come diagnosticare un problema di filtro del traffico di rete di una macchina virtuale usando la funzionalità di verifica del flusso IP di Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 8b40a4fbee1e6ee501178039e87f39d47d77fcab
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855976"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Guida introduttiva: Diagnosticare un problema di filtro del traffico di rete di una macchina virtuale - Interfaccia della riga di comando di Azure

In questa guida introduttiva si apprende come distribuire una macchina virtuale e quindi controllare le comunicazioni verso un indirizzo IP e un URL e da un indirizzo IP. Viene determinata la causa di un errore di comunicazione e si apprende come è possibile risolverlo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questa guida introduttiva richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione installata, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Dopo avere verificato la versione dell'interfaccia della riga di comando, eseguire `az login` per creare una connessione con Azure. I comandi dell'interfaccia della riga di comando di questa guida introduttiva sono formattati per essere eseguiti in una shell Bash.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Prima di poter creare una macchina virtuale, è necessario creare un gruppo di risorse per contenerla. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az_group_create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Creare una VM con il comando [az vm create](/cli/azure/vm#az_vm_create). Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. L'esempio seguente crea una macchina virtuale denominata *myVM*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La creazione della VM richiede alcuni minuti. Non continuare con i passaggi rimanenti fino a quando non viene creata la macchina virtuale e l'interfaccia della riga di comando non restituisce l'output.

## <a name="test-network-communication"></a>Testare la comunicazione di rete

Per testare la comunicazione di rete con Network Watcher è necessario innanzitutto abilitare un network watcher nell'area in cui si trova la macchina virtuale che si intende testare, quindi usare la funzionalità di verifica del flusso IP di Network Watcher per testare la comunicazione.

### <a name="enable-network-watcher"></a>Abilitare Network Watcher

Se si dispone già di un Network Watcher abilitato nell'area Stati Uniti orientali, passare al paragrafo [Usare la verifica del flusso IP](#use-ip-flow-verify). Usare il comando [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) per creare un Network Watcher nell'area degli Stati Uniti orientali:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>Usare la verifica del flusso IP

Quando si crea una macchina virtuale, per impostazione predefinita Azure consente e nega il traffico di rete da e verso la macchina virtuale. È possibile sostituire in un secondo momento le impostazioni predefinite di Azure, consentendo o negando altri tipi di traffico. Per verificare se il traffico verso destinazioni diverse e da un indirizzo IP di origine è consentito o negato, usare il comando [az network watcher test-ip-flow](/cli/azure/network/watcher#az-network-watcher-test-ip-flow).

Testare le comunicazioni in uscita dalla macchina virtuale a uno degli indirizzi IP di www.bing.com:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Dopo alcuni secondi, il risultato restituito informa l'utente che l'accesso è consentito dalla regola di sicurezza denominata **AllowInternetOutbound**.

Testare le comunicazioni in uscita dalla macchina virtuale verso 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Il risultato restituito informa l'utente che l'accesso è negato dalla regola di sicurezza denominata **DefaultOutboundDenyAll**.

Testare le comunicazioni in ingresso verso la macchina virtuale provenienti da 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Il risultato restituito informa l'utente che l'accesso è negato dalla regola di sicurezza denominata **DefaultInboundDenyAll**. Conoscendo le regole di sicurezza che consentono o negano il traffico da o verso una macchina virtuale, è possibile determinare come risolvere i problemi.

## <a name="view-details-of-a-security-rule"></a>Visualizzare i dettagli di una regola di sicurezza

Per comprendere perché le regole indicate in [Usare la verifica del flusso IP](#use-ip-flow-verify) consentono o impediscono la comunicazione, esaminare le regole di sicurezza valide per l'interfaccia di rete con il comando [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg):

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

L'output restituito include il testo seguente per la regola **AllowInternetOutbound** che ha consentito l'accesso in uscita a www.bing.com in un passaggio precedente in [Usare la verifica del flusso IP](#use-ip-flow-verify):

```azurecli
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

Nell'output precedente il valore di **destinationAddressPrefix** è **Internet**. Tuttavia, non è chiaro come 13.107.21.200 sia correlato a **Internet**. In **expandedDestinationAddressPrefix** sono elencati numerosi prefissi degli indirizzi. Uno dei prefissi nell'elenco è **12.0.0.0/6**, che comprende l'intervallo di indirizzi IP 12.0.0.1-15.255.255.254. Poiché 13.107.21.200 è compreso in tale intervallo di indirizzi, la regola **AllowInternetOutBound** consente il traffico in uscita. Inoltre, nell'output precedente non sono presenti regole con priorità più alta (numero inferiore) che sostituiscono questa regola. Per impedire le comunicazioni in uscita verso un indirizzo IP, è possibile aggiungere una regola di sicurezza con una priorità più alta, che neghi l'accesso in uscita dalla porta 80 verso l'indirizzo IP.

Quando si esegue il comando `az network watcher test-ip-flow` per testare le comunicazioni in uscita verso 172.131.0.100 in [Usare la verifica del flusso IP](#use-ip-flow-verify), l'output informa l'utente che la regola **DefaultOutboundDenyAll** ha negato la comunicazione. La regola **DefaultOutboundDenyAll** equivale alla regola **DenyAllOutBound** indicata nell'output derivante dal comando `az network nic list-effective-nsg`:

```azurecli
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

La regola indica **0.0.0.0/0** come valore di **destinationAddressPrefix**. La regola nega la comunicazione in uscita verso 172.131.0.100, perché l'indirizzo non rientra nel valore di **destinationAddressPrefix** di nessuna delle altre regole in uscita nell'output derivanti dal comando `az network nic list-effective-nsg`. Per consentire la comunicazione in uscita, è possibile aggiungere una regola di sicurezza con una priorità più alta, che consente il traffico in uscita sulla porta 80 verso 172.131.0.100.

Quando si esegue il comando `az network watcher test-ip-flow` in [Usare la verifica del flusso IP](#use-ip-flow-verify) per testare le comunicazioni in ingresso da 172.131.0.100, l'output informa l'utente che la regola **DefaultInboundDenyAll** ha negato la comunicazione. La regola **DefaultInboundDenyAll** equivale alla regola **DenyAllInBound** indicata nell'output derivante dal comando `az network nic list-effective-nsg`:

```azurecli
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

La regola **DenyAllInBound** viene applicata perché, come illustrato nell'output, non è presente un'altra regola con priorità più alta nell'output del comando `az network nic list-effective-nsg` che consente l'ingresso della porta 80 nella macchina virtuale da 172.131.0.100. Per consentire la comunicazione in ingresso, è possibile aggiungere una regola di sicurezza con una priorità più alta, che consente il traffico in ingresso sulla porta 80 da 172.131.0.100.

I controlli eseguiti in questa guida introduttiva hanno consentito di testare la configurazione di Azure. Se i controlli hanno restituito i risultati previsti ma sussistono ancora problemi di rete, verificare che non sia presente un firewall tra la macchina virtuale e l'endpoint con cui si sta comunicando e che il sistema operativo nella macchina virtuale non disponga di un firewall che stia consentendo o negando la comunicazione.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [az group delete](/cli/azure/group) per rimuoverli:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare una macchina virtuale e diagnosticare problemi dei filtri del traffico di rete in ingresso e in uscita. Si è appreso che le regole del gruppo di sicurezza di rete consentono o negano il traffico da e verso una macchina virtuale. Altre informazioni sulle [regole di sicurezza](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e su come [creare le regole di sicurezza](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Anche se i filtri del traffico di rete non presentano problemi, la comunicazione verso una macchina virtuale può non riuscire a causa della configurazione del routing. Per informazioni su come diagnosticare problemi di routing della rete delle macchine virtuali, vedere [Diagnosticare i problemi di routing delle macchine virtuali](diagnose-vm-network-routing-problem-cli.md) o, per diagnosticare con uno strumento i problemi inerenti il routing in uscita, la latenza e i filtri del traffico, vedere [Risolvere i problemi relativi alle connessioni](network-watcher-connectivity-cli.md).
