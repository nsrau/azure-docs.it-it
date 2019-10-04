---
title: Eliminare un insieme di credenziali di Servizi di ripristino configurato per il servizio Azure Site Recovery
description: Informazioni su come eliminare un insieme di credenziali di Servizi di ripristino configurato per il servizio Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a13dee2010688b02fd86fb05900826470a7d7a08
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876034"
---
# <a name="delete-a-site-recovery-services-vault"></a>Eliminare un insieme di credenziali di Servizi di ripristino per Site Recovery

Le dipendenze possono impedire l'eliminazione di un insieme di credenziali di Azure Site Recovery. Le azioni da eseguire variano a seconda dello scenario di ripristino del sito. Per eliminare un insieme di credenziali usato in Backup di Azure, vedere [Delete a Backup vault in Azure](../backup/backup-azure-delete-vault.md) (Eliminare un insieme di credenziali di Backup in Azure).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Eliminare un insieme di credenziali di Site Recovery 
Per eliminare l'insieme di credenziali, seguire la procedura consigliata per lo scenario.
### <a name="azure-vms-to-azure"></a>Da macchine virtuali Azure ad Azure

1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una VM VMware o un server fisico](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure).
2. Eliminare l'insieme di credenziali.

### <a name="vmware-vms-to-azure"></a>Da VM VMware ad Azure

1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una VM VMware o un server fisico](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Eliminare tutti i criteri di replica seguendo la procedura descritta in [Eliminare un criterio di replica](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Eliminare i riferimenti a vCenter seguendo la procedura descritta in [Eliminare un server vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Eliminare il server di configurazione seguendo la procedura descritta in [Rimozione delle autorizzazioni per un server di configurazione](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Eliminare l'insieme di credenziali.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Da VM Hyper-V (con VMM) ad Azure
1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una VM Hyper-V (con VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Annullare l'associazione ed eliminare tutti i criteri di replica passando all'insieme di credenziali -> **Infrastruttura di Site Recovery** -> **per System Center VMM** (Per System Center VMM) -> **Criteri di replica**

3.  Eliminare i riferimenti ai server VMM seguendo la procedura descritta in [Annullare la registrazione di un server VMM connesso](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Eliminare l'insieme di credenziali.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Macchine virtuali Hyper-V (senza Virtual Machine Manager) in Azure
1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una macchina virtuale Hyper-V (da Hyper-V ad Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Annullare l'associazione ed eliminare tutti i criteri di replica passando all'insieme di credenziali -> **Infrastruttura di Site Recovery** -> **For Hyper-V Sites** (Per siti Hyper-V) -> **Criteri di replica**

3. Eliminare i riferimenti ai server Hyper-V seguendo la procedura descritta in [Annullare la registrazione di un host Hyper-V in un sito di Hyper-V](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Eliminare il sito Hyper-V.

5. Eliminare l'insieme di credenziali.


## <a name="use-powershell-to-force-delete-the-vault"></a>Usare PowerShell per forzare l'eliminazione dell'insieme di credenziali 

> [!Important]
> Se si esegue il test del prodotto e non si hanno problemi di perdita di dati, usare il metodo di eliminazione forzata per rimuovere rapidamente l'insieme di credenziali e tutte le relative dipendenze.
> Il comando di PowerShell elimina tutti i contenuti dell'insieme di credenziali e **non è reversibile**.

Per eliminare l'insieme di credenziali di Site Recovery anche se sono presenti elementi protetti, usare i comandi seguenti:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Altre informazioni su [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)e [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
