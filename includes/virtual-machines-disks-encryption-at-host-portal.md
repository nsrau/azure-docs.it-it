---
title: includere il file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c5ea31217bd3088ec123281b36f8578b08ea25b2
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136236"
---
## <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Prerequisiti

Per poter usare la crittografia nell'host per le VM o i set di scalabilità di macchine virtuali, è necessario abilitare la funzionalità nella sottoscrizione. Inviare un messaggio di posta elettronica all'indirizzo encryptionAtHost@microsoft. com con gli ID sottoscrizione per ottenere la funzionalità abilitata per le sottoscrizioni.

Accedere al portale di Azure usando il [collegamento fornito](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Per accedere alla portale di Azure, è necessario utilizzare il [collegamento fornito](https://aka.ms/diskencryptionupdates) . La crittografia nell'host non è attualmente visibile nel portale di Azure pubblico senza usare il collegamento.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Creare una Azure Key Vault e un set di crittografia del disco

Quando la funzionalità è abilitata, è necessario configurare un Azure Key Vault e un set di crittografia del disco, se non è già stato fatto.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Distribuire una macchina virtuale

È necessario distribuire una nuova macchina virtuale per abilitare la crittografia nell'host, ma non può essere abilitata nelle macchine virtuali esistenti.

1. Cercare le **macchine virtuali** e selezionare **+ Aggiungi** per creare una macchina virtuale.
1. Creare una nuova macchina virtuale, selezionare un'area appropriata e una dimensione di macchina virtuale supportata.
1. Inserire gli altri valori nel pannello di **base** come si desidera, quindi passare al pannello **dischi** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="testo di esempio":::

1. Nel pannello **dischi** selezionare **Sì** per **crittografia nell'host**.
1. Eseguire le selezioni rimanenti desiderate.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="testo di esempio":::

1. Completare il processo di distribuzione della macchina virtuale, effettuare le selezioni adatte all'ambiente.

A questo punto è stata distribuita una macchina virtuale con la crittografia in host abilitata, tutti i dischi associati verranno crittografati usando la crittografia nell'host.