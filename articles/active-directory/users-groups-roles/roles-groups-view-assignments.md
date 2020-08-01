---
title: Visualizzare i ruoli assegnati a un gruppo in Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 3b7613fb70299a70e4389b97c2647a26cb7c3374
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476088"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Visualizzare i ruoli assegnati a un gruppo in Azure Active Directory

Questa sezione descrive il modo in cui i ruoli assegnati a un gruppo possono essere visualizzati usando Azure AD interfaccia di amministrazione. I gruppi di visualizzazione e i ruoli assegnati sono le autorizzazioni utente predefinite.

1. Accedere al centro di [amministrazione Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con le credenziali di amministratore o non amministratore.

1. Selezionare il gruppo a cui si è interessati.

1. Selezionare **ruoli assegnati**. È ora possibile visualizzare tutti i ruoli di Azure AD assegnati a questo gruppo.

   ![Visualizza tutti i ruoli assegnati a un gruppo selezionato](./media/roles-groups-view-assignments/view-assignments.png)

## <a name="using-powershell"></a>Utilizzo di PowerShell

### <a name="get-object-id-of-the-group"></a>Ottenere l'ID oggetto del gruppo

```powershell
Get-AzureADMSGroup -SearchString “Contoso_Helpdesk_Administrators”
```

### <a name="view-role-assignment-to-a-group"></a>Visualizzare l'assegnazione di ruolo a un gruppo

```powershell
Get-AzureADMSRoleAssignment -Filter "principalId eq '<object id of group>" 
```

## <a name="using-microsoft-graph-api"></a>Uso dell'API Microsoft Graph

### <a name="get-object-id-of-the-group"></a>Ottenere l'ID oggetto del gruppo

```powershell
GET https://graph.microsoft.com/beta/groups?$filter displayName eq ‘Contoso_Helpdesk_Administrator’ 
```

### <a name="get-role-assignments-to-a-group"></a>Ottenere le assegnazioni di ruolo a un gruppo

```powershell
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq
```

## <a name="next-steps"></a>Passaggi successivi

- [Usare i gruppi di cloud per gestire le assegnazioni di ruolo](roles-groups-concept.md)
- [Risoluzione dei problemi assegnati ai gruppi di cloud](roles-groups-faq-troubleshooting.md)
