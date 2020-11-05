---
title: Assegnare ed elencare i ruoli con ambito unità amministrativa-Azure Active Directory | Microsoft Docs
description: Usare unità amministrative per limitare l'ambito delle assegnazioni di ruolo in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ed2741c7dd754127a57642703b650a70637c63
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93393439"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Assegnare ruoli con ambito a un'unità amministrativa

In Azure Active Directory (Azure AD), per un controllo amministrativo più granulare, è possibile assegnare utenti a un ruolo di Azure AD con un ambito limitato a una o più unità amministrative.

Per preparare l'uso di PowerShell e Microsoft Graph per la gestione delle unità amministrative, vedere [Introduzione](admin-units-manage.md#get-started).

## <a name="available-roles"></a>Ruoli disponibili

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
* Gruppi di cloud assegnabili al ruolo (anteprima)
* Nome dell'entità servizio (SPN)

## <a name="assign-a-scoped-role"></a>Assegnare un ruolo con ambito

È possibile assegnare un ruolo con ambito usando il portale di Azure, PowerShell o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Nella portale di Azure passare a **Azure ad**.

1. Selezionare **unità amministrative** , quindi selezionare l'unità amministrativa a cui si desidera assegnare un ambito del ruolo utente. 

1. Nel riquadro sinistro selezionare **ruoli e amministratori** per elencare tutti i ruoli disponibili.

   ![Screenshot del riquadro "ruolo e amministratori" per la selezione di un'unità amministrativa di cui si desidera assegnare l'ambito del ruolo.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Selezionare il ruolo da assegnare e quindi selezionare **Aggiungi assegnazioni**. 

1. Nel riquadro **Aggiungi assegnazioni** selezionare uno o più utenti da assegnare al ruolo.

   ![Selezionare il ruolo da definire come ambito, quindi selezionare Aggiungi assegnazioni](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Per assegnare un ruolo a un'unità amministrativa usando Azure AD Privileged Identity Management (PIM), vedere [assegnare Azure ad ruoli in PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Usare PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

È possibile modificare la sezione evidenziata come richiesto per l'ambiente specifico.

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

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

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Visualizzare un elenco degli amministratori con ambito in un'unità amministrativa

È possibile visualizzare un elenco di amministratori con ambito usando il portale di Azure, PowerShell o Microsoft Graph.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

È possibile visualizzare tutte le assegnazioni di ruolo create con un ambito di unità amministrativa nella [sezione unità amministrative del Azure ad](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. Nella portale di Azure passare a **Azure ad**.

1. Nel riquadro sinistro selezionare **unità amministrative** , quindi selezionare l'unità amministrativa per l'elenco di assegnazioni di ruolo che si desidera visualizzare. 

1. Selezionare **ruoli e amministratori** , quindi aprire un ruolo per visualizzare le assegnazioni nell'unità amministrativa.

### <a name="use-powershell"></a>Usare PowerShell

```powershell
$administrativeUnit = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

È possibile modificare la sezione evidenziata come richiesto per l'ambiente specifico.

### <a name="use-microsoft-graph"></a>USA Microsoft Graph

```http
Http request
GET /directory/administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi cloud per gestire le assegnazioni di ruoli](groups-concept.md)
- [Risolvere i problemi relativi ai ruoli assegnati ai gruppi cloud](groups-faq-troubleshooting.md)
