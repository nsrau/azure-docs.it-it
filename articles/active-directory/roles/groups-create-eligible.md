---
title: Creazione di un gruppo per l'assegnazione di ruoli in Azure Active Directory | Microsoft Docs
description: Informazioni su come creare un gruppo assegnabile ai ruoli in Azure AD. Gestire i ruoli di Azure nel portale di Azure, in PowerShell o nell'API Graph.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f58aaecaa9f8606399b40fdb1d38a274117ec6a2
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377638"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Creare un gruppo assegnabile al ruolo in Azure Active Directory

È possibile assegnare un ruolo solo a un gruppo creato con la proprietà' isAssignableToRole ' impostata su true oppure è stato creato nel portale di Azure AD con **Azure ad i ruoli possono essere assegnati al gruppo** attivato. Questo attributo di gruppo rende il gruppo uno che può essere assegnato a un ruolo in Azure Active Directory (Azure AD). Questo articolo descrive come creare questo tipo di gruppo speciale. **Nota:** Un gruppo con la proprietà isAssignableToRole impostata su true non può essere di tipo di appartenenza dinamica. Per ulteriori informazioni, vedere [utilizzo di un gruppo per gestire le assegnazioni di ruolo Azure ad](groups-concept.md).

## <a name="using-azure-ad-admin-center"></a>Uso di Azure AD interfaccia di amministrazione

1. Accedere al centro di [amministrazione di Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con autorizzazioni di amministratore del ruolo con privilegi o di amministratore globale nell'organizzazione Azure ad.
1. Selezionare **gruppi**  >  **tutti i gruppi**  >  **nuovo gruppo**.

    [![Aprire Azure Active Directory e creare un nuovo gruppo.](./media/groups-create-eligible/new-group.png "Aprire Azure Active Directory e creare un nuovo gruppo.")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. Nella scheda **nuovo gruppo** specificare tipo di gruppo, nome e descrizione.
1. **È possibile assegnare i ruoli di Azure ad al gruppo**. Questa opzione è visibile solo per gli amministratori dei ruoli con privilegi e per gli amministratori globali, perché si tratta solo di due ruoli che possono impostare l'opzione.

    [![Rendere il nuovo gruppo idoneo per l'assegnazione di ruolo](./media/groups-create-eligible/eligible-switch.png "Rendere il nuovo gruppo idoneo per l'assegnazione di ruolo")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Selezionare i membri e i proprietari del gruppo. È anche possibile assegnare ruoli al gruppo, ma in questo caso non è necessario assegnare un ruolo.

    [![Aggiungere membri al gruppo assegnabile al ruolo e assegnare i ruoli.](./media/groups-create-eligible/specify-members.png "Aggiungere membri al gruppo assegnabile al ruolo e assegnare i ruoli.")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. Dopo aver specificato i membri e i proprietari, selezionare **Crea**.

    [![Il pulsante Crea si trova nella parte inferiore della pagina.](./media/groups-create-eligible/create-button.png "Il pulsante Crea si trova nella parte inferiore della pagina.")](./media/groups-create-eligible/create-button.png#<lightbox>)

Il gruppo viene creato con tutti i ruoli che potrebbero essere stati assegnati.

## <a name="using-powershell"></a>Utilizzo di PowerShell

### <a name="install-the-azure-ad-preview-module"></a>Installare il modulo Azure AD Preview

```powershell
install-module azureadpreview
import-module azureadpreview
```

Per verificare che il modulo sia pronto per l'uso, eseguire il comando seguente:

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Creare un gruppo che può essere assegnato al ruolo

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Per questo tipo di gruppo, `isPublic` sarà sempre false e `isSecurityEnabled` sarà sempre true.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Copiare gli utenti e le entità servizio di un gruppo in un gruppo assegnabile al ruolo

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Uso dell'API Microsoft Graph

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Creare un gruppo assegnabile al ruolo in Azure AD

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

Per questo tipo di gruppo, `isPublic` sarà sempre false e `isSecurityEnabled` sarà sempre true.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare un ruolo a un gruppo cloud](groups-assign-role.md)
- [Usare i gruppi di cloud per gestire le assegnazioni di ruolo](groups-concept.md)
- [Risoluzione dei problemi correlati ai ruoli assegnati ai gruppi cloud](groups-faq-troubleshooting.md)
