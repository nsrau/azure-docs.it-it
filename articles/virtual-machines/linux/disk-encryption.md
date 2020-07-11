---
title: Crittografia lato server di Azure Managed Disks - Interfaccia della riga di comando di Azure
description: Archiviazione di Azure protegge i dati crittografandoli nello stato inattivo prima di renderli persistenti nei cluster di archiviazione. È possibile basarsi sulle chiavi gestite da Microsoft per la crittografia dei dischi gestiti oppure usare chiavi gestite dal cliente per gestire la crittografia con chiavi personalizzate.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: c78fa51150ba09e5b72eeb0587679f779c947acf
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231052"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Crittografia lato server di archiviazione su disco di Azure

La crittografia lato server (SSE) protegge i dati e consente di soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, SSE crittografa automaticamente i dati archiviati in dischi gestiti di Azure (dischi del sistema operativo e dischi dati) per impostazione predefinita quando vengono salvati in modo permanente nel cloud. 

I dati nei dischi gestiti di Azure sono crittografati in modo trasparente con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide disponibili, conforme a FIPS 140-2. Per altre informazioni sui moduli crittografici sottostanti i dischi gestiti di Azure, vedere [API Cryptography Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

La crittografia lato server non influisce sulle prestazioni dei dischi gestiti e non sono previsti costi aggiuntivi. 

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia del disco gestito oppure è possibile gestire la crittografia usando le proprie chiavi. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dati presenti nei dischi gestiti. 

Le sezioni seguenti descrivono in modo più dettagliato tutte le opzioni per la gestione delle chiavi.

### <a name="platform-managed-keys"></a>Chiavi gestite dalla piattaforma

Per impostazione predefinita, i dischi gestiti usano chiavi di crittografia gestite dalla piattaforma. A partire dal 10 giugno 2017, tutti i nuovi dischi gestiti, snapshot, immagini e nuovi dati scritti nei dischi gestiti esistenti vengono automaticamente crittografati mentre sono inattivi con le chiavi gestite dalla piattaforma.

### <a name="customer-managed-keys"></a>Chiavi gestite dal cliente

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Restrizioni

Per il momento, le chiavi gestite dal cliente presentano le restrizioni seguenti:

- Se questa funzionalità è abilitata per il disco, non è possibile disabilitarla.
    Se è necessario risolvere questo problema, occorre [copiare tutti i dati](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) in un disco gestito completamente diverso che non usa chiavi gestite dal cliente.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata a Managed Disks non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere [Trasferimento di una sottoscrizione tra directory di Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Crittografia in host-end-to-end Encryption per i dati della VM

Quando si Abilita la crittografia nell'host, la crittografia viene avviata nell'host della macchina virtuale, il server Azure a cui è allocata la macchina virtuale. I dati per il disco temporaneo e le cache del disco del sistema operativo/dati vengono archiviati nell'host VM. Dopo aver abilitato la crittografia nell'host, tutti questi dati vengono crittografati inattivi e flussi crittografati nel servizio di archiviazione, dove vengono salvati in modo permanente. In sostanza, la crittografia nell'host crittografa i dati da end a end. La crittografia nell'host non usa la CPU della macchina virtuale e non influisca sulle prestazioni della VM. 

I dischi temporanei vengono crittografati a riposo con chiavi gestite dalla piattaforma quando si Abilita la crittografia end-to-end. Le cache del sistema operativo e del disco dati vengono crittografate a riposo con chiavi gestite dal cliente o gestite dalla piattaforma, a seconda del tipo di crittografia del disco selezionato. Se, ad esempio, un disco è crittografato con chiavi gestite dal cliente, la cache per il disco viene crittografata con le chiavi gestite dal cliente e se un disco viene crittografato con le chiavi gestite dalla piattaforma, la cache per il disco viene crittografata con le chiavi gestite dalla piattaforma.

### <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Crittografia a doppio inattivo

I clienti sensibili alla sicurezza elevata interessati dal rischio associato a un algoritmo di crittografia specifico, un'implementazione o una chiave compromessa possono ora optare per un ulteriore livello di crittografia usando un algoritmo/modalità di crittografia diverso a livello di infrastruttura usando chiavi di crittografia gestite dalla piattaforma. Questo nuovo livello può essere applicato a dischi, snapshot e immagini, ognuno dei quali verrà crittografato a riposo con crittografia doppia.

### <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Crittografia lato server e Crittografia dischi di Azure

[Crittografia dischi di Azure](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) sfrutta la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della macchina virtuale guest.  La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare la crittografia nell'host](disks-enable-host-based-encryption-cli.md)
- [INTERFACCIA della riga di comando di Azure-abilitare la crittografia doppia sui dischi gestiti di Rest](disks-enable-double-encryption-at-rest-cli.md)
- [Abilitare le chiavi gestite dal cliente per Managed disks-CLI](disks-enable-customer-managed-keys-cli.md)
- [Abilitare le chiavi gestite dal cliente per Managed disks-portale](disks-enable-customer-managed-keys-portal.md)
- [Abilitare le chiavi gestite dal cliente per il disco gestito-PowerShell](../windows/disks-enable-customer-managed-keys-powershell.md)
- [Che cos'è Azure Key Vault?](../../key-vault/general/overview.md)
