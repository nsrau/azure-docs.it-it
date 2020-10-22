---
title: Identificare un gruppo da gestire in Privileged Identity Management-Azure AD | Microsoft Docs
description: Informazioni su come eseguire l'onboarding dei gruppi assegnabili ai ruoli per gestire i gruppi di accesso con privilegi in Privileged Identity Management (PIM).
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
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ef27475509c59b3c0caf194fd85530f3fdd6c7c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367840"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Portare i gruppi di accesso con privilegi (anteprima) in Privileged Identity Management

In Azure Active Directory (Azure AD), è possibile assegnare Azure AD ruoli predefiniti ai gruppi cloud per semplificare la gestione delle assegnazioni di ruolo. Per proteggere i ruoli di Azure AD e per proteggere l'accesso, è ora possibile usare Privileged Identity Management (PIM) per gestire l'accesso JIT per i membri o i proprietari di questi gruppi. Per gestire un gruppo assegnabile al ruolo Azure AD come gruppo di accesso con privilegi in Privileged Identity Management, è necessario gestirlo in PIM.

## <a name="identify-groups-to-manage"></a>Identificare i gruppi da gestire

È possibile creare un gruppo assegnabile al ruolo in Azure AD come descritto in [creare un gruppo assegnabile al ruolo in Azure Active Directory](../roles/groups-create-eligible.md). L'utente è proprietario del gruppo per gestirlo con Privileged Identity Management.

1. [Accedere a Azure ad](https://aad.portal.azure.com) con autorizzazioni ruolo amministratore ruolo con privilegi.
1. Selezionare **gruppi** e quindi selezionare il gruppo assegnabile al ruolo che si vuole gestire in PIM. È possibile cercare e filtrare l'elenco.

    ![trovare un gruppo assegnabile ai ruoli da gestire in PIM](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Aprire il gruppo e selezionare **accesso con privilegi (anteprima)**.

    ![Apri l'esperienza Privileged Identity Management](./media/groups-discover-groups/groups-discover-groups.png)

1. Iniziare a gestire le assegnazioni in PIM.

    ![Gestire le assegnazioni in Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Una volta gestito, un gruppo di accesso con privilegi non può essere escluso dalla gestione. In questo modo si impedisce a un altro amministratore di risorse di rimuovere Privileged Identity Management impostazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le assegnazioni dei gruppi di accesso con privilegi in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare gruppi di accesso con privilegi in Privileged Identity Management](pim-resource-roles-assign-roles.md)
