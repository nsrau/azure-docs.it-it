---
title: Assegnare ed elencare i ruoli con l'ambito dell'unità amministrativa (anteprima) - Azure Active Directory Documenti Microsoft
description: Utilizzo di unità amministrative per limitare l'ambito delle assegnazioni di ruolo in Azure Active DirectoryUsing administrative units to restrict the scope of role assignments in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870426"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Assegnare ruoli con ambito a un'unità amministrativaAssign scoped roles to an administrative unit

In Azure Active Directory (Azure AD) è possibile assegnare gli utenti a un ruolo di Azure AD con un ambito limitato a una o più unità amministrative (AU) per un controllo amministrativo più granulare.

Per la procedura di preparazione all'utilizzo di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione.](roles-admin-units-manage.md#get-started)

## <a name="roles-available"></a>Ruoli disponibili

Ruolo  |  Descrizione
----- |  -----------
Amministratore dell'autenticazione  |  Consente di visualizzare, impostare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente non amministratore solo nell'unità amministrativa assegnata.
Amministratore gruppi  |  Può gestire tutti gli aspetti delle impostazioni di gruppi e gruppi, ad esempio i criteri di denominazione e di scadenza solo nell'unità amministrativa assegnata.
Amministratore del supporto tecnico  |  Può reimpostare le password solo per gli amministratori non amministratori e gli amministratori del supporto tecnico nell'unità amministrativa assegnata.
Amministratore licenze  |  Può assegnare, rimuovere e aggiornare le assegnazioni delle licenze solo all'interno dell'unità amministrativa.
Amministratore password  |  Può reimpostare le password solo per gli utenti non amministratori e gli amministratori delle password all'interno dell'unità amministrativa assegnata.
Amministratore utenti  |  Può gestire tutti gli aspetti di utenti e gruppi, inclusa la reimpostazione delle password per gli amministratori con password limitata solo all'interno dell'unità amministrativa assegnata.

## <a name="assign-a-scoped-role"></a>Assegnare un ruolo con ambitoAssign a scoped role

### <a name="azure-portal"></a>Portale di Azure

Passare ad **Azure AD > unità amministrative** nel portale. Selezionare l'unità amministrativa a cui si desidera assegnare il ruolo a un utente. Nel riquadro sinistro selezionare Ruoli e amministratori per elencare tutti i ruoli disponibili.

![Selezionare un'unità amministrativa per modificare l'ambito del ruolo](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selezionare il ruolo da assegnare, quindi selezionare **Aggiungi assegnazioni**. Questa diapositiva aprirà un pannello a destra in cui è possibile selezionare uno o più utenti da assegnare al ruolo.

![Selezionare il ruolo da definire l'ambito e quindi selezionare Aggiungi assegnazioni](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>Elencare gli amministratori con ambito in un'Unione africanaList the scoped admins on an AU

### <a name="azure-portal"></a>Portale di Azure

Tutte le assegnazioni di ruolo eseguite con un ambito di unità amministrativa possono essere visualizzate nella [sezione Unità amministrative di Azure AD.](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) Passare ad **Azure AD > unità amministrative** nel portale. Selezionare l'unità di amministrazione per le assegnazioni di ruolo che si desidera elencare. Selezionare **Ruoli e amministratori** e aprire un ruolo per visualizzare le assegnazioni nell'unità di amministrazione.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi relativi alle unità amministrative e domande frequenti](roles-admin-units-faq-troubleshoot.md)
