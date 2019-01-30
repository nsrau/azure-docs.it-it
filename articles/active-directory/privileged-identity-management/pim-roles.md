---
title: Ruoli che non è possibile gestire in PIM - Azure | Microsoft Docs
description: Questo articolo descrive i ruoli che non è possibile gestire in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 01/18/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: e4c60c6ecdc586ba97a01fa42cd80b8ed541fa61
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434939"
---
# <a name="roles-you-cannot-manage-in-pim"></a>Ruoli che non è possibile gestire in PIM

Azure AD Privileged Identity Management (PIM) consente di gestire tutti i [ruoli della directory di Azure AD](../users-groups-roles/directory-assign-admin-roles.md) e tutti i [ruoli delle risorse di Azure](../../role-based-access-control/built-in-roles.md). Questi ruoli includono anche i ruoli personalizzati associati ai gruppi di gestione, alle sottoscrizioni, ai gruppi di risorse e alle risorse. Esistono tuttavia alcuni ruoli che non è possibile gestire. Questo articolo descrive i ruoli che non possono essere gestiti in PIM.

## <a name="classic-subscription-administrator-roles"></a>Ruoli di amministratore sottoscrizione classica

In PIM non è possibile gestire i ruoli di amministratore sottoscrizione classico seguenti:

- Amministratore dell'account
- Amministratore del servizio
- Coamministratore

Per altre informazioni sui ruoli di amministratore sottoscrizione classico, vedere [Ruoli di amministratore sottoscrizione classico, ruoli di controllo degli accessi in base al ruolo di Azure e ruoli di amministratore di Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Informazioni sui ruoli amministratore di Office 365

I ruoli all'interno di Exchange Online o SharePoint Online, ad eccezione dei ruoli di amministratore di Exchange e amministratore di SharePoint, non sono rappresentati in Azure AD e quindi non possono essere gestiti in PIM. Per altre informazioni su questi servizi di Office 365, vedere [Informazioni sui ruoli di amministratore di Office 365](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Il ruolo di amministratore di SharePoint ha accesso amministrativo a SharePoint Online tramite l'interfaccia di amministrazione di SharePoint Online e può eseguire quasi tutte le attività in SharePoint Online. Potrebbero verificarsi ritardi se gli utenti idonei usano il ruolo all'interno di SharePoint dopo l'attivazione di PIM.

## <a name="next-steps"></a>Passaggi successivi

- [Iniziare a usare PIM](pim-getting-started.md)
- [Assegnare ruoli della directory di Azure AD in PIM](pim-how-to-add-role-to-user.md)
- [Assegnare i ruoli delle risorse di Azure in PIM](pim-resource-roles-assign-roles.md)
