---
title: portale di Azure abilitare le chiavi gestite dal cliente con dischi gestiti da SSE
description: Abilitare le chiavi gestite dal cliente sui dischi gestiti tramite il portale di Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 1b40eef0a4cae8fa68631426ff72003db43d7530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88817620"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Usare il portale di Azure per abilitare la crittografia lato server con chiavi gestite dal cliente per Managed Disks

Archiviazione su disco di Azure consente di gestire chiavi personalizzate quando si usa la crittografia lato server (SSE) per Managed disks, se si sceglie. Per informazioni concettuali su SSE con chiavi gestite dal cliente, oltre ad altri tipi di crittografia del disco gestito, vedere la sezione **chiavi gestite dal cliente** dell'articolo crittografia del disco:

- Per Linux: [chiavi gestite dal cliente](./linux/disk-encryption.md#customer-managed-keys)
- Per Windows: [chiavi gestite dal cliente](./windows/disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Restrizioni

Per il momento, le chiavi gestite dal cliente presentano le restrizioni seguenti:

- Se questa funzionalità è abilitata per il disco, non è possibile disabilitarla.
    Se è necessario aggirare questo problema, è necessario copiare tutti i dati in un disco gestito completamente diverso che non usa chiavi gestite dal cliente:

    - Per Linux: [copiare un disco gestito](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Per Windows: [copiare un disco gestito](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- Sono supportate solo [le chiavi RSA software e HSM](../key-vault/keys/about-keys.md) di dimensioni 2.048 bit, 3.072 bit e 4.096 bit, senza altre chiavi o dimensioni.
    - Le chiavi [HSM](../key-vault/keys/hsm-protected-keys.md) richiedono il livello **Premium** di Azure Key Vault.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

Le sezioni seguenti illustrano come abilitare e usare chiavi gestite dal cliente per Managed disks:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

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

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata ai dischi gestiti non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere [Trasferimento di una sottoscrizione tra directory di Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i modelli di Azure Resource Manager per la creazione di dischi crittografati con chiavi gestite dal cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/general/overview.md)
- [Replicare le macchine virtuali con i dischi abilitati per le chiavi gestite dal cliente](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurare il ripristino di emergenza di VM VMware in Azure con PowerShell](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurare il ripristino di emergenza in Azure per le macchine virtuali Hyper-V tramite PowerShell e Azure Resource Manager](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
