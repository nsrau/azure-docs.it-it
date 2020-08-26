---
title: Gestire gruppi assegnabili ai ruoli come gruppi di accesso con privilegi-Azure AD | Microsoft Docs
description: Consenso all'onboarding di gruppi assegnabili ai ruoli per la gestione come gruppi di accesso con privilegi in Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869533"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Gestire i gruppi di accesso con privilegi (anteprima) in Privileged Identity Management

In Azure Active Directory (Azure AD), è possibile assegnare Azure AD ruoli predefiniti ai gruppi cloud per semplificare la gestione delle assegnazioni di ruolo. Per proteggere i ruoli di Azure AD e per proteggere l'accesso, è ora possibile usare Privileged Identity Management (PIM) per gestire l'accesso JIT per i membri o i proprietari di questi gruppi. Per gestire un gruppo assegnabile al ruolo Azure AD come gruppo di accesso con privilegi in Privileged Identity Management, è necessario gestirlo in PIM.

## <a name="identify-groups-to-manage"></a>Identificare i gruppi da gestire

È possibile creare un gruppo assegnabile al ruolo in Azure AD come descritto in [creare un gruppo assegnabile al ruolo in Azure Active Directory](../users-groups-roles/roles-groups-create-eligible.md). L'utente è proprietario del gruppo per gestirlo in Privileged Identity Management.

1. [Accedere a Azure ad](https://aad.portal.azure.com) con autorizzazioni di amministratore del ruolo con privilegi.
1. Selezionare **gruppi** e quindi selezionare il gruppo assegnabile al ruolo che si desidera gestire. È possibile cercare o filtrare l'elenco.

    ![trovare un gruppo assegnabile ai ruoli da gestire in PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Aprire il gruppo e selezionare **accesso con privilegi (anteprima)**.

    ![Apri l'esperienza Privileged Identity Management](./media/groups-discover-groups/groups-discover-groups.png)

1. Se i gruppi non sono ancora stati sottoposti a gestione in PIM, selezionare **Abilita accesso con privilegi** per il consenso alla gestione. Solo l'amministratore globale o il proprietario di un gruppo può concedere questo consenso.

    ![consenso alla gestione del gruppo in Privileged Identity Management, se necessario](./media/groups-discover-groups/consent-page.png)

1. Iniziare a gestire le assegnazioni in PIM.

    ![Gestire le assegnazioni in Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Una volta gestito, un gruppo di accesso con privilegi non può essere escluso dalla gestione. In questo modo si impedisce a un altro amministratore di rimuovere Privileged Identity Management impostazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le assegnazioni dei gruppi di accesso con privilegi in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare gruppi di accesso con privilegi in Privileged Identity Management](pim-resource-roles-assign-roles.md)
