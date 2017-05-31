---
title: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Instradare il traffico attraverso un&quot;appliance virtuale di rete | Documentazione Microsoft
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Instradare il traffico attraverso un&quot;appliance virtuale di rete.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e1ab4fd899b31eba3929b8c78e04bc8c09f39e14
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---

# <a name="route-traffic-through-a-network-virtual-appliance"></a>Instradare il traffico attraverso un'appliance virtuale di rete

Questo script di esempio crea una rete virtuale con subnet front-end e back-end. Crea anche una VM con inoltro IP attivato per instradare il traffico tra le due subnet. Dopo aver eseguito lo script è possibile distribuire il software di rete, ad esempio un'applicazione firewall, nella VM.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio


[!code-azurecli[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Instradare il traffico attraverso un'appliance virtuale di rete")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](/cli/azure/network/vnet#create) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet#create) | Consente di creare le subnet back-end e di rete perimetrale. |
| [az network public-ip create](/cli/azure/network/public-ip#create) | Consente di creare un indirizzo IP pubblico per accedere alla VM da Internet. |
| [az network nic create](/cli/azure/network/nic#create) | Consente di creare un'interfaccia di rete virtuale e attiva l'inoltro IP. |
| [az network nsg create](/cli/azure/network/nsg#create) | Consente di creare un gruppo di sicurezza di rete. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#create) | Consente di creare regole del gruppo di sicurezza di rete per le porte HTTP e HTTPS in ingresso alla VM. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#update)| Consente di associare il gruppo di sicurezza di rete e le tabelle di route alle subnet. |
| [az network route-table create](/cli/azure/network/route-table#create)| Consente di creare una tabella di route per tutte le route. |
| [az network route-table route create](/cli/azure/network/route-table/route#create)| Consente di creare le route per instradare il traffico tra subnet e Internet attraverso la VM. |
| [az vm create](/cli/azure/vm#create) | Consente di creare una macchina virtuale e vi connette la NIC. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione. |
| [az group delete](/cli/azure/group#delete) | Consente di eliminare un gruppo di risorse e tutte le risorse in esso contenute. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando per la rete sono disponibili nella documentazione sulla [Panoramica delle reti di Azure](../cli-samples.md).
