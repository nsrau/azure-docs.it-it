---
title: Abilitare il Backup per lo Stack di Azure con PowerShell | Documenti Microsoft
description: "Abilitare l'infrastruttura di servizio con Windows PowerShell in modo che Azure Stack può essere ripristinato se si è verificato un errore."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: b4f48b7fd07c5fb590b6989e04e9084c86142d2a
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Abilitare il Backup per lo Stack di Azure con PowerShell

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Abilitare l'infrastruttura di servizio con Windows PowerShell in modo che Azure Stack può essere ripristinato se si è verificato un errore. Per visualizzare i cmdlet PowerShell per abilitare il backup, avviare il backup e ottenere informazioni di backup tramite l'endpoint di gestione di operatore.

## <a name="download-azure-stack-tools"></a>Scaricare strumenti di Azure Stack

Installare e PowerShell configurato per lo Stack di Azure e gli strumenti di Azure Stack. Vedere [diventare operativi con PowerShell nello Stack di Azure](https://review.docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart).

##  <a name="load-the-connect-and-infrastructure-modules"></a>Caricare i moduli di connessione e l'infrastruttura

Aprire Windows PowerShell con un prompt dei comandi con privilegi elevati ed eseguire i comandi seguenti:

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>Ambiente Rm e log di installazione nell'endpoint di gestione (operatore)

Nella stessa sessione di PowerShell, modificare lo script di PowerShell seguente aggiungendo le variabili per l'ambiente. Eseguire lo script aggiornato per configurare l'ambiente RM e accedere all'endpoint di gestione di operatore.

| Variabile    | DESCRIZIONE |
|---          |---          |
| $TenantName | Nome di tenant di Azure Active Directory. |
| Nome dell'account (operatore)        | Il nome dell'account Azure Stack operatore. |
| Endpoint di gestione risorse di Azure | URL per Gestione risorse di Azure. |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>Generare una nuova chiave di crittografia

Nella stessa sessione di PowerShell, eseguire i comandi seguenti:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Specificare la chiave di crittografia, credenziali e condivisione di backup per abilitare il backup

Nella stessa sessione di PowerShell, modificare lo script di PowerShell seguente aggiungendo le variabili per l'ambiente. Eseguire lo script aggiornato per fornire la chiave di crittografia, credenziali e condivisione di backup per il servizio di Backup di infrastruttura.

| Variabile        | DESCRIZIONE   |
|---              |---                                        |
| $username       | Tipo di **Username** utilizzando il dominio e il nome utente per il percorso dell'unità condivisa. Ad esempio, `Contoso\administrator`. |
| $password       | Tipo di **Password** per l'utente. |
| $sharepath      | Digitare il percorso di **il percorso di archiviazione di Backup**. È necessario utilizzare una stringa di Universal Naming Convention (UNC) per il percorso di una condivisione file ospitata su un dispositivo distinto. Una stringa UNC specifica il percorso delle risorse, ad esempio i dispositivi o file condivisi. Per garantire la disponibilità dei dati di backup, il dispositivo deve essere in una posizione separata. |

   ```powershell
   $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

Set-AzSBackupShare -Location $location -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey 

   ```
##  <a name="confirm-backup-settings"></a>Confermare le impostazioni di backup

Nella stessa sessione di PowerShell, eseguire i comandi seguenti:

   ```powershell
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
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
