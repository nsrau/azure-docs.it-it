---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Filtrare il traffico di rete della VM | Documentazione Microsoft
description: Esempio di script dell'interfaccia della riga di comando di Azure - Filtrare il traffico di rete della VM in ingresso e in uscita.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: jdial
ms.openlocfilehash: 3eefbcb048799f783dca7471f879d566983e4c51
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753402"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-script-sample"></a>Esempio di script per filtrare il traffico di rete della VM in ingresso e in uscita

Questo script di esempio crea una rete virtuale con subnet front-end e back-end. Il traffico di rete in ingresso alla subnet front-end è limitato a HTTP, HTTPS e SSH, mentre il traffico in uscita verso Internet dalla subnet back-end non è consentito. Dopo aver eseguito lo script sarà presente una macchina virtuale con due NIC. Ogni NIC è collegata a una subnet diversa.

È possibile eseguire lo script da Azure [Cloud Shell](https://shell.azure.com/bash) o da un'installazione locale dell'interfaccia della riga di comando di Azure. Se si usa l'interfaccia della riga di comando in locale, per questo script è necessaria la versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](/cli/azure/network/vnet) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Consente di creare una subnet back-end. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Consente di associare i gruppi di risorse di rete alla subnet. |
| [az network public-ip create](/cli/azure/network/public-ip) | Consente di creare un indirizzo IP pubblico per accedere alla macchina virtuale da Internet. |
| [az network nic create](/cli/azure/network/nic) | Consente di creare interfacce di rete virtuale e di associarle alle subnet front-end e back-end della rete virtuale. |
| [az network nsg create](/cli/azure/network/nsg) | Consente di creare gruppi di sicurezza di rete associati alle subnet front-end e back-end. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Consente di creare regole del gruppo di sicurezza di rete che consentono o bloccano porte specifiche su subnet specifiche. |
| [az vm create](/cli/azure/vm) | Consente di creare macchine virtuali e associa una NIC a ogni VM. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione. |
| [az group delete](/cli/azure/group) | Consente di eliminare un gruppo di risorse e tutte le risorse in esso contenute. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della rete virtuale sono disponibili in [Virtual network CLI samples](../cli-samples.md) (Esempi di interfaccia della riga di comando della rete virtuale).
