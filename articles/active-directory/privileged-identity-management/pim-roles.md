---
title: Ruoli che non è possibile gestire in Gestione delle identità con privilegi - Azure Active Directory . Documenti Microsoft
description: Questo articolo descrive i ruoli che non è possibile gestire in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72895193"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Ruoli che non è possibile gestire in Gestione identità con privilegi

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) consente di gestire tutti i ruoli di Azure AD e tutti i ruoli delle risorse di Azure.Azure Active Directory (Azure AD) Privileged Identity Management (PIM) enables you to manage all [Azure AD roles](../users-groups-roles/directory-assign-admin-roles.md) and all Azure resource [roles](../../role-based-access-control/built-in-roles.md). Questi ruoli includono anche i ruoli personalizzati associati ai gruppi di gestione, alle sottoscrizioni, ai gruppi di risorse e alle risorse. Esistono tuttavia alcuni ruoli che non è possibile gestire. In questo articolo vengono descritti i ruoli che non è possibile gestire in Gestione identità con privilegi.

## <a name="classic-subscription-administrator-roles"></a>Ruoli di amministratore sottoscrizione classica

In Gestione identità con privilegi non è possibile gestire i ruoli di amministratore della sottoscrizione classica seguenti:You cannot manage the following classic subscription administrator roles in Privileged Identity Management:

- Amministratore dell'account
- Amministratore del servizio
- Coamministratore

Per altre informazioni sui ruoli di amministratore sottoscrizione classico, vedere [Ruoli di amministratore sottoscrizione classico, ruoli di controllo degli accessi in base al ruolo di Azure e ruoli di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Informazioni sui ruoli amministratore di Office 365

I ruoli all'interno di Exchange Online o SharePoint Online, ad eccezione di Amministratore di Exchange e Amministratore di SharePoint, non sono rappresentati in Azure AD e pertanto non possono essere gestiti in Gestione identità con privilegi. Per altre informazioni su questi servizi di Office 365, vedere [Informazioni sui ruoli di amministratore di Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Il ruolo di amministratore di SharePoint ha accesso amministrativo a SharePoint Online tramite l'interfaccia di amministrazione di SharePoint Online e può eseguire quasi tutte le attività in SharePoint Online. Gli utenti idonei potrebbero verificarsi ritardi nell'utilizzo di questo ruolo all'interno di SharePoint dopo l'attivazione in Gestione identità privilegiate.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Gestione identità con privilegiAssign Azure AD roles in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Assegnare ruoli delle risorse di Azure in Gestione identità con privilegiAssign Azure resource roles in Privileged Identity Management](pim-resource-roles-assign-roles.md)
