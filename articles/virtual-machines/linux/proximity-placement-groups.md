---
title: Usare i gruppi di posizionamento di prossimità per le macchine virtuali LinuxUse proximity placement groups for Linux VMs
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità per le macchine virtuali Linux in Azure.Learn about creating and using proximity placement groups for Linux virtual machines in Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 353a266b647c299515c15889c302ba4409aa511b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73171045"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Distribuire macchine virtuali a gruppi di posizionamento di prossimità usando l'interfaccia della riga di comando di AzureDeploy VMs to proximity placement groups using Azure CLI

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è necessario distribuirle all'interno di un gruppo di [posizionamento di prossimità.](co-location.md#proximity-placement-groups)

Un gruppo di posizionamento di prossimità è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure si trovino fisicamente vicine l'una all'altra. I gruppi di posizionamento di prossimità sono utili per i carichi di lavoro in cui una bassa latenza è un requisito.


## <a name="create-the-proximity-placement-group"></a>Creare il gruppo di posizionamento di prossimità
Creare un gruppo di [`az ppg create`](/cli/azure/ppg#az-ppg-create)posizionamento di prossimità utilizzando . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Elenca gruppi di posizionamento di prossimità

È possibile elencare tutti i gruppi di posizionamento di prossimità utilizzando [az ppg list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale all'interno del gruppo di posizionamento di prossimità usando [new az vm](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

È possibile visualizzare la macchina virtuale nel gruppo di posizionamento di prossimità usando [az ppg show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>SET DI DISPONIBILITÀ
È anche possibile creare un set di disponibilità nel gruppo di posizionamento di prossimità. Usare lo `--ppg` stesso parametro con [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) per creare un set di disponibilità e tutte le macchine virtuali nel set di disponibilità verranno create anche nello stesso gruppo di posizionamento di prossimità.

## <a name="scale-sets"></a>Set di scalabilità

Puoi anche creare un set di scalabilità nel tuo gruppo di posizionamento di prossimità. Usare lo `--ppg` stesso parametro con [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) per creare un set di scalabilità e tutte le istanze verranno create nello stesso gruppo di posizionamento di prossimità.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui comandi [dell'interfaccia della riga](/cli/azure/ppg) di comando di Azure per i gruppi di posizionamento di prossimità.
