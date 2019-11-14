---
title: Visualizzare il report di controllo per i ruoli Azure AD in PIM-Azure AD | Microsoft Docs
description: Informazioni su come visualizzare la cronologia di controllo per i ruoli Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c4a157d8d5bcd281ca9fee488e58c455034e898
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022070"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Visualizzare la cronologia di controllo per i ruoli Azure AD in PIM

È possibile usare la cronologia di controllo Privileged Identity Management (PIM) per visualizzare tutte le assegnazioni di ruolo e le attivazioni negli ultimi 30 giorni per tutti i ruoli con privilegi. Se si desidera visualizzare la cronologia di controllo completa dell'attività nell'organizzazione Azure Active Directory (Azure AD), tra cui l'amministratore, l'utente finale e l'attività di sincronizzazione, è possibile utilizzare i [report di sicurezza e attività di Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Visualizzare la cronologia di controllo

Per visualizzare la cronologia di controllo per i ruoli di Azure AD, attenersi alla seguente procedura.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **ruoli directory cronologia di controllo**.

    A seconda della cronologia di controllo, viene visualizzato un istogramma con le attivazioni totali, le attivazioni massime al giorno e le attivazioni medie al giorno.

    ![Cronologia di controllo dei ruoli della directory](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Nella parte inferiore della pagina viene visualizzata una tabella con informazioni su ogni azione nella cronologia di controllo disponibile. Le colonne hanno i significati seguenti:

    | Colonna | DESCRIZIONE |
    | --- | --- |
    | Time | Quando si è verificata l'azione. |
    | Richiedente | Utente che ha richiesto l'attivazione o la modifica del ruolo. Se il valore è **Azure System**, controllare la cronologia di controllo di Azure per altre informazioni. |
    | Azione | Azioni eseguite dal richiedente. Le azioni possono includere Assign, Unassign, Activate, disactivate o AddedOutsidePIM. |
    | Membro | Utente che sta attivando o assegnata a un ruolo. |
    | Ruolo | Ruolo assegnato o attivato dall'utente. |
    | Ragionamento | Testo immesso nel campo motivo durante l'attivazione. |
    | Scadenza | Quando un ruolo attivato scade. Si applica solo alle assegnazioni di ruolo idonee. |

1. Per ordinare la cronologia di controllo, fare clic sui pulsanti **ora**, **azione**e **ruolo** .

## <a name="filter-audit-history"></a>Filtrare la cronologia dei controlli

1. Nella parte superiore della pagina cronologia di controllo fare clic sul pulsante **filtro** .

    Viene visualizzato il riquadro **Aggiorna parametri del grafico** .

1. In **intervallo di tempo**selezionare un intervallo di tempo.

1. In **ruoli**selezionare le caselle di controllo per indicare i ruoli che si desidera visualizzare.

    ![Riquadro Aggiorna parametri grafico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Fare clic su **fine** per visualizzare la cronologia di controllo filtrata.

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in Privileged Identity Management](azure-pim-resource-rbac.md)
