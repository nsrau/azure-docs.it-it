---
title: Eliminare un insieme di credenziali dei servizi di ripristino
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
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>Eliminare un insieme di credenziali dei servizi di ripristino
Le dipendenze impediscono di eliminare un insieme di credenziali dei servizi di ripristino e le azioni da eseguire variano in base al tipo di scenario di Azure Site Recovery: da VMware ad Azure, da Hyper-V (con e senza VMM) ad Azure e Backup di Azure. Per eliminare un insieme di credenziali usato in Backup di Azure, selezionare [questo](../backup/backup-azure-delete-vault.md) collegamento.

>[!Important]
>Se dopo aver eseguito il test del prodotto si vuole eliminare rapidamente l'insieme di credenziali e non si hanno problemi di perdita di dati, è possibile usare il metodo di eliminazione forzata per rimuovere l'insieme di credenziali e tutte le relative dipendenze.

> Tenere presente che il comando di PowerShell eliminerà tutti i contenuti dell'insieme di credenziali e che questo non è un passaggio reversibile

## <a name="force-delete-vault-using-powershell"></a>Forzare l'eliminazione dell'insieme di credenziali con PowerShell

Di seguito è illustrata la procedura per eliminare un insieme di credenziali di Site Recovery anche se sono presenti elementi protetti

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



Per eliminare l'insieme di credenziali, seguire i passaggi consigliati (nell'ordine specificato) relativi al proprio scenario

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>Eliminare l'insieme di credenziali usato in Site Recovery per la protezione di macchine virtuali da VMware ad Azure:
1. Verificare che tutte le macchine virtuali protette siano eliminate: vedere la [procedura](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
2.  Verificare che tutti i criteri di replica siano eliminati: vedere la [procedura](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
3.  Verificare che tutti i riferimenti a vCenter siano eliminati: vedere la [procedura](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).
4. Verificare che il server di configurazione sia eliminato: vedere la [procedura](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server).
5. Provare ora a eliminare l'insieme di credenziali.


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>Eliminare l'insieme di credenziali usato in Site Recovery per la protezione di macchine virtuali da Hyper-V (con VMM) ad Azure:
1.  Verificare che tutte le macchine virtuali protette siano eliminate: vedere la [procedura](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Verificare che tutti i criteri di replica siano eliminati: vedere la [procedura](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Eliminare i riferimenti ai server VMM: vedere la [procedura](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server).
-   Provare ora a eliminare l'insieme di credenziali.

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>Eliminare l'insieme di credenziali usato in Site Recovery per la protezione di macchine virtuali da Hyper-V (senza VMM) ad Azure:
1. Verificare che tutte le macchine virtuali protette siano eliminate: vedere la [procedura](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Verificare che tutti i criteri di replica siano eliminati: vedere la [procedura](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Eliminare i riferimenti ai server Hyper-V: vedere la [procedura](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).
-   Eliminare il sito Hyper-V.
-   Provare ora a eliminare l'insieme di credenziali.

