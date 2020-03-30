---
title: Crittografia del disco di Azure per macchine virtuali e set di scalabilità di macchine virtualiAzure Disk Encryption for virtual machines and virtual machine scale sets
description: Questo articolo offre una panoramica di Crittografia disco di AzureThis article provides an overview of Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062120"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Crittografia del disco di Azure per macchine virtuali e set di scalabilità di macchine virtualiAzure Disk Encryption for virtual machines and virtual machine scale sets

La crittografia del disco di Azure può essere applicata sia a Linux che a windows, nonché ai set di scalabilità delle macchine virtuali. 

## <a name="linux-virtual-machines"></a>Macchine virtuali Linux

Gli articoli seguenti forniscono indicazioni per la crittografia di macchine virtuali Linux.The following articles provide guidance for encrypting Linux virtual machines.

### <a name="current-version-of-azure-disk-encryption"></a>Versione corrente di Crittografia disco di AzureCurrent version of Azure Disk Encryption

- [Panoramica della crittografia dischi di Azure per le macchine virtuali Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Scenari di crittografia dischi di Azure per macchine virtuali Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Creare e crittografare una macchina virtuale Linux con Azure PowerShellCreate and encrypt a Linux VM with Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Creare e crittografare una macchina virtuale Linux con il portale di Azure](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption Extension Schema for Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Script di esempio per la crittografia dischi di Azure](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Crittografia dischi di Azure: domande frequenti](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Crittografia del disco di Azure con Azure AD (versione precedente)Azure disk encryption with Azure AD (previous version)

- [Panoramica di Crittografia disco di Azure con macchine virtuali di Azure AD per LinuxOverview of Azure Disk Encryption with Azure AD for Linux virtual machines](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure Disk Encryption with Azure AD scenarios on Linux VMs](../../virtual-machines/linux/disk-encryption-linux.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure con Azure AD (versione precedente)Creating and configuring a key vault for Azure Disk Encryption with Azure AD (previous release)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Macchine virtuali Windows

Negli articoli seguenti vengono fornite indicazioni per la crittografia delle macchine virtuali Windows.The following articles provide guidance for encrypting Windows virtual machines.

### <a name="current-version-of-azure-disk-encryption"></a>Versione corrente di Crittografia disco di AzureCurrent version of Azure Disk Encryption

- [Panoramica della crittografia dischi di Azure per le macchine virtuali Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Scenari di crittografia dischi di Azure per macchine virtuali Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Creare e crittografare una macchina virtuale Windows con l'interfaccia della riga di comando di Azure](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Creare e crittografare una macchina virtuale Windows con Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Creare e crittografare una macchina virtuale Windows con il portale di Azure](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption Extension Schema for Windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Script di esempio per la crittografia dischi di Azure](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Crittografia dischi di Azure: domande frequenti](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Crittografia del disco di Azure con Azure AD (versione precedente)Azure disk encryption with Azure AD (previous version)

- [Panoramica di Crittografia disco di Azure con Azure AD per macchine virtuali WindowsOverview of Azure Disk Encryption with Azure AD for Windows virtual machines](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption with Azure AD scenarios on Windows VMs](../../virtual-machines/windows/disk-encryption-windows.md)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure con Azure AD (versione precedente)Creating and configuring a key vault for Azure Disk Encryption with Azure AD (previous release)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>set di scalabilità di macchine virtuali

Negli articoli seguenti vengono fornite indicazioni per la crittografia dei set di scalabilità delle macchine virtuali.

- [Panoramica di Crittografia disco di Azure per set di scalabilità di macchine virtualiOverview of Azure Disk Encryption for virtual machine scale sets](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Crittografare set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- Crittografare un set di scalabilità di [macchine virtuali usando Azure Powershell.Encrypt](../../virtual-machine-scale-sets/disk-encryption-powershell.md)a virtual machine scale sets using Azure Powershell .
- [Crittografare un set di scalabilità di macchine virtuali usando Azure Resource ManagerEncrypt a virtual machine scale sets using the Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Creare e configurare un insieme di credenziali delle chiavi per Crittografia dischi di Azure](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Usare Crittografia dischi di Azure con sequenziazione delle estensioni del set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della crittografia di Azure](encryption-overview.md)
- [Crittografia dei dati inattivi](encryption-atrest.md)
- [Procedure consigliate per la sicurezza e la crittografia dei dati](data-encryption-best-practices.md)
