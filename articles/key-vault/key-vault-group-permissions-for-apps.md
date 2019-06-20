---
title: Concedere a molte applicazioni l'autorizzazione ad accedere a un insieme di credenziali delle chiavi di Azure - Azure Key Vault | Microsoft Docs
description: Informazioni su come concedere a molte applicazioni l'autorizzazione per accedere a Key Vault
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: b1d0b0948e089d41f460ac2a54150ee51333f87c
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "64721986"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Concedere a più applicazioni l'accesso a un insieme di credenziali delle chiavi

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per concedere a più applicazioni l'accesso a un insieme di credenziali delle chiavi, è possibile usare criteri di controllo di accesso. Un criterio di controllo di accesso può supportare fino a 1024 applicazioni e viene configurato come segue:

1. Creare un gruppo di sicurezza di Azure Active Directory. 
2. Aggiungere al gruppo di sicurezza tutte le entità servizio associate alle applicazioni.
3. Concedere al gruppo di sicurezza l'accesso all'insieme di credenziali delle chiavi.

## <a name="prerequisites"></a>Prerequisiti

Di seguito vengono indicati i prerequisiti:
* [Installare Azure PowerShell](/powershell/azure/overview).
* Installare il [modulo Azure Active Directory V2 PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* Autorizzazioni per la creazione/modifica di gruppi nel tenant di Azure Active Directory. Se non si dispone delle autorizzazioni, può essere necessario contattare l'amministratore di Azure Active Directory. Per informazioni dettagliate sulle autorizzazioni dei criteri di accesso dell'insieme di credenziali delle chiavi, vedere [Informazioni su chiavi, segreti e certificati di Azure Key Vault](about-keys-secrets-and-certificates.md).

## <a name="granting-key-vault-access-to-applications"></a>Concessione dell'accesso Key Vault alle applicazioni

Eseguire i comandi seguenti in PowerShell:

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
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Se è necessario concedere un diverso set di autorizzazioni a un gruppo di applicazioni, creare un gruppo di sicurezza di Azure Active Directory separato per tali applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [proteggere Key Vault](key-vault-secure-your-key-vault.md).
