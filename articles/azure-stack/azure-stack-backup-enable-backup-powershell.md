---
title: Abilitare il Backup per Azure Stack con PowerShell | Microsoft Docs
description: Abilitare il servizio di infrastruttura di Backup con Windows PowerShell in modo che Azure Stack può essere ripristinato se si verifica un errore.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 8fe7f0ddd630cfca0242af6cc1d728bdef163352
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139358"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Abilitare il Backup per Azure Stack con PowerShell

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Abilitare il servizio di infrastruttura di Backup con Windows PowerShell quindi eseguire backup periodici del:
 - Identità interna del servizio e il certificato radice
 - I piani utente, offerte, le sottoscrizioni
 - Segreti di Key Vault
 - I criteri e i ruoli RBAC utente

È possibile accedere i cmdlet di PowerShell per abilitare il backup, avviare il backup e ottenere le informazioni di backup tramite l'endpoint di gestione di operatore.

## <a name="prepare-powershell-environment"></a>Preparare l'ambiente di PowerShell

Per istruzioni sulla configurazione dell'ambiente di PowerShell, vedere [installazione di PowerShell per Azure Stack ](azure-stack-powershell-install.md). Per accedere ad Azure Stack, vedere [configurare l'ambiente di operatore e accedere ad Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Fornire la chiave di crittografia, credenziali e condivisione di backup per abilitare il backup

Nella stessa sessione di PowerShell, modificare lo script di PowerShell seguente aggiungendo le variabili per l'ambiente. Eseguire lo script aggiornato per fornire la chiave di crittografia, credenziali e condivisione di backup per il servizio Backup di infrastruttura.

| Variabile        | DESCRIZIONE   |
|---              |---                                        |
| $username       | Tipo di **Username** usando il nome utente e dominio per il percorso dell'unità condivisa con diritti di accesso sufficienti per leggere e scrivere file. Ad esempio: `Contoso\backupshareuser`. |
| $password       | Tipo di **Password** per l'utente. |
| $sharepath      | Digitare il percorso per il **percorso di archiviazione di Backup**. È necessario usare una stringa di Universal Naming Convention (UNC) per il percorso a una condivisione file ospitata su un dispositivo separato. Una stringa UNC specifica il percorso delle risorse, ad esempio i file condivisi o i dispositivi. Per garantire la disponibilità dei dati di backup, il dispositivo deve trovarsi in una posizione separata. |
| $frequencyInHours | La frequenza in ore determina ogni quanto vengono creati i backup. Il valore predefinito è 12. Utilità di pianificazione supporta un massimo di 12 e almeno 4.|
| $retentionPeriodInDays | Periodo di conservazione in giorni determina il numero di giorni di backup viene conservato in una posizione esterna. Il valore predefinito è 7. Utilità di pianificazione supporta un massimo di 14 e un minimo di 2. I backup precedenti al periodo di conservazione vengono eliminati automaticamente dalla posizione esterna.|
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Confermare le impostazioni di backup

Nella stessa sessione di PowerShell, eseguire i comandi seguenti:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
   ```

Il risultato dovrebbe essere simile al seguente esempio:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Aggiornare le impostazioni di backup
Nella stessa sessione di PowerShell, è possibile aggiornare i valori predefiniti per il periodo di conservazione e frequenza per i backup. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Il risultato dovrebbe essere simile al seguente esempio:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>Passaggi successivi

 - Imparare a eseguire un backup, vedere [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Informazioni su come verificare che sia stato eseguito il backup, vedere [conferma backup completato nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md ).
