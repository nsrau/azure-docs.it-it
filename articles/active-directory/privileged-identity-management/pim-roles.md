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
ms.date: 03/31/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6eaa50f57dd8037ef0ad96b69284f565bd3558f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80607531"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Ruoli che non è possibile gestire in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) consente di gestire tutti i [ruoli di Azure ad](../users-groups-roles/directory-assign-admin-roles.md) e tutti i [ruoli di Azure](../../role-based-access-control/built-in-roles.md). I ruoli di Azure possono includere anche i ruoli personalizzati collegati a gruppi di gestione, sottoscrizioni, gruppi di risorse e risorse. Esistono tuttavia alcuni ruoli che non è possibile gestire. Questo articolo descrive i ruoli che non è possibile gestire in Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Ruoli di amministratore sottoscrizione classica

Non è possibile gestire i seguenti ruoli di amministratore della sottoscrizione classica in Privileged Identity Management:

- Amministratore dell'account
- Amministratore del servizio
- Coamministratore

Per altre informazioni sui ruoli di amministratore sottoscrizione classico, vedere [Ruoli di amministratore sottoscrizione classico, ruoli di controllo degli accessi in base al ruolo di Azure e ruoli di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Informazioni sui ruoli amministratore di Office 365

Sono supportati tutti i ruoli Office365 nell'esperienza del portale degli amministratori e ruoli di Azure AD, ad esempio amministratore di Exchange e amministratore di SharePoint, ma non sono supportati ruoli specifici all'interno di Exchange RBAC o di SharePoint RBAC. Per altre informazioni su questi servizi di Office 365, vedere [Informazioni sui ruoli di amministratore di Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Gli utenti idonei per il ruolo di amministratore di SharePoint, nonché i ruoli che tentano di accedere a Microsoft Security and Compliance Center potrebbero subire ritardi fino a poche ore dopo l'attivazione del ruolo. Microsoft sta collaborando con i team per risolvere i problemi.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-assign-roles.md)
