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
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72587c5486ed61215fd20c215a1dd194f4b7bc4
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372413"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Ruoli che non è possibile gestire in Privileged Identity Management

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) consente di gestire tutti i [ruoli di Azure ad](../roles/permissions-reference.md) e tutti i [ruoli di Azure](../../role-based-access-control/built-in-roles.md). I ruoli di Azure possono includere anche i ruoli personalizzati collegati a gruppi di gestione, sottoscrizioni, gruppi di risorse e risorse. Esistono tuttavia alcuni ruoli che non è possibile gestire. Questo articolo descrive i ruoli che non è possibile gestire in Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Ruoli di amministratore sottoscrizione classica

Non è possibile gestire i seguenti ruoli di amministratore della sottoscrizione classica in Privileged Identity Management:

- Amministratore dell'account
- Amministratore del servizio
- Coamministratore

Per altre informazioni sui ruoli di amministratore della sottoscrizione classica, vedere ruoli di [amministratore della sottoscrizione classica, ruoli di Azure e ruoli di amministratore Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>Quali sono i ruoli di amministratore Microsoft 365?

Sono supportati tutti i ruoli di Microsoft 365 nell'esperienza del portale per gli amministratori e i ruoli di Azure AD, ad esempio amministratore di Exchange e amministratore di SharePoint, ma non sono supportati ruoli specifici all'interno di Exchange RBAC o di SharePoint RBAC. Per ulteriori informazioni su questi servizi Microsoft 365, vedere [Microsoft 365 ruoli di amministratore](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Gli utenti idonei per il ruolo di amministratore di SharePoint, il ruolo di amministratore del dispositivo e tutti i ruoli che tentano di accedere a Microsoft Security and Compliance Center potrebbero subire ritardi di poche ore dopo l'attivazione del ruolo. Microsoft sta collaborando con i team per risolvere i problemi.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli di Azure AD in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-assign-roles.md)