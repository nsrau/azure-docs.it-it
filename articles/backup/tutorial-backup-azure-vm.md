---
title: Backup di macchine virtuali in Azure su larga scala | Microsoft Docs
description: "Questa esercitazione descrive in dettaglio l'esecuzione di backup tra più macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: backup di macchine virtuali; back up di macchine virtuali; backup e ripristino di emergenza
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: mvc
ms.openlocfilehash: 01609c00c6f0585eff4843932b9eb7a090a59c19
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-azure-virtual-machines-in-azure-at-scale"></a>Backup di macchine virtuali di Azure su larga scala

Questa esercitazione descrive in dettaglio l'esecuzione di backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino. Per eseguire il backup di macchine virtuali, il più del lavoro è dato dalla preparazione. Prima di poter eseguire il backup o proteggere una macchina virtuale, è necessario completare i [prerequisiti](backup-azure-arm-vms-prepare.md) per preparare l'ambiente per la protezione delle macchine virtuali. 

> [!IMPORTANT]
> In questa esercitazione si presuppone che siano già stati creati un gruppo di risorse e una macchina virtuale di Azure.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un [insieme di credenziali di Servizi di ripristino](backup-azure-recovery-services-vault-overview.md) è un contenitore che include i punti di ripristino degli elementi di cui viene eseguito il backup. Un insieme di credenziali di Servizi di ripristino è una risorsa di Azure che può essere distribuita e gestita come parte di un gruppo di risorse di Azure. In questa esercitazione viene creato un insieme di credenziali di Servizi di ripristino nello stesso gruppo di risorse della macchina virtuale da proteggere.


Quando si usa Backup di Azure per la prima volta, è necessario registrare il provider di Servizi di ripristino di Azure nella sottoscrizione. Se il provider con la sottoscrizione è già stato registrato, andare al passaggio successivo.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
```

Creare un insieme di credenziali di Servizi di ripristino tramite il comando **New-AzureRmRecoveryServicesVault**. Assicurarsi di specificare il nome e il percorso del gruppo di risorse usato quando si configura la macchina virtuale di cui si desidera eseguire il backup. 

```powershell
New-AzureRmRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Molti cmdlet di Backup di Azure richiedono l'oggetto dell'insieme di credenziali dei servizi di ripristino come input. Per questo motivo, è utile archiviare l'oggetto dell'insieme di credenziali dei servizi di ripristino di Backup in una variabile. Usare quindi il comando **Set-AzureRmRecoveryServicesBackupProperties** per impostare l'opzione **-BackupStorageRedundancy** su [Archiviazione con ridondanza geografica (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). 

```powershell
$vault1 = Get-AzureRmRecoveryServicesVault –Name myRSVault
Set-AzureRmRecoveryServicesBackupProperties  -vault $vault1 -BackupStorageRedundancy GeoRedundant
```

## <a name="back-up-azure-virtual-machines"></a>Eseguire il backup di macchine virtuali di Azure

Prima di poter eseguire il backup iniziale è necessario impostare il contesto dell'insieme di credenziali. Il contesto dell'insieme di credenziali definisce il tipo di dati protetti nell'insieme di credenziali. Quando si crea un insieme di credenziali di Servizi di ripristino, questo è dotato di protezione predefinita e criteri di conservazione. I criteri di protezione predefinita attivano un processo di backup ogni giorno all'ora specificata. I criteri di conservazione predefiniti consentono di mantenere il punto di recupero giornaliero per 30 giorni. Per questa esercitazione, accettare le impostazioni predefinite. 

Usare **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** per impostare il contesto dell'insieme di credenziali. Una volta impostato il contesto dell'insieme di credenziali, si applica a tutti i cmdlet successivi. 

```powershell
Get-AzureRmRecoveryServicesVault -Name myRSVault | Set-AzureRmRecoveryServicesVaultContext
```

Usare **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** per attivare il processo di backup. Il processo di backup crea un punto di ripristino. Se si tratta del backup iniziale, allora il punto di recupero è un backup completo. I backup successivi creeranno una copia incrementale.

```powershell
$namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
```

## <a name="delete-the-recovery-services-vault"></a>Eliminare l'insieme di credenziali di Servizi di ripristino

Per eliminare un insieme di credenziali di Servizi di ripristino è necessario innanzitutto eliminare i punti di ripristino nell'insieme di credenziali e quindi annullare la registrazione dell'insieme di credenziali. Questi passaggi sono descritti in dettaglio dai comandi seguenti. 


```powershell
$Cont = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzureRmRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzureRmRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzureRmRecoveryServicesVault -Vault $vault1
```

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi
Se si riscontrano problemi durante il backup della macchina virtuale, leggere l'[articolo sulla risoluzione dei problemi di backup di macchine virtuali di Azure ](backup-azure-vms-troubleshoot.md) per assistenza.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver protetto la macchina virtuale, vedere gli articoli seguenti per ottenere informazioni sulle attività di gestione delle macchine virtuali e su come ripristinare le macchine virtuali da un punto di recupero.

* Per modificare il criterio di backup, vedere [Usare i cmdlet AzureRM.RecoveryServices.Backup per eseguire il backup di macchine virtuali](backup-azure-vms-automation.md#create-a-protection-policy).
* [Gestire e monitorare il backup delle macchine virtuali di Azure](backup-azure-manage-vms.md)
* [Ripristino di macchine virtuali](backup-azure-arm-restore-vms.md)
