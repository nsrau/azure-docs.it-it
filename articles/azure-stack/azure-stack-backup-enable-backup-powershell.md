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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 773e600577b35019b8a3619c7eec3e93b77a4382
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085797"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Abilitare il Backup per Azure Stack con PowerShell

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Abilitare il servizio di infrastruttura di Backup con Windows PowerShell quindi eseguire backup periodici del:
 - Identità interna del servizio e il certificato radice
 - I piani utente, offerte, le sottoscrizioni
 - Calcolo, archiviazione e le quote utente di rete
 - Segreti dell'insieme di credenziali chiave utente
 - I criteri e i ruoli RBAC utente
 - Account di archiviazione utente

È possibile accedere i cmdlet di PowerShell per abilitare il backup, avviare il backup e ottenere le informazioni di backup tramite l'endpoint di gestione di operatore.

## <a name="prepare-powershell-environment"></a>Preparare l'ambiente di PowerShell

Per istruzioni sulla configurazione dell'ambiente di PowerShell, vedere [installazione di PowerShell per Azure Stack](azure-stack-powershell-install.md). Per accedere ad Azure Stack, vedere [configurare l'ambiente di operatore e accedere ad Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Fornire la chiave di crittografia, credenziali e condivisione di backup per abilitare il backup

Nella stessa sessione di PowerShell, modificare lo script di PowerShell seguente aggiungendo le variabili per l'ambiente. Eseguire lo script aggiornato per fornire la chiave di crittografia, credenziali e condivisione di backup per il servizio Backup di infrastruttura.

| Variabile        | DESCRIZIONE   |
|---              |---                                        |
| $username       | Tipo di **Username** usando il nome utente e dominio per il percorso dell'unità condivisa con diritti di accesso sufficienti per leggere e scrivere file. Ad esempio: `Contoso\backupshareuser`. |
| $password       | Tipo di **Password** per l'utente. |
| $sharepath      | Digitare il percorso per il **percorso di archiviazione di Backup**. È necessario usare una stringa di Universal Naming Convention (UNC) per il percorso a una condivisione file ospitata su un dispositivo separato. Una stringa UNC specifica il percorso delle risorse, ad esempio i file condivisi o i dispositivi. Per garantire la disponibilità dei dati di backup, il dispositivo deve trovarsi in una posizione separata. |
| $frequencyInHours | La frequenza in ore determina ogni quanto vengono creati i backup. Il valore predefinito è 12. Utilità di pianificazione supporta un massimo di 12 e almeno 4.|
| $retentionPeriodInDays | Periodo di conservazione in giorni determina il numero di giorni di backup viene conservato in una posizione esterna. Il valore predefinito è 7. Utilità di pianificazione supporta un massimo di 14 e un minimo di 2. I backup precedenti al periodo di conservazione vengono eliminati automaticamente dalla posizione esterna.|
| $encryptioncertpath | Si applica a 1901 e altro ancora.  Parametro è disponibile in Azure Stack modulo versione 1.7 e versioni successive. Il percorso del certificato di crittografia specifica il percorso file il. File CER con chiave pubblica usata per la crittografia dei dati. |
| $encryptionkey | Applicato per compilare 1811 o versioni precedenti. Parametro è disponibile in Azure Stack modulo versione 1.6 o versioni precedenti. La chiave di crittografia utilizzata per la crittografia dei dati. Usare la [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) cmdlet per generare una nuova chiave. |
|     |     |

### <a name="enable-backup-on-1901-and-beyond-using-certificate"></a>Abilitare il backup in 1901 e versioni successive mediante certificato
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>Abilitare il backup in 1811 o in precedenza usando il certificato
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>Confermare le impostazioni di backup

Nella stessa sessione di PowerShell, eseguire i comandi seguenti:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
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

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Il risultato dovrebbe essere simile al seguente esempio:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-powershell"></a>PowerShell per Azure Stack 
Il cmdlet di PowerShell per configurare il backup dell'infrastruttura è Set-AzsBackupConfiguration. Nelle versioni precedenti, il cmdlet è Set-AzsBackupShare. Questo cmdlet è necessario fornire un certificato. Se il backup dell'infrastruttura è configurato con una chiave di crittografia, è possibile aggiornare la chiave di crittografia o visualizzare le proprietà. Dovrai usare la versione 1.6 di Admin PowerShell. 

Se è stato configurato il backup di infrastruttura prima di aggiornare a 1901 è possibile utilizzare la versione 1.6 dell'Admin PowerShell impostare e visualizzare la chiave di crittografia. Versione 1.6 non consentirà di aggiornare dalla chiave di crittografia in un file di certificato.
Fare riferimento a [installare PowerShell per Azure Stack](azure-stack-powershell-install.md) per altre informazioni sull'installazione della versione corretta del modulo. 


## <a name="next-steps"></a>Passaggi successivi

Imparare a eseguire un backup, vedere [eseguire il backup di Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Informazioni su come verificare che sia stato eseguito il backup, vedere [conferma backup completato nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md)
