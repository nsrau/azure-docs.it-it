---
title: Eliminare un insieme di credenziali di Site Recovery
description: Informazioni su come eliminare un insieme di credenziali di Azure Site Recovery, in base allo scenario di Site Recovery.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 9c4a88d8a3d63555a57bd5553b721cfbcd77df2c
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971189"
---
# <a name="delete-a-site-recovery-vault"></a>Eliminare un insieme di credenziali di Site Recovery

Le dipendenze possono impedire l'eliminazione di un insieme di credenziali di Azure Site Recovery. Le azioni da eseguire variano a seconda dello scenario di ripristino del sito. Per eliminare un insieme di credenziali usato in Backup di Azure, vedere [Delete a Backup vault in Azure](../backup/backup-azure-delete-vault.md) (Eliminare un insieme di credenziali di Backup in Azure).



## <a name="delete-a-site-recovery-vault"></a>Eliminare un insieme di credenziali di Site Recovery 
Per eliminare l'insieme di credenziali, seguire la procedura consigliata per lo scenario.

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

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmRecoveryServicesVault -Name "vaultname"

    Remove-AzureRmRecoveryServicesVault -Vault $vault

Altre informazioni su [Get-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault?view=azurermps-6.0.0) e [Remove-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/remove-azurermrecoveryservicesvault?view=azurermps-6.0.0).
