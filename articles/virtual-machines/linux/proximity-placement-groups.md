---
title: Usare l'anteprima dei gruppi di posizionamento vicino per le macchine virtuali Linux | Microsoft Docs
description: Informazioni sulla creazione e l'uso di gruppi di posizionamento di prossimità per macchine virtuali Linux in Azure.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 0d44e38343d6f7113b296b57353080e2de068bb6
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278291"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Anteprima: Distribuire macchine virtuali in gruppi di posizionamento con prossimità usando l'interfaccia della riga di comando

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un [gruppo di posizionamento di prossimità](co-location.md#preview-proximity-placement-groups).

Un gruppo di posizionamento vicino è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure siano posizionate fisicamente tra loro. I gruppi di posizionamento prossimità sono utili per i carichi di lavoro in cui è necessario un livello di latenza bassa.

> [!IMPORTANT]
> I gruppi di posizionamento di prossimità sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> I gruppi di posizionamento di prossimità non sono disponibili in queste aree durante l'anteprima: **Giappone orientale**, **Australia orientale** e **India centrale**.

## <a name="create-the-proximity-placement-group"></a>Creare il gruppo di posizionamento vicino
Creare un gruppo di posizionamento di [`az ppg create`](/cli/azure/ppg#az-ppg-create)prossimità usando. 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Elenca gruppi di posizionamento prossimità

È possibile elencare tutti i gruppi di posizionamento vicini usando [AZ PPG list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale all'interno del gruppo di posizionamento di prossimità usando il [nuovo AZ VM](/cli/azure/vm#az-vm-create).
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

## <a name="availability-sets"></a>SET DI DISPONIBILITÀ
È anche possibile creare un set di disponibilità nel gruppo di posizionamento di prossimità. Usare lo stesso `--ppg` parametro con il comando [AZ VM Availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) per creare un set di disponibilità e tutte le VM nel set di disponibilità verranno create anche nello stesso gruppo di posizionamento di prossimità.

## <a name="scale-sets"></a>Set di scalabilità

È anche possibile creare un set di scalabilità nel gruppo di posizionamento di prossimità. Usare lo stesso `--ppg` parametro con il comando [AZ vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) per creare un set di scalabilità e tutte le istanze verranno create nello stesso gruppo di posizionamento di prossimità.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui comandi dell'interfaccia della riga di comando di [Azure](/cli/azure/ppg) per i gruppi di posizionamento prossimità.
