---
title: Abilitare il Backup per lo Stack di Azure con PowerShell | Documenti Microsoft
description: Abilitare il servizio di Backup di infrastruttura con Windows PowerShell in modo che Azure Stack può essere ripristinato se si è verificato un errore.
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
ms.openlocfilehash: 5fab656734d0984cf44a9fe1f29fd73530bd9aa8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259856"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Abilitare il Backup per lo Stack di Azure con PowerShell

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Abilitare il servizio di Backup di infrastruttura con Windows PowerShell così eseguire backup periodici del:
 - Identità interne del servizio e il certificato radice
 - I piani di utente, offerte, le sottoscrizioni
 - Segreti Keyvault
 - I criteri e i ruoli utente RBAC

Per visualizzare i cmdlet PowerShell per abilitare il backup, avviare il backup e ottenere informazioni di backup tramite l'endpoint di gestione di operatore.

## <a name="prepare-powershell-environment"></a>Preparazione ambiente di PowerShell

Per istruzioni sulla configurazione dell'ambiente di PowerShell, vedere [installazione di PowerShell per Azure Stack ](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Generare una nuova chiave di crittografia

Installare e PowerShell configurato per lo Stack di Azure e gli strumenti di Azure Stack.
 - Vedere [diventare operativi con PowerShell nello Stack di Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).
 - Vedere [strumenti scaricare Azure Stack da GitHub](azure-stack-powershell-download.md)

Aprire Windows PowerShell con un prompt dei comandi con privilegi elevati ed eseguire i comandi seguenti:
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
Nella stessa sessione di PowerShell, eseguire i comandi seguenti:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> È necessario utilizzare gli strumenti di AzureStack per generare la chiave.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Specificare la chiave di crittografia, credenziali e condivisione di backup per abilitare il backup

Nella stessa sessione di PowerShell, modificare lo script di PowerShell seguente aggiungendo le variabili per l'ambiente. Eseguire lo script aggiornato per fornire la chiave di crittografia, credenziali e condivisione di backup per il servizio di Backup di infrastruttura.

| Variabile        | DESCRIZIONE   |
|---              |---                                        |
| $username       | Tipo di **Username** usando il dominio e il nome utente per il percorso dell'unità condivisa con diritti di accesso sufficienti per leggere e scrivere file. Ad esempio, `Contoso\backupshareuser`. |
| $password       | Tipo di **Password** per l'utente. |
| $sharepath      | Digitare il percorso di **il percorso di archiviazione di Backup**. È necessario utilizzare una stringa di Universal Naming Convention (UNC) per il percorso di una condivisione file ospitata su un dispositivo distinto. Una stringa UNC specifica il percorso delle risorse, ad esempio i dispositivi o file condivisi. Per garantire la disponibilità dei dati di backup, il dispositivo deve essere in una posizione separata. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Confermare le impostazioni di backup

Nella stessa sessione di PowerShell, eseguire i comandi seguenti:

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
   ```

Il risultato dovrebbe essere simile all'output JSON seguente:

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Passaggi successivi

 - Informazioni su come eseguire un backup, vedere [backup Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Informazioni su come verificare che sia stato eseguito il backup, vedere [backup conferma completata nel portale di amministrazione](azure-stack-backup-back-up-azure-stack.md ).
