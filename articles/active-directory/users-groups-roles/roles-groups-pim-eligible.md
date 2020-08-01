---
title: Assegnare un ruolo a un gruppo utilizzando Privileged Identity Management in Azure AD | Microsoft Docs
description: Anteprima dei ruoli di Azure AD personalizzati per la delega della gestione delle identità. Gestire i ruoli di Azure nel portale di Azure, in PowerShell o nell'API Graph.
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
ms.openlocfilehash: 343da87048cf43c04a137376e9a7f24270ce729a
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476096"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Assegnare un ruolo a un gruppo utilizzando Privileged Identity Management

Questo articolo descrive come assegnare un ruolo di Azure Active Directory (Azure AD) a un gruppo usando Azure AD Privileged Identity Management (PIM).

> [!NOTE]
> Per poter assegnare un gruppo a un ruolo di Azure AD usando PIM, è necessario usare la versione aggiornata di Privileged Identity Management. È possibile che si usi una versione precedente di PIM se l'organizzazione Azure AD utilizza l'API Privileged Identity Management. In tal caso, contattare l'alias pim_preview@microsoft.com per spostare l'organizzazione e aggiornare l'API. Per altre informazioni [, vedere Azure ad Roles and features in PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Uso di Azure AD interfaccia di amministrazione

1. Accedere a [Azure ad Privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) come amministratore del ruolo con privilegi o amministratore globale nell'organizzazione.

1. Selezionare **Privileged Identity Management**  >  ruoli di**Azure ad ruoli**  >  **Roles**  >  **Aggiungi assegnazioni**

1. Selezionare un ruolo e quindi selezionare un gruppo. Vengono visualizzati solo i gruppi idonei per l'assegnazione di ruolo (gruppi assegnabili al ruolo), non tutti i gruppi.

    ![selezionare l'utente a cui verrà assegnato il ruolo](./media/roles-groups-pim-eligible/select-member.png)

1. Selezionare l'impostazione di appartenenza desiderata. Per i ruoli che richiedono l'attivazione, scegliere **idoneo**. Per impostazione predefinita, l'utente è idoneo in modo permanente, ma è anche possibile impostare un'ora di inizio e di fine per l'idoneità dell'utente. Al termine, fare clic su Save (Salva) e aggiungere per completare l'assegnazione di ruolo.

    ![selezionare l'utente a cui verrà assegnato il ruolo](./media/roles-groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>Utilizzo di PowerShell

### <a name="download-the-azure-ad-preview-powershell-module"></a>Scaricare il modulo PowerShell di Azure AD Preview

Per installare il modulo Azure AD #PowerShell, usare i cmdlet seguenti:

```powershell
install-module azureadpreview
import-module azureadpreview
```

Per verificare che il modulo sia pronto per l'uso, usare il cmdlet seguente:

```powershell
get-module azureadpreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Assegnare un gruppo come membro idoneo di un ruolo

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Uso dell'API Microsoft Graph

```powershell
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests  

{

 "roleDefinitionId": {roleDefinitionId},

 "resourceId": {tenantId},

 "subjectId": {GroupId},

 "assignmentState": "Eligible",

 "type": "AdminAdd",

 "reason": "reason string",

 "schedule": {

   "startDateTime": {DateTime},

   "endDateTime": {DateTime},

   "type": "Once"

 }

}
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi di cloud per gestire le assegnazioni di ruolo](roles-groups-concept.md)
- [Risoluzione dei problemi assegnati ai gruppi di cloud](roles-groups-faq-troubleshooting.md)
- [Configurare le impostazioni del ruolo amministratore Azure AD in Privileged Identity Management](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md)
