---
title: Visualizzare i ruoli assegnati a un gruppo in Azure Active Directory | Microsoft Docs
description: Informazioni sul modo in cui i ruoli assegnati a un gruppo possono essere visualizzati usando Azure AD interfaccia di amministrazione. I gruppi di visualizzazione e i ruoli assegnati sono le autorizzazioni utente predefinite.
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
ms.openlocfilehash: e578c90a05085df33c2d547402256cfc38229aa3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377539"
---
# <a name="view-roles-assigned-to-a-group-in-azure-active-directory"></a>Visualizzare i ruoli assegnati a un gruppo in Azure Active Directory

Questa sezione descrive il modo in cui i ruoli assegnati a un gruppo possono essere visualizzati usando Azure AD interfaccia di amministrazione. I gruppi di visualizzazione e i ruoli assegnati sono le autorizzazioni utente predefinite.

1. Accedere al centro di [amministrazione Azure ad](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con le credenziali di amministratore o non amministratore.

1. Selezionare il gruppo a cui si è interessati.

1. Selezionare **ruoli assegnati**. È ora possibile visualizzare tutti i ruoli di Azure AD assegnati a questo gruppo.

   ![Visualizza tutti i ruoli assegnati a un gruppo selezionato](./media/groups-view-assignments/view-assignments.png)

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

- [Usare i gruppi di cloud per gestire le assegnazioni di ruolo](groups-concept.md)
- [Risoluzione dei problemi correlati ai ruoli assegnati ai gruppi cloud](groups-faq-troubleshooting.md)
