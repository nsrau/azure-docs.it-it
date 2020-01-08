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
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a275b08beac842c7d435d77d6b4c1338e817fbc7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430098"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Visualizzare la cronologia di controllo per i ruoli Azure AD in PIM

È possibile usare la cronologia di controllo Privileged Identity Management (PIM) per visualizzare tutte le assegnazioni di ruolo e le attivazioni negli ultimi 30 giorni per tutti i ruoli con privilegi. Se si desidera visualizzare la cronologia di controllo completa dell'attività nell'organizzazione Azure Active Directory (Azure AD), tra cui l'amministratore, l'utente finale e l'attività di sincronizzazione, è possibile utilizzare i [report di sicurezza e attività di Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte Azure AD ruoli di Privileged Identity Management viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure. In questo modo vengono create funzionalità aggiuntive e le [modifiche apportate all'API esistente](azure-ad-roles-features.md#api-changes). Mentre è in corso il rollback della nuova versione, le procedure descritte in questo articolo dipendono dalla versione di Privileged Identity Management attualmente disponibile. Attenersi alla procedura descritta in questa sezione per determinare la versione di Privileged Identity Management. Quando si conosce la versione di Privileged Identity Management, è possibile selezionare le procedure descritte in questo articolo corrispondenti a tale versione.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente appartenente al ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Aprire **Azure AD Privileged Identity Management**. Se si dispone di un banner nella parte superiore della pagina Panoramica, seguire le istruzioni riportate nella scheda **nuova versione** di questo articolo. In caso contrario, seguire le istruzioni riportate nella scheda **versione precedente** .

    ![Nuova versione dei ruoli Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="previous-versiontabprevious"></a>[Versione precedente](#tab/previous)

## <a name="view-audit-history"></a>Visualizzare la cronologia di controllo

Per visualizzare la cronologia di controllo per i ruoli di Azure AD, attenersi alla seguente procedura.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **cronologia di controllo ruoli della directory**.

    A seconda della cronologia di controllo, viene visualizzato un istogramma con le attivazioni totali, le attivazioni massime al giorno e le attivazioni medie al giorno.

    ![Cronologia di controllo dei ruoli della directory](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Nella parte inferiore della pagina viene visualizzata una tabella con informazioni su ogni azione nella cronologia di controllo disponibile. Le colonne hanno i significati seguenti:

    | Colonna | Description |
    | --- | --- |
    | Durata | Quando si è verificata l'azione. |
    | Utente supporto tecnico | Utente che ha richiesto l'attivazione o la modifica del ruolo. Se il valore è **Azure System**, controllare la cronologia di controllo di Azure per altre informazioni. |
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

1. Selezionare **fine** per visualizzare la cronologia di controllo filtrata.

# <a name="new-versiontabnew"></a>[Nuova versione](#tab/new)

Per visualizzare la cronologia di controllo per i ruoli di Azure AD, attenersi alla seguente procedura.

## <a name="view-resource-audit-history"></a>Visualizzare la cronologia dei controlli delle risorse

Il controllo delle risorse offre una visualizzazione di tutte le attività associate ai ruoli del Azure AD.

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **controllo risorse**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo delle risorse con filtri](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Visualizzare Controllo personale

Controllo personale consente di visualizzare l'attività del ruolo personale.

1. Aprire **Azure AD Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare la risorsa per cui si vuole visualizzare la cronologia di controllo.

1. Selezionare **My audit**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo per l'utente corrente](media/azure-pim-resource-rbac/my-audit-time.png)

---

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in Privileged Identity Management](azure-pim-resource-rbac.md)
