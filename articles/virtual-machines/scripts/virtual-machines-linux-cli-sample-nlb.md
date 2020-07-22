---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una VM Linux con bilanciamento del carico di rete
description: Esempio di script dell'interfaccia della riga di comando di Azure - Creare una VM Linux con bilanciamento del carico di rete
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 600d4bdab4c7f7038cafbb8605fa2490f3bc6788
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501382"
---
# <a name="create-a-highly-available-vm"></a>Creare una VM a disponibilità elevata

Questo script di esempio crea tutti gli elementi necessari per eseguire più macchine virtuali Ubuntu configurate in una configurazione a disponibilità elevata e con bilanciamento del carico. Dopo aver eseguito lo script, si disporrà di tre macchine virtuali, aggiunte a un set di disponibilità di Azure e accessibili tramite Azure Load Balancer.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale, il set di disponibilità, il bilanciamento del carico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](/cli/azure/network/vnet) | Consente di creare una rete virtuale e una subnet di Azure. |
| [az network public-ip create](/cli/azure/network/public-ip) | Consente di creare un indirizzo IP pubblico con un indirizzo IP statico e un nome DNS associato. |
| [az network lb create](/cli/azure/network/lb) | Consente di creare un bilanciamento del carico di rete di Azure (NLB). |
| [az network lb probe create](/cli/azure/network/lb/probe) | Consente di creare un probe di bilanciamento del carico di rete. Il probe di bilanciamento del carico di rete viene usato per monitorare ogni macchina virtuale nel set di bilanciamento del carico di rete. Se una macchina virtuale diventa inaccessibile, il traffico non viene indirizzato sulla macchina virtuale. |
| [az network lb rule create](/cli/azure/network/lb/rule) | Consente di creare una regola di bilanciamento del carico di rete. In questo esempio viene creata una regola per la porta 80. Poiché il traffico HTTP arriva al sistema di bilanciamento del carico di rete, viene indirizzato sulla porta 80 di una delle VM presenti nel set. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) | Consente di creare una regola Network Address Translation (NAT) di bilanciamento del carico di rete.  Le regole NAT mappano di una porta del bilanciamento del carico di rete su una porta in una macchina virtuale. In questo esempio viene creata una regola NAT per il traffico SSH in ogni macchina virtuale disponibile nel set di bilanciamento del carico di rete.  |
| [az network nsg create](/cli/azure/network/nsg) | Consente di creare un gruppo di sicurezza di rete, ovvero un confine di sicurezza tra Internet e la macchina virtuale. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Consente di creare una regola NSG per consentire il traffico in ingresso. In questo esempio, la porta 22 è aperta al traffico SSH. |
| [az network nic create](/cli/azure/network/nic) | Consente di creare una scheda di rete virtuale e la collega alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. |
| [az vm availability-set create](/cli/azure/network/lb/rule) | Consente di creare un set di disponibilità. I set di disponibilità garantiscono il tempo di attività dell'applicazione suddividendo le macchine virtuali in risorse fisiche in modo tale che, in caso di errore, non venga interessato l'intero set. |
| [az vm create](/cli/azure/vm/availability-set) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az group delete](/cli/azure/vm/extension) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
