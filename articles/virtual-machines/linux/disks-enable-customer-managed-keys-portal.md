---
title: portale di Azure abilitare le chiavi gestite dal cliente con dischi gestiti da SSE
description: Abilitare le chiavi gestite dal cliente sui dischi gestiti tramite il portale di Azure.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0128d2b88d6eb2464ef95824330cfab07ee3703d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235991"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Abilitare le chiavi gestite dal cliente con la crittografia lato server-Managed disks-Portal

Archiviazione su disco di Azure consente di gestire chiavi personalizzate quando si usa la crittografia lato server (SSE) per Managed disks, se si sceglie. Per informazioni concettuali su SSE con chiavi gestite dal cliente, oltre ad altri tipi di crittografia del disco gestito, vedere la sezione [chiavi gestite dal cliente](disk-encryption.md#customer-managed-keys) dell'articolo crittografia del disco.

## <a name="restrictions"></a>Restrizioni

Per il momento, le chiavi gestite dal cliente presentano le restrizioni seguenti:

- Se questa funzionalità è abilitata per il disco, non è possibile disabilitarla.
    Se è necessario risolvere questo problema, occorre [copiare tutti i dati](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) in un disco gestito completamente diverso che non usa chiavi gestite dal cliente.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata ai dischi gestiti non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere [Trasferimento di una sottoscrizione tra directory di Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i modelli di Azure Resource Manager per la creazione di dischi crittografati con chiavi gestite dal cliente](https://github.com/ramankumarlive/manageddiskscmkpreview))
- [Replicare le macchine virtuali con i dischi abilitati per le chiavi gestite dal cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurare il ripristino di emergenza di VM VMware in Azure con PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurare il ripristino di emergenza in Azure per le macchine virtuali Hyper-V tramite PowerShell e Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
