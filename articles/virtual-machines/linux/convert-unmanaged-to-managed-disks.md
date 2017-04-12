---
title: Convertire una macchina virtuale Linux in Azure da dischi non gestiti a dischi gestiti | Microsoft Docs
description: Come convertire una macchina virtuale da dischi non gestiti ad Azure Managed Disks con l&quot;interfaccia della riga di comando di Azure 2.0
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8429ee543d8cf838765ff9e8624390746474ee0d
ms.lasthandoff: 04/03/2017

---

# <a name="how-to-convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Come convertire una macchina virtuale Linux da dischi non gestiti ad Azure Managed Disks

Se le macchine virtuali Linux di Azure esistenti di cui si dispone usano i dischi non gestiti negli account di archiviazione e si desidera sfruttare i vantaggi dei dischi gestiti, è possibile convertire le macchine virtuali. Questo processo consente di convertire sia il disco del sistema operativo che eventuali dischi dati collegati. Il processo di conversione richiede un riavvio della macchina virtuale, pertanto pianificare la migrazione delle macchine virtuali in una finestra di manutenzione preesistente. Il processo di migrazione non è reversibile. Assicurarsi di testare il processo di migrazione eseguendo la migrazione di una macchina virtuale di test prima di eseguire la migrazione nell'ambiente di produzione.

> [!IMPORTANT]
> Durante la conversione la macchina virtuale verrà deallocata. La macchina virtuale riceve un nuovo indirizzo IP quando viene avviata dopo la conversione. Se si dispone di una dipendenza su un indirizzo IP fisso, usare un indirizzo IP riservato.

Non è possibile convertire un disco non gestito in un disco gestito se il disco non gestito si trova in un account di archiviazione che è stato, anche in passato, crittografato con la [Crittografia del servizio di archiviazione di Azure (SSE)](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). La procedura seguente illustra come convertire dischi non gestiti che sono, o sono stati, in un account di archiviazione crittografato:

- Copiare il disco rigido virtuale con [az storage blob copy start](/cli/azure/storage/blob/copy#start) in un account di archiviazione che non è mai stato abilitato per la crittografia del servizio di archiviazione di Azure.
- Creare una macchina virtuale che usi dischi gestiti e specificare il file del disco rigido virtuale durante la creazione con [az vm create](/cli/azure/vm#create) o
- Collegare il disco rigido virtuale copiato con [az vm disk attach](/cli/azure/vm/disk#attach) a una macchina virtuale in esecuzione con dischi gestiti.

## <a name="convert-vm-to-azure-managed-disks"></a>Convertire una macchina virtuale ad Azure Managed Disks
In questa sezione viene descritto come convertire le macchine virtuali di Azure esistenti da dischi non gestiti a dischi gestiti. È possibile usare questo processo per passare dai dischi non gestiti Premium (SDD) ai dischi gestiti o dai dischi non gestiti standard (HDD) ai dischi gestiti standard.

> [!IMPORTANT]
> Dopo aver eseguito la procedura seguente, un solo BLOB in blocchi rimane nel contenitore predefinito/VHDS. Il nome del file è "VMName.xxxxxxx.status". Non eliminare questo oggetto di stato rimanente. Le prossime procedure mirano a risolvere questo problema.

1. Deallocare la macchina virtuale con [az vm deallocate](/cli/azure/vm#deallocate). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Convertire la macchina virtuale in dischi gestiti con [az vm convert](/cli/azure/vm#convert). Il processo seguente converte la macchina virtuale denominata `myVM`, incluso il disco del sistema operativo ed eventuali dischi dati:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Avviare la macchina virtuale dopo la conversione in dischi gestiti con [az vm start](/cli/azure/vm#start). L'esempio seguente avvia la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vm-in-an-availability-set-to-managed-disks"></a>Convertire le macchine virtuali in un set di disponibilità in dischi gestiti

Se le macchine virtuali che si desidera convertire in dischi gestiti si trovano in un set di disponibilità, è innanzitutto necessario convertire il set di disponibilità in un set di disponibilità gestito.

Tutte le macchine virtuali nel set di disponibilità devono essere deallocate prima di convertire il set di disponibilità. Pianificare la conversione di tutte le macchine virtuali in dischi gestiti dopo la conversione del set di disponibilità stesso in un set gestito. È quindi possibile avviare tutte le macchine virtuali e continuare a utilizzarle normalmente.

1. Elencare tutte le macchine virtuali in un set di disponibilità con [az vm availability-set list](/cli/azure/vm/availability-set#list). L'esempio seguente elenca tutte le macchine virtuali nel set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm availability-set show --resource-group myResourceGroup \
        --name myAvailabilitySet --query [virtualMachines[*].id] --output table
    ```

2. Deallocare tutte le macchine virtuali con [az vm deallocate](/cli/azure/vm#deallocate). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convertire il set di disponibilità con [az vm availability-set convert](/cli/azure/vm/availability-set#convert). L'esempio seguente converte il set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm availability-set convert --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Convertire tutte le macchine virtuali in dischi gestiti con [az vm convert](/cli/azure/vm#convert). Il processo seguente converte la macchina virtuale denominata `myVM`, incluso il disco del sistema operativo ed eventuali dischi dati:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Avviare tutte le macchine virtuali dopo la conversione in dischi gestiti con [az vm start](/cli/azure/vm#start). L'esempio seguente avvia la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle opzioni di archiviazione [Azure Managed Disks overview](../../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks).

