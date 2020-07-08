---
title: Visualizzare il report del log di controllo per i ruoli Azure AD in Azure AD PIM | Microsoft Docs
description: Informazioni su come visualizzare la cronologia dei log di controllo per i ruoli Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a5a20498ccd0a133c6e02d366af690acf2665e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742267"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Visualizzare la cronologia di controllo per i ruoli di Azure AD in Privileged Identity Management

È possibile usare la cronologia di controllo Privileged Identity Management (PIM) per visualizzare tutte le assegnazioni di ruolo e le attivazioni negli ultimi 30 giorni per tutti i ruoli con privilegi. Se si desidera visualizzare la cronologia di controllo completa dell'attività nell'organizzazione Azure Active Directory (Azure AD), tra cui l'amministratore, l'utente finale e l'attività di sincronizzazione, è possibile utilizzare i [report di sicurezza e attività di Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte Azure AD ruoli di Privileged Identity Management viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure. In questo modo vengono create funzionalità aggiuntive e le [modifiche apportate all'API esistente](azure-ad-roles-features.md#api-changes). Mentre è in corso il rollback della nuova versione, le procedure descritte in questo articolo dipendono dalla versione di Privileged Identity Management attualmente disponibile. Attenersi alla procedura descritta in questa sezione per determinare la versione di Privileged Identity Management. Quando si conosce la versione di Privileged Identity Management, è possibile selezionare le procedure descritte in questo articolo corrispondenti a tale versione.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente appartenente al ruolo di [amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Aprire **Azure ad Privileged Identity Management**. Se si dispone di un banner nella parte superiore della pagina Panoramica, seguire le istruzioni riportate nella scheda **nuova versione** di questo articolo. In caso contrario, seguire le istruzioni riportate nella scheda **versione precedente** .

    [![Nuova versione dei ruoli Azure AD](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Selezionare la scheda per la versione")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nuova versione](#tab/new)

Per visualizzare la cronologia di controllo per i ruoli di Azure AD, attenersi alla seguente procedura.

## <a name="view-resource-audit-history"></a>Visualizzare la cronologia dei controlli delle risorse

Il controllo delle risorse offre una visualizzazione di tutte le attività associate ai ruoli del Azure AD.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **controllo risorse**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo delle risorse con filtri](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Visualizzare Controllo personale

Controllo personale consente di visualizzare l'attività del ruolo personale.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare la risorsa per cui si vuole visualizzare la cronologia di controllo.

1. Selezionare **My audit**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo per l'utente corrente](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="view-audit-history"></a>Visualizzare la cronologia di controllo

Per visualizzare la cronologia di controllo per i ruoli di Azure AD, attenersi alla seguente procedura.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. Selezionare **cronologia di controllo ruoli della directory**.

    A seconda della cronologia di controllo, viene visualizzato un istogramma con le attivazioni totali, le attivazioni massime al giorno e le attivazioni medie al giorno.

    [![Nuova versione dei ruoli Azure AD](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Visualizzare la cronologia di controllo dei ruoli della directory")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Nella parte inferiore della pagina viene visualizzata una tabella con informazioni su ogni azione nella cronologia di controllo disponibile. Le colonne hanno i significati seguenti:

    | Colonna | Descrizione |
    | --- | --- |
    | Tempo | Quando si è verificata l'azione. |
    | Richiedente | Utente che ha richiesto l'attivazione o la modifica del ruolo. Se il valore è **Azure System**, controllare la cronologia di controllo di Azure per altre informazioni. |
    | Action | Azioni eseguite dal richiedente. Le azioni possono includere Assign, Unassign, Activate, disactivate o AddedOutsidePIM. |
    | Membro | Utente che sta attivando o assegnata a un ruolo. |
    | Ruolo | Ruolo assegnato o attivato dall'utente. |
    | Motivo | Testo immesso nel campo motivo durante l'attivazione. |
    | Scadenza | Quando un ruolo attivato scade. Si applica solo alle assegnazioni di ruolo idonee. |

1. Per ordinare la cronologia di controllo, fare clic sui pulsanti **ora**, **azione**e **ruolo** .

## <a name="filter-audit-history"></a>Filtrare la cronologia dei controlli

1. Nella parte superiore della pagina cronologia di controllo fare clic sul pulsante **filtro** .

    Viene visualizzato il riquadro **Aggiorna parametri del grafico** .

1. In **intervallo di tempo**selezionare un intervallo di tempo.

1. In **ruoli**selezionare le caselle di controllo per indicare i ruoli che si desidera visualizzare.

    ![Riquadro Aggiorna parametri grafico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selezionare **fine** per visualizzare la cronologia di controllo filtrata.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Ottieni motivo, responsabile approvazione e numero di ticket per gli eventi di approvazione

1. Accedere al [portale di Azure](https://aad.portal.azure.com) con le autorizzazioni del ruolo amministratore del ruolo con privilegi e aprire Azure ad.
1. Selezionare **Log di controllo**.
1. Usare il filtro del **servizio** per visualizzare solo gli eventi di controllo per il servizio Privileged Identity Management. Nella pagina **log di controllo** è possibile:

    - Vedere la causa di un evento di controllo nella colonna **motivo dello stato** .
    - Vedere il responsabile approvazione nella colonna **Iniziato da (attore)** per l'evento "Aggiungi membro a richiesta di ruolo approvato".

    [![Nuova versione dei ruoli Azure AD](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrare il log di controllo per il servizio PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Selezionare un evento del registro di controllo per visualizzare il numero del ticket nella scheda **attività** del riquadro **Dettagli** .
  
    [![Nuova versione dei ruoli Azure AD](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Controllare il numero di ticket per l'evento di controllo")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. È possibile visualizzare il richiedente (persona che attiva il ruolo) nella scheda **destinazioni** del riquadro **Dettagli** per un evento di controllo. Esistono due tipi di destinazione per i ruoli Azure AD:

    - Role (**Type** = Role)
    - Il richiedente (**tipo** = utente)

In genere, l'evento del log di controllo immediatamente sopra l'evento di approvazione è un evento per "aggiunta di un membro al ruolo completato", in cui il richiedente è stato **avviato da (attore)** . Nella maggior parte dei casi non è necessario trovare il richiedente nella richiesta di approvazione dal punto di vista del controllo.

---

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in Privileged Identity Management](azure-pim-resource-rbac.md)
