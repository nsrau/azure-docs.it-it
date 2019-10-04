---
title: Diagnosticare un problema di routing di rete di una macchina virtuale - Interfaccia della riga di comando di Azure | Microsoft Docs
description: In questo articolo si apprenderà come diagnosticare un problema di routing di rete di una macchina virtuale usando la funzionalità Hop successivo di Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 968b7dd703ba40f46a068deb1d8b7d2b32e0de2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688206"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnosticare un problema di routing di rete di una macchina virtuale - Interfaccia della riga di comando di Azure

In questo articolo si distribuisce una macchina virtuale e quindi si controllano le comunicazioni verso un indirizzo IP e un URL. Viene determinata la causa di un errore di comunicazione e si apprende come è possibile risolverlo.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, questo articolo richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione installata, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Dopo avere verificato la versione dell'interfaccia della riga di comando, eseguire `az login` per creare una connessione con Azure. I comandi dell'interfaccia della riga di comando in questo articolo sono formattati per essere eseguiti in una shell Bash.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Prima di poter creare una macchina virtuale, è necessario creare un gruppo di risorse per contenerla. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Creare una VM con il comando [az vm create](/cli/azure/vm#az-vm-create). Il comando crea le chiavi SSH, se non esistono già in una posizione predefinita. Per usare un set specifico di chiavi, utilizzare l'opzione `--ssh-key-value`. L'esempio seguente crea una macchina virtuale denominata *myVM*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La creazione della VM richiede alcuni minuti. Non continuare con i passaggi rimanenti fino a quando non viene creata la macchina virtuale e l'interfaccia della riga di comando non restituisce l'output.

## <a name="test-network-communication"></a>Testare la comunicazione di rete

Per testare la comunicazione di rete con Network Watcher è necessario prima abilitare un network watcher nell'area in cui si trova la macchina virtuale da testare, quindi usare la funzionalità Hop successivo di Network Watcher per testare la comunicazione.

### <a name="enable-network-watcher"></a>Abilitare Network Watcher

Se si dispone già di un Network Watcher abilitato nell'area Stati Uniti orientali, passare al paragrafo [Usare la funzionalità Hop successivo](#use-next-hop). Usare il comando [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) per creare un Network Watcher nell'area degli Stati Uniti orientali:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Usare la funzionalità Hop successivo

Azure crea automaticamente le route per le destinazioni predefinite. È possibile creare route personalizzate per eseguire l'override delle route predefinite. In alcuni casi, le route personalizzate possono generare un errore di comunicazione. Per testare il routing da una macchina virtuale, usare [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) per determinare l'hop successivo quando il traffico è destinato a un indirizzo specifico.

Testare le comunicazioni in uscita dalla macchina virtuale a uno degli indirizzi IP di www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Dopo alcuni secondi, l'output indica che **nextHopType** è **Internet** e che **routeTableId** è **System Route**. Questo risultato informa l'utente che è presente una route valida per la destinazione.

Testare le comunicazioni in uscita dalla macchina virtuale verso 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

L'output indica che **Nessuno** è **nextHopType** e che **routeTableId** è **System Route**. Questo risultato informa che, nonostante sia presente una route di sistema valida per la destinazione, non vi è alcun hop successivo per indirizzare il traffico alla destinazione.

## <a name="view-details-of-a-route"></a>Visualizzare i dettagli di una route

Per analizzare ulteriormente il routing, esaminare le route valide per l'interfaccia di rete con il comando [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table):

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Nell'output restituito è incluso il testo seguente:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Quando è stato usato il comando `az network watcher show-next-hop` per testare le comunicazioni in uscita verso 13.107.21.200 in [Usare la funzionalità Hop successivo](#use-next-hop), è stata usata la route con **addressPrefix** 0.0.0.0/0** per instradare il traffico all'indirizzo poiché nessun'altra route nell'output include l'indirizzo. Per impostazione predefinita, tutti gli indirizzi non specificati entro il prefisso dell'indirizzo di un'altra route vengono instradati su internet.

Quando è stato usato il comando `az network watcher show-next-hop` per testare le comunicazioni in uscita verso 172.31.0.100, tuttavia, il risultato ha indicato che non è presente alcun hop successivo. Nell'output restituito è visualizzato anche il testo seguente:

```azurecli
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Come si può notare nell'output del comando `az network watcher nic show-effective-route-table`, benché vi sia una route predefinita per il prefisso 172.16.0.0/12, che include l'indirizzo 172.31.0.100, il valore di **nextHopType** è **Nessuno**. Azure crea una route predefinita per 172.16.0.0/12, ma non specifica alcun tipo di hop successivo fino a quando non c'è un motivo per farlo. Se, ad esempio, è stato aggiunto l'intervallo di indirizzi 172.16.0.0/12 allo spazio di indirizzi della rete virtuale, Azure modifica **nextHopType** in **Rete virtuale** per la route. Il segno di spunta indica **Rete virtuale** come **nextHopType**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse e tutte le risorse in esso contenute non sono più necessari, è possibile usare [az group delete](/cli/azure/group#az-group-delete) per rimuoverli:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata una macchina virtuale ed è stato diagnosticato un problema di routing di rete dalla macchina virtuale. Si è appreso che Azure crea più route predefinite e testa il routing verso due destinazioni diverse. Per altre informazioni, vedere il [routing in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e come [creare route personalizzate](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Per le connessioni delle macchine virtuali in uscita è anche possibile determinare la latenza, il traffico di rete consentito e negato tra la macchina virtuale e un endpoint tramite la funzionalità di [risoluzione dei problemi di connessione](network-watcher-connectivity-cli.md) di Network Watcher. È possibile monitorare la comunicazione nel corso del tempo tra una macchina virtuale e un endpoint, ad esempio un indirizzo IP o un URL, usando la funzionalità di monitoraggio connessione di Network Watcher. Per informazioni vedere [Monitorare una connessione di rete](connection-monitor.md).