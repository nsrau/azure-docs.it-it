---
title: Assegnare ed elencare i ruoli con ambito unità amministrativa-Azure Active Directory | Microsoft Docs
description: Utilizzo di unità amministrative per limitare l'ambito delle assegnazioni di ruolo in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ea638124ed81a494231c814962345c028fc12c9
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940855"
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

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Entità di sicurezza che possono essere assegnate a un ruolo con ambito

Le seguenti entità di sicurezza possono essere assegnate a un ruolo con un ambito di unità amministrativa:

* Utenti
* Gruppi di cloud assegnabili con ruolo (anteprima)
* Nome dell'entità servizio (SPN)

## <a name="assign-a-scoped-role"></a>Assegnare un ruolo con ambito

### <a name="azure-portal"></a>Portale di Azure

Andare ad **Azure AD > Unità amministrative** nel portale. Selezionare l'unità amministrativa a cui si desidera assegnare il ruolo a un utente. Nel riquadro sinistro selezionare ruoli e amministratori per elencare tutti i ruoli disponibili.

![Selezionare un'unità amministrativa per modificare l'ambito del ruolo](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Selezionare il ruolo da assegnare e quindi selezionare **Aggiungi assegnazioni**. Verrà visualizzato un pannello a destra in cui è possibile selezionare uno o più utenti da assegnare al ruolo.

![Selezionare il ruolo da definire come ambito, quindi selezionare Aggiungi assegnazioni](./media/roles-admin-units-assign-roles/select-add-assignment.png)

> [!Note]
>
> Per assegnare un ruolo a un'unità amministrativa usando PIM, seguire [questa procedura.](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-add-role-to-user?tabs=new#assign-a-role-with-restricted-scope)

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /directory/administrativeUnits/{id}/scopedRoleMembers
    
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
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

La sezione evidenziata può essere modificata in base alle esigenze dell'ambiente specifico.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi di cloud per gestire le assegnazioni di ruolo](roles-groups-concept.md)
- [Risoluzione dei problemi correlati ai ruoli assegnati ai gruppi cloud](roles-groups-faq-troubleshooting.md)
