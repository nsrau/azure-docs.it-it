---
title: Concedere a molte applicazioni l'autorizzazione per accedere ad Azure Key Vault | Documentazione Microsoft
description: Informazioni su come concedere a molte applicazioni l'autorizzazione per accedere a Key Vault
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 14da9256def60d678ef5cae795fef1c373914b5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Concedere a molte applicazioni l'autorizzazione per accedere a Key Vault

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>D: Sono presenti più applicazioni (oltre 16) che devono accedere a Key Vault. Poiché Key Vault consente solo 16 voci di controllo di accesso, come è possibile ottenere procedere?

I criteri di controllo di accesso a Key Vault supportano solo 16 voci. È tuttavia possibile creare un gruppo di sicurezza di Azure Active Directory. Aggiungere tutte le entità servizio associate a questo gruppo di sicurezza e quindi concedere a tale gruppo di accedere a Key Vault.

Di seguito vengono indicati i prerequisiti:
* [Installare il modulo di Azure Active Directory V2 per PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* [Installare Azure PowerShell](/powershell/azure/overview).
* Per eseguire questi comandi, sono necessarie le autorizzazioni per creare o modificare gruppi nel tenant di Azure Active Directory. Se non si dispone delle autorizzazioni, può essere necessario contattare l'amministratore di Azure Active Directory.

Eseguire questi comandi in PowerShell.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionToKeys all –PermissionToSecrets all –PermissionToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Se è necessario concedere un diverso set di autorizzazioni a un gruppo di applicazioni, creare un gruppo di sicurezza di Azure Active Directory separato per tali applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [proteggere Key Vault](key-vault-secure-your-key-vault.md).
