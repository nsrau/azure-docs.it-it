---
title: Cmdlet PowerShell di Azure Active Directory per la gestione dei gruppi in Azure AD | Microsoft Docs
description: Questa pagina riporta esempi di PowerShell per la gestione dei gruppi in Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, gruppi, gestione dei gruppi
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7a5023dc-2727-4c25-8254-b531fc3244ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand;rodejo
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: d7be54b508a845d6746fd65887e3339ff371a287
ms.contentlocale: it-it
ms.lasthandoff: 05/05/2017


---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Cmdlet di Azure Active Directory versione 2 per la gestione dei gruppi
> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-groups-create-azure-portal.md)
> * [Portale di Azure classico](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Il seguente documento riporta esempi di come usare PowerShell per gestire i gruppi in Azure Active Directory (Azure AD).  Offre inoltre informazioni su come configurare il modulo di Azure AD PowerShell. In primo luogo è necessario [scaricare il modulo Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="installing-the-azure-ad-powershell-module"></a>Installazione del modulo Azure AD PowerShell
Per installare il modulo PowerShell di Azure AD, usare i comandi seguenti:

    PS C:\Windows\system32> install-module azuread

Per verificare che il modulo sia stato installato, usare il comando seguente:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

A questo punto è possibile iniziare a usare i cmdlet nel modulo. Per una descrizione completa dei cmdlet nel modulo Azure AD, consultare la [documentazione di riferimento online](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connecting-to-the-directory"></a>Connessione alla directory
Prima di iniziare la gestione di gruppi mediante i cmdlet PowerShell di Azure AD, è necessario connettere la sessione di PowerShell alla directory da gestire. A tale scopo, eseguire il comando seguente:

    PS C:\Windows\system32> Connect-AzureAD

Il cmdlet richiederà le credenziali da usare per accedere alla directory. In questo esempio si usa karen@drumkit.onmicrosoft.com per accedere alla directory dimostrativa. Il cmdlet restituirà un messaggio di conferma per indicare che la sessione è stata connessa correttamente alla directory:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

A questo punto è possibile iniziare a usare i cmdlet di Azure AD per gestire i gruppi nella directory.

## <a name="retrieving-groups"></a>Recupero di gruppi
Per recuperare i gruppi esistenti dalla directory è possibile usare il cmdlet Get-AzureADGroups. Per recuperare tutti i gruppi nella directory, usare il cmdlet senza parametri:

    PS C:\Windows\system32> get-azureadgroup

Il cmdlet restituirà tutti i gruppi nella directory connessa.

È possibile usare il parametro -objectID per recuperare un gruppo specifico indicando l'objectID del gruppo:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Il cmdlet restituirà il gruppo il cui objectID corrisponde al valore del parametro che è stato immesso:

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

È possibile cercare un gruppo specifico usando il parametro -filter. Questo parametro accetta una clausola di filtro ODATA e restituisce tutti i gruppi che corrispondono al filtro, come nell'esempio seguente:

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

Si noti che i cmdlet di Azure AD PowerShell implementano lo standard di query OData. Altre informazioni sono disponibili [qui](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Creazione di gruppi
Per creare un nuovo gruppo nella directory, usare il cmdlet New-AzureADGroup. Questo cmdlet crea un nuovo gruppo di sicurezza denominato "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Aggiornamento di gruppi
Per aggiornare un gruppo esistente, usare il cmdlet Set-AzureADGroup. In questo esempio stiamo cambiando la proprietà DisplayName del gruppo "Amministratori di Intune". In primo luogo si cerca il gruppo mediante il cmdlet Get-AzureADGroup e si applica il filtro tramite l'attributo DisplayName:

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

Quindi si cambia la proprietà Description nel nuovo valore "Amministratori di dispositivi Intune":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Ora, se si cerca il gruppo nuovamente, si noterà che la proprietà Description è stata aggiornata con il nuovo valore:

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

## <a name="deleting-groups"></a>Eliminazione di gruppi
Per eliminare gruppi dalla directory, usare il cmdlet Remove-AzureADGroup come segue:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Gestione dei membri dei gruppi
Per aggiungere nuovi membri a un gruppo, si usa il cmdlet Add-AzureADGroupMember. Questo comando aggiunge un membro al gruppo degli amministratori di Intune che abbiamo usato nell'esempio precedente:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Il parametro -ObjectId è il valore ObjectID del gruppo a cui si vuole aggiungere un membro e -RefObjectId è il valore ObjectID dell'utente da aggiungere come membro al gruppo.

Per ottenere i membri di un gruppo esistente, usare il cmdlet Get-AzureADGroupMember, come nell'esempio seguente:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Per rimuovere il membro aggiunto al gruppo in precedenza, usare il cmdlet Remove-AzureADGroupMember, come illustrato di seguito:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Per verificare l'appartenenza al gruppo di un utente, usare il cmdlet Select-AzureADGroupIdsUserIsMemberOf. Questo cmdlet accetta come parametri il valore ObjectId dell'utente di cui controllare l'appartenenza al gruppo e l'elenco dei gruppi da controllare. L'elenco dei gruppi deve essere fornito sotto forma di variabile complessa di tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", pertanto è necessario innanzitutto creare una variabile con tale tipo:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

È quindi necessario fornire i valori per i groupId da controllare nell'attributo "GroupIds" della variabile complessa:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Se si desidera controllare l'appartenenza di un utente con ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea ai gruppi di $g, si usa:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Il valore restituito è un elenco dei gruppi di cui l'utente è membro. È possibile applicare questo metodo anche per controllare l'appartenenza di contatti, gruppi o entità servizio a un elenco di gruppi, tramite Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf o Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="managing-owners-of-groups"></a>Gestione dei proprietari dei gruppi
Per aggiungere proprietari a un gruppo, usare il cmdlet AzureADGroupOwner:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Il parametro -ObjectId è il valore ObjectID del gruppo a cui si vuole aggiungere un proprietario e -RefObjectId è il valore ObjectID dell'utente da aggiungere come proprietario al gruppo.

Per recuperare i proprietari di un gruppo, usare Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Il cmdlet restituirà l'elenco dei proprietari per il gruppo specificato:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Per rimuovere un proprietario da un gruppo, usare Remove-AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Azure Active Directory PowerShell, consultare la documentazione sui [cmdlet di Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Gestione dell'accesso alle risorse tramite i gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)

