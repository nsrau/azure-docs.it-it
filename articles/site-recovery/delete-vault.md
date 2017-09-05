---
title: Eliminare un insieme di credenziali di Site Recovery
description: Informazioni su come eliminare un insieme di credenziali di Azure Site Recovery, in base allo scenario di Site Recovery.
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: b95b9defa0a037f7d7d3ef36b99bc7c53c751050
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="delete-a-site-recovery-vault"></a>Eliminare un insieme di credenziali di Site Recovery
Le dipendenze possono impedire l'eliminazione di un insieme di credenziali di Azure Site Recovery. Le azioni da eseguire variano in base allo scenario di Site Recovery: VMware in Azure, Hyper-V (con e senza System Center Virtual Machine Manager) in Azure e Backup di Azure. Per eliminare un insieme di credenziali usato in Backup di Azure, vedere [Delete a Backup vault in Azure](../backup/backup-azure-delete-vault.md) (Eliminare un insieme di credenziali di Backup in Azure).

>[!Important]
>Se si esegue il test del prodotto e non si hanno problemi di perdita di dati, usare il metodo di eliminazione forzata per rimuovere rapidamente l'insieme di credenziali e tutte le relative dipendenze.

> Il comando di PowerShell elimina tutti i contenuti dell'insieme di credenziali e non è reversibile.

## <a name="use-powershell-to-force-delete-the-vault"></a>Usare PowerShell per forzare l'eliminazione dell'insieme di credenziali 

Per eliminare l'insieme di credenziali di Site Recovery anche se sono presenti elementi protetti, usare i comandi seguenti:

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault


## <a name="delete-a-site-recovery-vault"></a>Eliminare un insieme di credenziali di Site Recovery 
Per eliminare l'insieme di credenziali, seguire la procedura consigliata per lo scenario.

### <a name="vmware-vms-to-azure"></a>Da VM VMware ad Azure

1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una VM VMware o un server fisico](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Eliminare tutti i criteri di replica seguendo la procedura descritta in [Eliminare un criterio di replica](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3. Eliminare i riferimenti a vCenter seguendo la procedura descritta in [Eliminare un server vCenter in Azure Site Recovery](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).

4. Eliminare il server di configurazione seguendo la procedura descritta in [Rimozione delle autorizzazioni per un server di configurazione](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server).

5. Eliminare l'insieme di credenziali.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Macchine virtuali Hyper-V (con Virtual Machine Manager) in Azure
1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una VM VMware o un server fisico](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Eliminare tutti i criteri di replica seguendo la procedura descritta in [Eliminare un criterio di replica](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3.  Eliminare i riferimenti ai server Virtual Machine Manager seguendo la procedura descritta in [Annullare la registrazione di un server VMM connesso](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server).

4.  Eliminare l'insieme di credenziali.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Macchine virtuali Hyper-V (senza Virtual Machine Manager) in Azure
1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una VM VMware o un server fisico](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).

2. Eliminare tutti i criteri di replica seguendo la procedura descritta in [Eliminare un criterio di replica](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).

3. Eliminare i riferimenti ai server Hyper-V seguendo la procedura descritta in [Annullare la registrazione di un host Hyper-V in un sito di Hyper-V](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Eliminare il sito Hyper-V.

5. Eliminare l'insieme di credenziali.

