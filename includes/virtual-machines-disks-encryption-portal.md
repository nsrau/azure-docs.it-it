---
title: includere il file
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177032"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Distribuire una macchina virtuale

Ora che è stato creato e configurato l'insieme di credenziali delle chiavi e il set di crittografia del disco, è possibile distribuire una VM usando la crittografia.
Il processo di distribuzione delle VM è simile al processo di distribuzione standard. le uniche differenze sono la necessità di distribuire la macchina virtuale nella stessa area delle altre risorse e di scegliere di usare una chiave gestita dal cliente.

1. Cercare le **macchine virtuali** e selezionare **+ Aggiungi** per creare una macchina virtuale.
1. Nel pannello di **base** selezionare la stessa area del set di crittografia del disco e Azure Key Vault.
1. Inserire gli altri valori nel pannello di **base** come si desidera.

    ![Screenshot dell'esperienza di creazione della macchina virtuale con il valore Region evidenziato.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Nel pannello **dischi** selezionare crittografia inattiva **con una chiave gestita dal cliente**.
1. Selezionare la crittografia del disco impostata nell'elenco a discesa del **set di crittografia del disco** .
1. Eseguire le selezioni rimanenti desiderate.

    ![Screenshot dell'esperienza di creazione delle VM, il pannello dischi. Con l'elenco a discesa impostazione crittografia disco evidenziato.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Abilita su un disco esistente

> [!CAUTION]
> Per abilitare la crittografia dei dischi in qualsiasi disco collegato a una macchina virtuale, sarà necessario arrestare la macchina virtuale.
    
1. Passare a una macchina virtuale che si trova nella stessa area di uno dei set di crittografia del disco.
1. Aprire la macchina virtuale e selezionare **Arresta**.

    ![Screenshot della sovrimpressione principale per la VM di esempio, con il pulsante Interrompi evidenziato.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Al termine dell'arresto della macchina virtuale, selezionare **dischi** e quindi selezionare il disco che si desidera crittografare.

    ![Screenshot della VM di esempio con il pannello dischi aperto. Il disco del sistema operativo è evidenziato, come un disco di esempio da selezionare.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Selezionare **crittografia** e selezionare crittografia inattiva **con una chiave gestita dal cliente** , quindi selezionare la crittografia del disco impostata nell'elenco a discesa.
1. Selezionare **Salva**.

    ![Screenshot del disco del sistema operativo di esempio. Il pannello crittografia è aperto, la crittografia dei computer inattivi con una chiave gestita dal cliente è selezionata e l'esempio Azure Key Vault. Dopo aver effettuato queste selezioni, viene selezionato il pulsante Salva.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Ripetere questo processo per tutti gli altri dischi collegati alla macchina virtuale che si vuole crittografare.
1. Al termine del trasferimento dei dischi alle chiavi gestite dal cliente, se non sono presenti altri dischi collegati che si vuole crittografare, è possibile avviare la macchina virtuale.
