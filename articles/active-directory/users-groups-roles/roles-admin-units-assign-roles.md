---
title: Assegnare ed elencare i ruoli con ambito unità amministrativa (anteprima)-Azure Active Directory | Microsoft Docs
description: Utilizzo di unità amministrative per limitare l'ambito delle assegnazioni di ruolo in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 578fb481ec858e65ede49bdce2d8bc26470aa2ca
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850760"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Assegnare ruoli con ambito a un'unità amministrativa

In Azure Active Directory (Azure AD), è possibile assegnare utenti a un ruolo Azure AD con un ambito limitato a una o più unità amministrative (AUs) per un controllo amministrativo più granulare.

Per i passaggi necessari per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Ruoli disponibili

Ruolo  |  Descrizione
----- |  -----------
Amministratore dell'autenticazione  |  Ha accesso per visualizzare, impostare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente non amministratore nell'unità amministrativa assegnata.
Amministratore di gruppi  |  Consente di gestire tutti gli aspetti delle impostazioni di gruppi e gruppi, ad esempio i criteri di denominazione e scadenza, solo nell'unità amministrativa assegnata.
Amministratore del supporto tecnico  |  Può reimpostare le password per gli amministratori non amministratori e helpdesk solo nell'unità amministrativa assegnata.
Amministratore licenze  |  Consente di assegnare, rimuovere e aggiornare le assegnazioni di licenze solo nell'unità amministrativa.
Amministratore password  |  Consente di reimpostare le password per gli amministratori non amministratori e password solo nell'unità amministrativa assegnata.
Amministratore utenti  |  Può gestire tutti gli aspetti di utenti e gruppi, inclusa la reimpostazione delle password per gli amministratori limitati all'interno dell'unità amministrativa assegnata.

## <a name="assign-a-scoped-role"></a>Assegnare un ruolo con ambito

### <a name="azure-portal"></a>Portale di Azure

Andare ad **Azure AD > Unità amministrative** nel portale. Selezionare l'unità amministrativa a cui si desidera assegnare il ruolo a un utente. Nel riquadro sinistro selezionare ruoli e amministratori per elencare tutti i ruoli disponibili.

![Selezionare un'unità amministrativa per modificare l'ambito del ruolo](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selezionare il ruolo da assegnare e quindi selezionare **Aggiungi assegnazioni**. Verrà visualizzato un pannello a destra in cui è possibile selezionare uno o più utenti da assegnare al ruolo.

![Selezionare il ruolo da definire come ambito, quindi selezionare Aggiungi assegnazioni](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
$uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo
```

La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>Elencare gli amministratori con ambito in un AU

### <a name="azure-portal"></a>Portale di Azure

Tutte le assegnazioni di ruolo eseguite con un ambito di unità amministrativa possono essere visualizzate nella [sezione unità amministrative del Azure ad](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). Andare ad **Azure AD > Unità amministrative** nel portale. Selezionare l'unità amministrativa per le assegnazioni di ruolo che si desidera elencare. Selezionare **ruoli e amministratori** e aprire un ruolo per visualizzare le assegnazioni nell'unità amministrativa.

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *
```

La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi e domande frequenti sulle unità amministrative](roles-admin-units-faq-troubleshoot.md)
