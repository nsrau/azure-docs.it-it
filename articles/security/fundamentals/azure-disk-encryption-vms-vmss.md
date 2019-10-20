---
title: Crittografia dischi di Azure per macchine virtuali e set di scalabilità di macchine virtuali
description: Questo articolo fornisce una panoramica di crittografia dischi di Azure
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599955"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Crittografia dischi di Azure per macchine virtuali e set di scalabilità di macchine virtuali

Crittografia dischi di Azure può essere applicata alle macchine virtuali Linux e Windows, oltre che ai set di scalabilità di macchine virtuali. 

## <a name="linux-virtual-machines"></a>Macchine virtuali Linux

Gli articoli seguenti forniscono indicazioni per la crittografia delle macchine virtuali Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Versione corrente di crittografia dischi di Azure

- [Panoramica di crittografia dischi di Azure per macchine virtuali Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Scenari di crittografia dischi di Azure nelle macchine virtuali Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Creare e crittografare una VM Linux con l'interfaccia della riga di comando di Azure](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Creare e crittografare una VM Linux con Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Creare e crittografare una VM Linux con il portale di Azure](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Script di esempio di crittografia dischi di Azure](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Risoluzione dei problemi di crittografia dischi di Azure](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Domande frequenti su crittografia dischi di Azure](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Crittografia dischi di Azure con Azure AD (versione precedente)

- [Panoramica di crittografia dischi di Azure con Azure AD per le macchine virtuali Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Crittografia dischi di Azure con scenari di Azure AD in macchine virtuali Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure AD (versione precedente)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Macchine virtuali Windows

Gli articoli seguenti forniscono indicazioni per la crittografia delle macchine virtuali Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Versione corrente di crittografia dischi di Azure

- [Panoramica di crittografia dischi di Azure per macchine virtuali Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Scenari di crittografia dischi di Azure nelle macchine virtuali Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Creare e crittografare una VM Windows con l'interfaccia della riga di comando di Azure](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Creare e crittografare una macchina virtuale Windows con Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Creare e crittografare una macchina virtuale Windows con il portale di Azure](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Script di esempio di crittografia dischi di Azure](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Risoluzione dei problemi di crittografia dischi di Azure](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Domande frequenti su crittografia dischi di Azure](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Crittografia dischi di Azure con Azure AD (versione precedente)

- [Panoramica di crittografia dischi di Azure con Azure AD per le macchine virtuali Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Crittografia dischi di Azure con scenari di Azure AD nelle VM Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure AD (versione precedente)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Set di scalabilità di macchine virtuali

Gli articoli seguenti forniscono indicazioni per la crittografia dei set di scalabilità di macchine virtuali.

- [Panoramica di crittografia dischi di Azure per i set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Crittografare un set di scalabilità di macchine virtuali usando l'interfaccia della riga di comando](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Crittografare i set di scalabilità di macchine virtuali tramite Azure PowerShell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Crittografare i set di scalabilità di macchine virtuali usando il Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Creare e configurare un insieme di credenziali delle chiavi per crittografia dischi di Azure](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Usare crittografia dischi di Azure con la sequenziazione delle estensioni del set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della crittografia di Azure](encryption-overview.md)
- [Crittografia dei dati inattivi](encryption-atrest.md)
- [Procedure consigliate per la sicurezza e la crittografia dei dati](data-encryption-best-practices.md)
