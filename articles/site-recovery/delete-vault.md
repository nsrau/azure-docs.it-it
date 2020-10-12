---
title: Eliminare un insieme di credenziali Azure Site Recovery
description: Informazioni su come eliminare un insieme di credenziali di Servizi di ripristino configurato per il servizio Azure Site Recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426385"
---
# <a name="delete-a-site-recovery-services-vault"></a>Eliminare un insieme di credenziali di Servizi di ripristino per Site Recovery

Questo articolo descrive come eliminare un insieme di credenziali di servizi di ripristino per Site Recovery. Per eliminare un insieme di credenziali usato in Backup di Azure, vedere [Delete a Backup vault in Azure](../backup/backup-azure-delete-vault.md) (Eliminare un insieme di credenziali di Backup in Azure).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Prima di iniziare

Prima di poter eliminare un insieme di credenziali, è necessario rimuovere i server registrati e gli elementi nell'insieme di credenziali. Ciò che è necessario rimuovere dipende dagli scenari di replica distribuiti. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Eliminare un insieme di credenziali-macchina virtuale di Azure in Azure

1. Seguire [queste istruzioni](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) per eliminare tutte le macchine virtuali protette.
2. Quindi, eliminare l'insieme di credenziali.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Eliminare un insieme di credenziali-macchina virtuale VMware in Azure

1. Seguire [queste istruzioni](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) per eliminare tutte le macchine virtuali protette.
2. Per eliminare tutti i criteri di replica, attenersi alla [seguente procedura](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) .
3. Eliminare i riferimenti a vCenter seguendo [questa procedura](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Seguire [queste istruzioni](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) per rimuovere le autorizzazioni di un server di configurazione.
5. Quindi, eliminare l'insieme di credenziali.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Eliminare un insieme di credenziali-macchina virtuale Hyper-V (con VMM) in Azure

1. Seguire [questa procedura](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) per eliminare le VM Hyper-V gestite da System Center VMM.
2. Annullare l'associazione ed eliminare tutti i criteri di replica. Eseguire questa operazione nell'insieme di credenziali > **infrastruttura Site Recovery**  >  **per i criteri di replica di System Center VMM**  >  **Replication Policies**.
3. Per annullare la registrazione di un server VMM connesso, attenersi alla [seguente procedura](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) .
4. Quindi, eliminare l'insieme di credenziali.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Eliminare un insieme di credenziali-macchina virtuale Hyper-V in Azure

1. Per eliminare tutte le macchine virtuali protette, seguire [questa procedura](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) .
2. Annullare l'associazione ed eliminare tutti i criteri di replica. Eseguire questa operazione nell'insieme di credenziali > **infrastruttura Site Recovery**  >  **per i criteri di replica dei siti Hyper-V**  >  **Replication Policies**.
3. Seguire [queste istruzioni](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) per annullare la registrazione di un host Hyper-V.
4. Eliminare il sito Hyper-V.
5. Quindi, eliminare l'insieme di credenziali.


## <a name="use-powershell-to-force-delete-the-vault"></a>Usare PowerShell per forzare l'eliminazione dell'insieme di credenziali 

> [!Important]
> Se si esegue il test del prodotto e non si hanno problemi di perdita di dati, usare il metodo di eliminazione forzata per rimuovere rapidamente l'insieme di credenziali e tutte le relative dipendenze.
> Il comando di PowerShell Elimina tutti i contenuti dell'insieme di credenziali e **non è reversibile**.

Per eliminare l'insieme di credenziali di Site Recovery anche se sono presenti elementi protetti, usare i comandi seguenti:

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

Altre informazioni su [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)e [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
