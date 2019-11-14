---
title: Esempi di PowerShell V2 per la gestione dei gruppi-Azure AD | Microsoft Docs
description: Questa pagina riporta esempi di PowerShell per la gestione dei gruppi in Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, gruppi, gestione dei gruppi
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1aa696ccaecc24df700315962c1f01f3a298c56c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026701"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Cmdlet di Azure Active Directory versione 2 per la gestione dei gruppi

> [!div class="op_single_selector"]
> - [Portale di Azure](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Questo articolo contiene esempi di come usare PowerShell per gestire i gruppi in Azure Active Directory (Azure AD).  Offre inoltre informazioni su come configurare il modulo Azure AD PowerShell. In primo luogo è necessario [scaricare il modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Installare il modulo Azure AD PowerShell

Per installare il modulo Azure AD PowerShell, usare i comandi seguenti:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Per verificare che il modulo sia pronto per l'uso, usare il comando seguente:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

A questo punto è possibile iniziare a usare i cmdlet nel modulo. Per una descrizione completa dei cmdlet nel modulo Azure AD, consultare la documentazione di riferimento online per [Azure Active Directory PowerShell versione 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Connettersi alla directory

Prima di iniziare la gestione di gruppi mediante i cmdlet PowerShell di Azure AD, è necessario connettere la sessione di PowerShell alla directory da gestire. Usare il seguente comando:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Il cmdlet richiede le credenziali da usare per accedere alla directory. In questo esempio si usa karen@drumkit.onmicrosoft.com per accedere alla directory dimostrativa. Il cmdlet restituisce un messaggio di conferma per indicare che la sessione è stata connessa correttamente alla directory:

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

A questo punto è possibile iniziare a usare i cmdlet di Azure AD per gestire i gruppi nella directory.

## <a name="retrieve-groups"></a>Recuperare gruppi

Per recuperare gruppi esistenti dalla directory, usare il cmdlet Get-AzureADGroups. 

Per recuperare tutti i gruppi nella directory, usare il cmdlet senza parametri:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Il cmdlet restituisce tutti i gruppi nella directory connessa.

È possibile usare il parametro -objectID per recuperare un gruppo specifico indicando l'objectID del gruppo:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Il cmdlet restituisce ora il gruppo il cui objectID corrisponde al valore del parametro immesso:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

È possibile cercare un gruppo specifico usando il parametro -filter. Questo parametro accetta una clausola di filtro ODATA e restituisce tutti i gruppi che corrispondono al filtro, come nell'esempio seguente:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> I cmdlet di Azure AD PowerShell implementano lo standard di query OData. Per altre informazioni, vedere **$filter** in [Opzioni query di sistema OData usando l'endpoint OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Creare gruppi

Per creare un nuovo gruppo nella directory, usare il cmdlet New-AzureADGroup. Questo cmdlet crea un nuovo gruppo di sicurezza denominato "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Aggiornare gruppi

Per aggiornare un gruppo esistente, usare il cmdlet Set-AzureADGroup. In questo esempio stiamo cambiando la proprietà DisplayName del gruppo "Amministratori di Intune". In primo luogo si cerca il gruppo mediante il cmdlet Get-AzureADGroup e si applica il filtro tramite l'attributo DisplayName:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Quindi si cambia la proprietà Description nel nuovo valore "Amministratori di dispositivi Intune":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

A questo punto, se il gruppo viene trovato di nuovo, la proprietà Description viene aggiornata per riflettere il nuovo valore:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Eliminare gruppi

Per eliminare gruppi dalla directory, usare il cmdlet Remove-AzureADGroup come segue:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Gestire l'appartenenza a gruppi

### <a name="add-members"></a>Aggiungere membri

Per aggiungere nuovi membri a un gruppo, usare il cmdlet Add-AzureADGroupMember. Questo comando aggiunge un membro al gruppo degli amministratori di Intune che abbiamo usato nell'esempio precedente:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Il parametro -ObjectId è il valore ObjectID del gruppo a cui si vuole aggiungere un membro e -RefObjectId è il valore ObjectID dell'utente da aggiungere come membro al gruppo.

### <a name="get-members"></a>Ottenere membri

Per ottenere i membri di un gruppo esistente, usare il cmdlet Get-AzureADGroupMember, come nell'esempio seguente:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Rimuovere membri

Per rimuovere il membro aggiunto al gruppo in precedenza, usare il cmdlet Remove-AzureADGroupMember, come illustrato di seguito:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Verificare membri

Per verificare l'appartenenza di un utente a gruppi, usare il cmdlet Select-AzureADGroupIdsUserIsMemberOf. Questo cmdlet accetta come parametri il valore ObjectId dell'utente di cui controllare l'appartenenza al gruppo e l'elenco dei gruppi da controllare. L'elenco dei gruppi deve essere fornito sotto forma di variabile complessa di tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", pertanto è necessario innanzitutto creare una variabile con tale tipo:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

È quindi necessario fornire i valori per i groupId da controllare nell'attributo "GroupIds" della variabile complessa:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Se si desidera controllare l'appartenenza di un utente con ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea ai gruppi di $g, si usa:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Il valore restituito è un elenco dei gruppi di cui l'utente è membro. È possibile applicare questo metodo anche per controllare l'appartenenza di contatti, gruppi o entità servizio a un elenco di gruppi, tramite Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf o Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Disabilitare la creazione di gruppi da parte degli utenti

È possibile impedire agli utenti non amministratori la creazione di gruppi di sicurezza. Il comportamento predefinito in Microsoft Online Directory Services (MSODS) consente agli utenti non amministratori di creare gruppi, indipendentemente dal fatto che la gestione gruppi self-service sia abilitata o meno. L'impostazione della gestione gruppi self-service controlla il comportamento nel solo riquadro di accesso App personali.

Per disabilitare la creazione di gruppi da parte degli utenti non amministratori:

1. Verificare che gli utenti non amministratori siano autorizzati a creare gruppi:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Se restituisce `UsersPermissionToCreateGroupsEnabled : True`, gli utenti non amministratori possono creare gruppi. Per disabilitare questa funzionalità:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Gestire i proprietari di gruppi

Per aggiungere proprietari a un gruppo, usare il cmdlet AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Il parametro-ObjectId è il valore ObjectID del gruppo a cui si desidera aggiungere un proprietario e-RefObjectId è il valore ObjectID dell'utente o dell'entità servizio che si desidera aggiungere come proprietario del gruppo.

Per recuperare i proprietari di un gruppo, usare il cmdlet Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Il cmdlet restituisce l'elenco di proprietari (utenti ed entità servizio) per il gruppo specificato:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Per rimuovere un proprietario da un gruppo, usare il cmdlet Remove-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Alias riservati

Quando viene creato un gruppo, alcuni endpoint consentono all'utente finale di specificare un attributo mailNickname o un alias da usare come parte dell'indirizzo e-mail del gruppo. I gruppi con gli alias di posta elettronica con privilegi elevati seguenti possono essere creati solo da un amministratore globale Azure AD. 
  
* abuse
* admin
* administrator
* hostmaster
* majordomo
* postmaster
* root
* secure
* security
* ssl-admin
* webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Eseguire il writeback del gruppo in locale (anteprima)

Attualmente, molti gruppi sono ancora gestiti in Active Directory locali. Per rispondere alle richieste di sincronizzazione dei gruppi di cloud di nuovo in locale, la funzionalità di writeback dei gruppi di Office 365 per Azure AD è ora disponibile in anteprima.

I gruppi di Office 365 vengono creati e gestiti nel cloud. La funzionalità di writeback consente di eseguire il writeback dei gruppi di Office 365 come gruppi di distribuzione in una foresta Active Directory con Exchange installato. Gli utenti con cassette postali di Exchange locali possono inviare e ricevere messaggi di posta elettronica da questi gruppi. La funzionalità di writeback dei gruppi non supporta i gruppi di sicurezza o i gruppi di distribuzione di Azure AD.

Per altri dettagli, vedere la documentazione per il [servizio di sincronizzazione Azure ad Connect](../hybrid/how-to-connect-syncservice-features.md).

Il writeback dei gruppi di Office 365 è una funzionalità di anteprima pubblica di Azure Active Directory (Azure AD) ed è disponibile con qualsiasi piano di licenza Azure AD a pagamento. Per alcune informazioni legali sulle anteprime, vedere le [condizioni per l'utilizzo supplementari per le anteprime Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Active Directory PowerShell, consultare la documentazione sui [cmdlet di Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrazione delle identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
