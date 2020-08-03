---
title: Visualizzare il report di controllo per le assegnazioni di gruppi di accesso con privilegi in Privileged Identity Management (PIM)-Azure AD | Microsoft Docs
description: Visualizzare le attività e la cronologia di controllo per le assegnazioni dei gruppi di accesso con privilegi in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9bbc90776ca007b84d5f67c50f8550ee9c881c7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506043"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Cronologia delle attività di controllo per le assegnazioni di gruppi di accesso con privilegi (anteprima) in Privileged Identity Management

Con Privileged Identity Management (PIM) è possibile visualizzare le attività, le attivazioni e la cronologia di controllo per i membri e i proprietari del gruppo di accesso con privilegi di Azure all'interno dell'organizzazione di Azure Active Directory (Azure AD).

> [!NOTE]
> Se l'organizzazione dispone di funzioni di gestione esternalizzate a un provider di servizi che usa la [gestione delle risorse delegate di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), le assegnazioni di ruolo autorizzate dal provider di servizi non verranno visualizzate qui.

Seguire questa procedura per visualizzare la cronologia di controllo per i gruppi di accesso con privilegi.

## <a name="view-resource-audit-history"></a>Visualizzare la cronologia dei controlli delle risorse

Il **controllo delle risorse** offre una visualizzazione di tutte le attività associate ai gruppi di accesso con privilegi.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **accesso con privilegi (anteprima)**.

1. In **attività**selezionare **controllo risorse**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo delle risorse con filtri](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>Visualizzare Controllo personale

Controllo personale consente di visualizzare l'attività del ruolo personale.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **accesso con privilegi (anteprima)**.

1. Selezionare il membro o il gruppo per cui si desidera visualizzare la cronologia di controllo.

1. Selezionare **My audit**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo per l'utente corrente](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnazione dell'appartenenza a un gruppo e della proprietà in Privileged Identity Management](groups-assign-member-owner.md)
- [Approva o rifiuta le richieste per i gruppi di accesso con privilegi in PIM](groups-approval-workflow.md)
- [Visualizzare la cronologia di controllo per i ruoli Azure AD in PIM](groups-audit.md)
