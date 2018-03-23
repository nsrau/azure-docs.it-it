---
title: Eliminare un insieme di credenziali di Site Recovery
description: Informazioni su come eliminare un insieme di credenziali di Azure Site Recovery, in base allo scenario di Site Recovery.
service: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 89ab1e7c8b2fa0f4014ecfa0e677b398e601e6fa
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="delete-a-site-recovery-vault"></a>Eliminare un insieme di credenziali di Site Recovery
Le dipendenze possono impedire l'eliminazione di un insieme di credenziali di Azure Site Recovery. Le azioni da eseguire variano in base allo scenario di Site Recovery: VMware in Azure, Hyper-V (con e senza System Center Virtual Machine Manager) in Azure e Backup di Azure. Per eliminare un insieme di credenziali usato in Backup di Azure, vedere [Delete a Backup vault in Azure](../backup/backup-azure-delete-vault.md) (Eliminare un insieme di credenziali di Backup in Azure).



## <a name="delete-a-site-recovery-vault"></a>Eliminare un insieme di credenziali di Site Recovery 
Per eliminare l'insieme di credenziali, seguire la procedura consigliata per lo scenario.

### <a name="vmware-vms-to-azure"></a>Da VM VMware ad Azure

1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una VM VMware o un server fisico](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Eliminare tutti i criteri di replica seguendo la procedura descritta in [Eliminare un criterio di replica](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Eliminare i riferimenti a vCenter seguendo la procedura descritta in [Eliminare un server vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Eliminare il server di configurazione seguendo la procedura descritta in [Rimozione delle autorizzazioni per un server di configurazione](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Eliminare l'insieme di credenziali.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Macchine virtuali Hyper-V (con Virtual Machine Manager) in Azure
1. Eliminare tutte le macchine virtuali protette seguendo la procedura in [Disabilitare la protezione per una macchina virtuale Hyper-V con replica in Azure con lo scenario da System Center VMM ad Azure](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Annullare l'associazione ed eliminare tutti i criteri di replica passando all'insieme di credenziali -> **Infrastruttura di Site Recovery** -> **per System Center VMM** (Per System Center VMM) -> **Criteri di replica**

3.  Eliminare i riferimenti ai server Virtual Machine Manager seguendo la procedura descritta in [Annullare la registrazione di un server VMM connesso](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Eliminare l'insieme di credenziali.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Macchine virtuali Hyper-V (senza Virtual Machine Manager) in Azure
1. Eliminare tutte le macchine virtuali protette seguendo la procedura descritta in [Disabilitare la protezione per una macchina virtuale Hyper-V (da Hyper-V ad Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Annullare l'associazione ed eliminare tutti i criteri di replica passando all'insieme di credenziali -> **Infrastruttura di Site Recovery** -> **For Hyper-V Sites** (Per siti Hyper-V) -> **Criteri di replica**

3. Eliminare i riferimenti ai server Hyper-V seguendo la procedura descritta in [Annullare la registrazione di un host Hyper-V in un sito di Hyper-V](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Eliminare il sito Hyper-V.

5. Eliminare l'insieme di credenziali.


## <a name="use-powershell-to-force-delete-the-vault"></a>Usare PowerShell per forzare l'eliminazione dell'insieme di credenziali 

> [!Important]
> Se si esegue il test del prodotto e non si hanno problemi di perdita di dati, usare il metodo di eliminazione forzata per rimuovere rapidamente l'insieme di credenziali e tutte le relative dipendenze.
> Il comando di PowerShell elimina tutti i contenuti dell'insieme di credenziali e **non è reversibile**.

Per eliminare l'insieme di credenziali di Site Recovery anche se sono presenti elementi protetti, usare i comandi seguenti:

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault
