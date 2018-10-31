---
title: Concedere a molte applicazioni l'autorizzazione per accedere ad Azure Key Vault | Documentazione Microsoft
description: Informazioni su come concedere a molte applicazioni l'autorizzazione per accedere a Key Vault
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: ambapat
ms.openlocfilehash: 4ad6a18f9937fcc7d24bebc3ac197e23990ff59e
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309247"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Concedere a più applicazioni l'accesso a un insieme di credenziali delle chiavi

Per concedere a più applicazioni l'accesso a un insieme di credenziali delle chiavi, è possibile usare criteri di controllo di accesso. Un criterio di controllo di accesso può supportare fino a 1024 applicazioni e viene configurato come segue:

1. Creare un gruppo di sicurezza di Azure Active Directory. 
2. Aggiungere al gruppo di sicurezza tutte le entità servizio associate alle applicazioni.
3. Concedere al gruppo di sicurezza l'accesso all'insieme di credenziali delle chiavi.

Di seguito vengono indicati i prerequisiti:
* [Installare il modulo di Azure Active Directory V2 per PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* [Installare Azure PowerShell](/powershell/azure/overview).
* Per eseguire questi comandi, sono necessarie le autorizzazioni per creare o modificare gruppi nel tenant di Azure Active Directory. Se non si dispone delle autorizzazioni, può essere necessario contattare l'amministratore di Azure Active Directory. Per informazioni dettagliate sulle autorizzazioni dei criteri di accesso dell'insieme di credenziali delle chiavi, vedere [Informazioni su chiavi, segreti e certificati di Azure Key Vault](about-keys-secrets-and-certificates.md).

Eseguire ora i comandi seguenti in PowerShell:

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Se è necessario concedere un diverso set di autorizzazioni a un gruppo di applicazioni, creare un gruppo di sicurezza di Azure Active Directory separato per tali applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [proteggere Key Vault](key-vault-secure-your-key-vault.md).
