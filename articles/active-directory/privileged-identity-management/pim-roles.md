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
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607531"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Ruoli che non è possibile gestire in Gestione identità con privilegi

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) consente di gestire tutti i ruoli di [Azure AD](../users-groups-roles/directory-assign-admin-roles.md) e tutti i ruoli di [Azure.](../../role-based-access-control/built-in-roles.md) I ruoli di Azure possono includere anche i ruoli personalizzati associati ai gruppi di gestione, alle sottoscrizioni, ai gruppi di risorse e alle risorse. Esistono tuttavia alcuni ruoli che non è possibile gestire. In questo articolo vengono descritti i ruoli che non è possibile gestire in Gestione identità con privilegi.

## <a name="classic-subscription-administrator-roles"></a>Ruoli di amministratore sottoscrizione classica

In Gestione identità con privilegi non è possibile gestire i ruoli di amministratore della sottoscrizione classica seguenti:You cannot manage the following classic subscription administrator roles in Privileged Identity Management:

- Amministratore dell'account
- Amministratore del servizio
- Coamministratore

Per altre informazioni sui ruoli di amministratore sottoscrizione classico, vedere [Ruoli di amministratore sottoscrizione classico, ruoli di controllo degli accessi in base al ruolo di Azure e ruoli di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Informazioni sui ruoli amministratore di Office 365

Tutti i ruoli di Office365 nel portale Ruoli e amministratori di Azure AD sono supportati, ad esempio Amministratore di Exchange e Amministratore di SharePoint, ma non sono supportati ruoli specifici all'interno di Rbac di Exchange o RBAC di SharePoint. Per altre informazioni su questi servizi di Office 365, vedere [Informazioni sui ruoli di amministratore di Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Gli utenti idonei per il ruolo di amministratore di SharePoint e per i ruoli che tentano di accedere al Centro sicurezza e conformità Microsoft potrebbero subire ritardi fino a poche ore dopo l'attivazione del ruolo. Stiamo lavorando con questi team per risolvere i problemi.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Gestione identità con privilegiAssign Azure AD roles in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Assegnare ruoli delle risorse di Azure in Gestione identità con privilegiAssign Azure resource roles in Privileged Identity Management](pim-resource-roles-assign-roles.md)
