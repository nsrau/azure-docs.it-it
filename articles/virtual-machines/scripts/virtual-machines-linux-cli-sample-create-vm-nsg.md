---
title: "Esempio dell'interfaccia della riga di comando: Creare due VM con un gruppo di sicurezza di rete interno e uno esterno"
description: Creare due macchine virtuali con un gruppo di sicurezza di rete interno e uno esterno per proteggere il traffico di rete tramite l'interfaccia della riga di comando di Azure.
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
ms.openlocfilehash: b4339ab2c512a96614158f673cb07b60184f5f71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459951"
---
# <a name="secure-network-traffic-between-virtual-machines-using-an-nsg"></a>Proteggere il traffico di rete tra macchine virtuali con un gruppo di sicurezza di rete

Questo script crea due macchine virtuali e protegge il traffico in ingresso su entrambe le macchine. Una macchina virtuale è accessibile su Internet e dispone di un gruppo di sicurezza di rete (NSG) configurato per consentire il traffico sulle porte 22 e 80. La seconda macchina virtuale non è accessibile su Internet e dispone di un NSG configurato per consentire solo il traffico proveniente dalla prima macchina virtuale.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Consente di creare una rete virtuale e una subnet di Azure. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | Consente di creare una subnet. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Restituisce informazioni sulla regola del gruppo di sicurezza di rete. In questo esempio, il nome della regola è archiviato in una variabile da usare successivamente nello script. |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Consente di aggiornare una regola NSG. In questo esempio la regola di back-end viene aggiornata affinché il traffico passi solo attraverso la subnet front-end. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
