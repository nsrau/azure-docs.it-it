---
title: Ruoli che non è possibile gestire in Privileged Identity Management-Azure Active Directory | Microsoft Docs
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
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895193"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Ruoli che non è possibile gestire in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) consente di gestire tutti i [ruoli di Azure ad](../users-groups-roles/directory-assign-admin-roles.md) e tutti i [ruoli delle risorse di Azure](../../role-based-access-control/built-in-roles.md). Questi ruoli includono anche i ruoli personalizzati associati ai gruppi di gestione, alle sottoscrizioni, ai gruppi di risorse e alle risorse. Esistono tuttavia alcuni ruoli che non è possibile gestire. Questo articolo descrive i ruoli che non è possibile gestire in Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Ruoli di amministratore sottoscrizione classica

Non è possibile gestire i seguenti ruoli di amministratore della sottoscrizione classica in Privileged Identity Management:

- Amministratore dell'account
- Amministratore del servizio
- Coamministratore

Per altre informazioni sui ruoli di amministratore sottoscrizione classico, vedere [Ruoli di amministratore sottoscrizione classico, ruoli di controllo degli accessi in base al ruolo di Azure e ruoli di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Informazioni sui ruoli amministratore di Office 365

I ruoli all'interno di Exchange Online o SharePoint Online, ad eccezione dell'amministratore di Exchange e di SharePoint, non sono rappresentati in Azure AD e pertanto non possono essere gestiti in Privileged Identity Management. Per altre informazioni su questi servizi di Office 365, vedere [Informazioni sui ruoli di amministratore di Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Il ruolo di amministratore di SharePoint ha accesso amministrativo a SharePoint Online tramite l'interfaccia di amministrazione di SharePoint Online e può eseguire quasi tutte le attività in SharePoint Online. Gli utenti idonei possono riscontrare ritardi utilizzando questo ruolo all'interno di SharePoint dopo l'attivazione in Privileged Identity Management.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-assign-roles.md)
