---
title: Individuare i gruppi assegnabili ai ruoli da gestire in PIM-Azure AD | Microsoft Docs
description: Informazioni su come individuare gruppi assegnabili ai ruoli da gestire come gruppi di accesso con privilegi in Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506004"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>Individuare i gruppi di accesso con privilegi (anteprima) da gestire in Privileged Identity Management

In Azure Active Directory (Azure AD), è possibile assegnare Azure AD ruoli predefiniti ai gruppi cloud per semplificare la gestione delle assegnazioni di ruolo. Ora è possibile usare Privileged Identity Management (PIM) per assegnare l'idoneità per l'appartenenza o la proprietà di questi gruppi, per proteggere i ruoli di Azure AD e per proteggere l'accesso. Prima di poter gestire un gruppo assegnabile al ruolo Azure AD come gruppo di accesso con privilegi in Privileged Identity Management, è necessario individuarlo e portarlo sotto la gestione in PIM.

## <a name="discover-resources"></a>Individuare le risorse

1. [Accedere a Azure ad](https://aad.portal.azure.com) con autorizzazioni ruolo amministratore ruolo con privilegi.
1. Creare un gruppo assegnabile al ruolo in Azure AD. È necessario essere un proprietario del gruppo per individuarlo e gestirlo con Privileged Identity Management.
1. Aprire **Privileged Identity Management**.
1. Selezionare **accesso con privilegi (anteprima)**.

    ![Comando individua gruppi per la prima volta](./media/groups-discover-groups/groups-discover-groups.png)

1. Selezionare **Discover groups**.
1. Cerca per nome gruppo.
1. Selezionare il gruppo e selezionare **Gestisci gruppi** per riportarlo in gestione PIM.

    ![Individuare i gruppi senza risorse elencate per la prima esperienza](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > Una volta gestito, un gruppo di accesso con privilegi non può essere escluso dalla gestione. In questo modo si impedisce a un altro amministratore di risorse di rimuovere Privileged Identity Management impostazioni.

1. Se viene visualizzato un messaggio per confermare l'onboarding della risorsa selezionata per la gestione, selezionare **Sì**.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le assegnazioni dei gruppi di accesso con privilegi in Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Assegnare gruppi di accesso con privilegi in Privileged Identity Management](pim-resource-roles-assign-roles.md)
