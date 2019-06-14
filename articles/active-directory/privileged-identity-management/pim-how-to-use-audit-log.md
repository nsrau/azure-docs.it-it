---
title: Visualizzare la cronologia di controllo per i ruoli di Azure AD in PIM - Azure Active Directory | Microsoft Docs
description: Informazioni su come visualizzare la cronologia di controllo per i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8061cff8d39db66cb22a5650c7688657aa8b3554
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053964"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Cronologia di controllo di visualizzazione per i ruoli di Azure AD in PIM

È possibile utilizzare la cronologia di controllo di Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per visualizzare tutte le assegnazioni di ruolo e le attivazioni entro ultimi 30 giorni per i ruoli con tutti i privilegi. Se si desidera visualizzare la cronologia di controllo completa dell'attività della directory, inclusi amministratore, utente finale e attività di sincronizzazione, è possibile usare la [report di sicurezza e l'attività di Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Visualizzare la cronologia di controllo

Seguire questi passaggi per visualizzare la cronologia di controllo per i ruoli di Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Ruoli di Azure AD**.

1. Fare clic su **cronologia di controllo dei ruoli della Directory**.

    A seconda la cronologia di controllo, viene visualizzato un istogramma nonché attivazioni totali, numero massimo di attivazioni al giorno e numero medio di attivazioni al giorno.

    ![Cronologia di controllo dei ruoli della directory](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Nella parte inferiore della pagina, viene visualizzata una tabella con le informazioni su ogni azione nella cronologia di controllo disponibili. Le colonne hanno i significati seguenti:

    | Colonna | Descrizione |
    | --- | --- |
    | Time | Quando si è verificata l'azione. |
    | Richiedente | Utente che ha richiesto l'attivazione del ruolo o modifica. Se il valore è **sistema Azure**, controllare la cronologia di controllo di Azure per altre informazioni. |
    | Azione | Azioni eseguite dal richiedente. Le azioni possono includere assegnazione, Annulla l'assegnazione, attiva, disattiva o AddedOutsidePIM. |
    | Membro | Utente che si sta attivando o assegnato a un ruolo. |
    | Ruolo | Ruolo assegnato o attivato dall'utente. |
    | Ragionamento | Testo immesso nel campo del motivo durante l'attivazione. |
    | Scadenza | Quando scade un ruolo attivato. Si applica solo alle assegnazioni di ruolo idonee. |

1. Per ordinare la cronologia di controllo, fare clic il **tempo**, **azione**, e **ruolo** pulsanti.

## <a name="filter-audit-history"></a>Filtrare la cronologia dei controlli

1. Nella parte superiore della pagina della cronologia di controllo, scegliere il **filtro** pulsante.

    Il **aggiornare i parametri del grafico** viene visualizzato il riquadro.

1. Nelle **intervallo di tempo**, selezionare un intervallo di tempo.

1. Nelle **ruoli**, aggiungere segni di spunta per i ruoli che si desidera visualizzare.

    ![Riquadro parametri di aggiornamento del grafico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Fare clic su **** per visualizzare la cronologia di controllo filtrato.

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in PIM](azure-pim-resource-rbac.md)
