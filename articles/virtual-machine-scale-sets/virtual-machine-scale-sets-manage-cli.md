---
title: Gestire set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Comandi comuni dell'interfaccia della riga di comando di Azure per la gestione dei set di scalabilità di macchine virtuali, ad esempio per l'avvio e l'arresto di un'istanza o la modifica della capacità del set di scalabilità.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 014e6ff2bc769675e47743cfdd4fbf7fc6234974
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999835"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Gestione di un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure
Nel ciclo di vita del set di scalabilità di una macchina virtuale potrebbe essere necessario eseguire una o più attività di gestione. Si potrebbe anche voler creare script per automatizzare le attività di ciclo di vita. Questo articolo descrive alcuni comandi comuni dell'interfaccia della riga di comando di Azure che consentono di eseguire queste attività.

Per completare queste attività di gestione è necessaria la versione più recente dell'interfaccia della riga di comando di Azure. Per informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se è necessario creare un set di scalabilità di macchine virtuali, è possibile [creare un set di scalabilità con l'interfaccia della riga di comando di Azure](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Visualizzare informazioni su un set di scalabilità
Per visualizzare le informazioni generali su un set di scalabilità, usare [az vmss show](/cli/azure/vmss#az_vmss_show). Nell'esempio seguente si ottengono informazioni su un set di scalabilità denominato *myScaleSet* nel gruppo di risorse *myResourceGroup*. Immettere i nomi personalizzati nel modo seguente:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Visualizzare le macchine virtuali in un set di scalabilità
Per visualizzare l'elenco delle istanze di VM in un set di scalabilità, usare [az vmss list-instances](/cli/azure/vmss#list-instances). L'esempio seguente elenca tutte le istanze di VM presenti nel set di scalabilità denominato *myScaleSet* nel gruppo di risorse *myResourceGroup*. Specificare valori personalizzati per questi nomi:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Per visualizzare altre informazioni su un'istanza di VM specifica, aggiungere il parametro `--instance-id` a [az vmss get-instance-view](/cli/azure/vmss#get-instance-view) e specificare un'istanza da visualizzare. L'esempio seguente visualizza informazioni su un'istanza di VM *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Immettere i nomi personalizzati nel modo seguente:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Visualizzare le informazioni di connessione per le macchine virtuali
Per connettersi alle macchine virtuali in un set di scalabilità, viene usato il protocollo SSH o RDP per connettersi a un indirizzo IP pubblico assegnato e al numero di porta. Per impostazione predefinita, le regole Network Address Translation (NAT) vengono aggiunte al servizio Azure Load Balancer che inoltra il traffico della connessione remota alle singole macchine virtuali. Per visualizzare l'indirizzo e le porte per la connessione alle istanze di VM in un set di scalabilità, usare [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info). L'esempio seguente visualizza le informazioni di connessione per le istanze di VM presenti nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare valori personalizzati per questi nomi:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Modificare la capacità di un set di scalabilità
Con i comandi precedenti vengono visualizzate informazioni sul set di scalabilità e sulle istanze di VM. Per aumentare o diminuire il numero di istanze in un set di scalabilità è possibile modificare la capacità. Il set di scalabilità crea o rimuove il numero necessario di VM, quindi configura le VM per la ricezione del traffico dell'applicazione.

Per visualizzare il numero di istanze attualmente presenti in un set di scalabilità, usare il comando [az vmss show](/cli/azure/vmss#az_vmss_show) ed eseguire una query su *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

È possibile aumentare o ridurre manualmente il numero di macchine virtuali nel set di scalabilità con il comando [az vmss scale](/cli/azure/vmss#az_vmss_scale). L'esempio seguente imposta il numero di VM del set di scalabilità su *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Sono necessari alcuni minuti per aggiornare la capacità del set di scalabilità. Quando si riduce la capacità di un set di scalabilità, vengono rimosse prima le macchine virtuali con l'ID istanza più elevato.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Arrestare e avviare le macchine virtuali in un set di scalabilità
Per arrestare una o più macchine virtuali in un set di scalabilità, usare [az vmss stop](/cli/azure/vmss#az-vmss-stop). Il parametro `--instance-ids` consente di specificare una o più macchine virtuali da arrestare. Se non si specifica un ID istanza, vengono arrestate tutte le macchine virtuali del set di scalabilità. Per arrestare più macchine virtuali, separare gli ID istanza con uno spazio.

L'esempio seguente arresta l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Le macchine virtuali arrestate rimangono allocate e continuano a essere conteggiate nell'addebito. Se si vuole invece deallocare le macchine virtuali e mantenere solo i costi per l'archiviazione, usare [az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate). Per deallocare più macchine virtuali, separare gli ID istanza con uno spazio. L'esempio seguente arresta e dealloca l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Avviare le macchine virtuali in un set di scalabilità
Per avviare una o più macchine virtuali in un set di scalabilità, usare [az vmss start](/cli/azure/vmss#az_vmss_start). Il parametro `--instance-ids` consente di specificare una o più macchine virtuali da avviare. Se non si specifica un ID istanza, vengono avviate tutte le macchine virtuali del set di scalabilità. Per avviare più macchine virtuali, separare gli ID istanza con uno spazio.

L'esempio seguente avvia l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Riavviare le macchine virtuali in un set di scalabilità
Per riavviare una o più macchine virtuali in un set di scalabilità, usare [az vmss restart](/cli/azure/vmss#az_vmss_restart). Il parametro `--instance-ids` consente di specificare una o più macchine virtuali da riavviare. Se non si specifica un ID istanza, vengono riavviate tutte le macchine virtuali del set di scalabilità. Per riavviare più macchine virtuali, separare gli ID istanza con uno spazio.

L'esempio seguente riavvia l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Rimuovere le macchine virtuali da un set di scalabilità
Per rimuovere una o più macchine virtuali in un set di scalabilità, usare [az vmss delete-instances](/cli/azure/vmss#delete-instances). Il parametro `--instance-ids` consente di specificare una o più VM da rimuovere. Se si specifica * per l'ID istanza, vengono rimosse tutte le macchine virtuali del set di scalabilità. Per rimuovere più macchine virtuali, separare gli ID istanza con uno spazio.

L'esempio seguente rimuove l'istanza *0* nel set di scalabilità denominato *myScaleSet* e nel gruppo di risorse *myResourceGroup*. Specificare i valori personalizzati nel modo seguente:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Passaggi successivi
Altre attività comuni per i set di scalabilità includono la [distribuzione di un'applicazione](virtual-machine-scale-sets-deploy-app.md) e l'[aggiornamento delle istanze di VM](virtual-machine-scale-sets-upgrade-scale-set.md). È anche possibile usare l'interfaccia della riga di comando di Azure per [configurare regole di scalabilità automatica](virtual-machine-scale-sets-autoscale-overview.md).
