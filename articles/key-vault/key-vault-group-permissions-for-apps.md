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
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: 14e6e8bb723eb236f8fb315454b8697a3bd947ef
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286428"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Concedere a molte applicazioni l'autorizzazione per accedere a Key Vault

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>D: se si hanno diverse applicazioni che devono accedere all'insieme di credenziali delle chiavi, come si può fare in modo che tali applicazioni, fino a 1024, accedano a Key Vault?

I criteri di controllo di accesso di Key Vault supportano fino a 1024 elementi. È tuttavia possibile creare un gruppo di sicurezza di Azure Active Directory. Aggiungere tutte le entità servizio associate a questo gruppo di sicurezza e quindi concedere a tale gruppo di accedere a Key Vault.

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Se è necessario concedere un diverso set di autorizzazioni a un gruppo di applicazioni, creare un gruppo di sicurezza di Azure Active Directory separato per tali applicazioni.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come [proteggere Key Vault](key-vault-secure-your-key-vault.md).
