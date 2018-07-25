---
title: Abilitare il Backup per Azure Stack con PowerShell | Microsoft Docs
description: Abilitare il servizio di infrastruttura di Backup con Windows PowerShell in modo che Azure Stack può essere ripristinato se si verifica un errore.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 76a24e7096cbc2a9bcea8bf68e2b333345dbff68
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242955"
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
| $key            | Tipo di **chiave di crittografia** usata per crittografare ogni backup. |
| $password       | Tipo di **Password** per l'utente. |
| $sharepath      | Digitare il percorso per il **percorso di archiviazione di Backup**. È necessario usare una stringa di Universal Naming Convention (UNC) per il percorso a una condivisione file ospitata su un dispositivo separato. Una stringa UNC specifica il percorso delle risorse, ad esempio i file condivisi o i dispositivi. Per garantire la disponibilità dei dati di backup, il dispositivo deve trovarsi in una posizione separata. |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Confermare le impostazioni di backup

Nella stessa sessione di PowerShell, eseguire i comandi seguenti:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

Il risultato dovrebbe essere simile al seguente:

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>Passaggi successivi

 - Imparare a eseguire un backup, vedere [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Informazioni su come verificare che sia stato eseguito il backup, vedere [conferma backup completato nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md ).