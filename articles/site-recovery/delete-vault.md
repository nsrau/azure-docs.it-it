---
title: Eliminare un insieme di credenziali di Azure Site RecoveryDelete an Azure Site Recovery vault
description: Informazioni su come eliminare un insieme di credenziali di Servizi di ripristino configurato per il servizio Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894971"
---
# <a name="delete-a-site-recovery-services-vault"></a>Eliminare un insieme di credenziali di Servizi di ripristino per Site Recovery

In questo articolo viene descritto come eliminare un insieme di credenziali di Servizi di ripristino per Site Recovery. Per eliminare un insieme di credenziali usato in Backup di Azure, vedere [Delete a Backup vault in Azure](../backup/backup-azure-delete-vault.md) (Eliminare un insieme di credenziali di Backup in Azure).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Prima di iniziare

Prima di poter eliminare un vault è necessario rimuovere i server registrati e gli elementi nel vault. Gli elementi da rimuovere dipendono dagli scenari di replica distribuiti. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Eliminare una macchina virtuale vault-Azure in AzureDelete a vault-Azure VM to Azure

1. Seguire [queste istruzioni](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) per eliminare tutte le macchine virtuali protette.
2. Quindi, eliminare il vault.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Eliminare una macchina virtuale vault-VMware in AzureDelete a vault-VMware VM to Azure

1. Seguire [queste istruzioni](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) per eliminare tutte le macchine virtuali protette.
2. Seguire [questi passaggi](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) per eliminare tutti i criteri di replica.
3. Eliminare i riferimenti a vCenter utilizzando [questi passaggi.](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)
4. Seguire [queste istruzioni](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) per rimuovere le autorizzazioni di un server di configurazione.
5. Quindi, eliminare il vault.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Eliminare una macchina virtuale hyper-V del vault (con VMM) in AzureDelete a vault-Hyper-V VM (with VMM) to Azure

1. Seguire [questa procedura](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) per eliminare le macchine virtuali Hyper-V gestite da System Center VMM.
2. Annullare l'associazione ed eliminare tutti i criteri di replica. Eseguire questa operazione nell'insieme di credenziali **>'infrastruttura** > di site recovery per i criteri di**replica**VMM di**System Center.** > 
3. Seguire [questa procedura](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) per annullare la registrazione di un server VMM connesso.
4. Quindi, eliminare il vault.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Eliminare una macchina virtuale vault Hyper-V in AzureDelete a vault-Hyper-V VM to Azure

1. Seguire [questi passaggi](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) per eliminare tutte le macchine virtuali protette.
2. Annullare l'associazione ed eliminare tutti i criteri di replica. Eseguire questa operazione nell'insieme di credenziali **>'infrastruttura** > di site recovery per**i criteri**di replica dei siti > **Hyper-V**.
3. Seguire [queste istruzioni](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) per annullare la registrazione di un host Hyper-V.
4. Eliminare il sito Hyper-V.
5. Quindi, eliminare il vault.


## <a name="use-powershell-to-force-delete-the-vault"></a>Usare PowerShell per forzare l'eliminazione dell'insieme di credenziali 

> [!Important]
> Se si esegue il test del prodotto e non si hanno problemi di perdita di dati, usare il metodo di eliminazione forzata per rimuovere rapidamente l'insieme di credenziali e tutte le relative dipendenze.
> Il comando PowerShell elimina tutto il contenuto dell'insieme di credenziali e non è **reversibile.**

Per eliminare l'insieme di credenziali di Site Recovery anche se sono presenti elementi protetti, usare i comandi seguenti:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Ulteriori informazioni su [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)e [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
