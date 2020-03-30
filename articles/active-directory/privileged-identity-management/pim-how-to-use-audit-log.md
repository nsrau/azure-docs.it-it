---
title: Visualizzare il report del log di controllo per i ruoli di Azure AD in Azure AD PIM Documenti Microsoft
description: Informazioni su come visualizzare la cronologia del log di controllo per i ruoli di Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329511"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Visualizzare la cronologia di controllo per i ruoli di Azure AD in Gestione delle identità con privilegiView audit history for Azure AD roles in Privileged Identity Management

È possibile utilizzare la cronologia di controllo di Privileged Identity Management (PIM) per visualizzare tutte le assegnazioni di ruolo e le attivazioni negli ultimi 30 giorni per tutti i ruoli con privilegi. Se si vuole visualizzare la cronologia di controllo completa delle attività nell'organizzazione di Azure Active Directory (Azure AD), inclusi l'amministratore, l'utente finale e l'attività di sincronizzazione, è possibile usare i report sulla sicurezza e sull'attività di [Azure Active Directory.](../reports-monitoring/overview-reports.md)

## <a name="determine-your-version-of-pim"></a>Determinare la versione di PIM

A partire da novembre 2019, la parte dei ruoli di Azure AD di Gestione delle identità con privilegi viene aggiornata a una nuova versione che corrisponde alle esperienze per i ruoli delle risorse di Azure.Start in November 2019, the Azure AD roles portion of Privileged Identity Management is being updated to a new version that matches the experiences for Azure resource roles. In questo modo vengono create funzionalità aggiuntive e [modifiche all'API esistente.](azure-ad-roles-features.md#api-changes) Durante l'implementazione della nuova versione, le procedure seguite in questo articolo dipendono dalla versione di Privileged Identity Management attualmente in uso. Seguire i passaggi descritti in questa sezione per determinare la versione di Gestione identità con privilegi in uso. Dopo aver conosciuto la versione di Gestione identità privilegiate, è possibile selezionare le procedure descritte in questo articolo che corrispondono a tale versione.

1. Accedere al [portale](https://portal.azure.com/) di Azure con un utente che risieda il ruolo di amministratore del ruolo Privileged.Sign in to the Azure portal with a user who is in the [Privileged role administrator](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) role role role.
1. Aprire **Gestione identità con privilegi**di Azure AD . Se hai un banner nella parte superiore della pagina di panoramica, segui le istruzioni nella scheda **Nuova versione** di questo articolo. In caso contrario, seguire le istruzioni nella scheda **Versione precedente.**

    [![Nuova versione dei ruoli di Azure ADAzure AD roles new version](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Selezionare la scheda per la versione")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nuova versione](#tab/new)

Seguire questi passaggi per visualizzare la cronologia di controllo per i ruoli di Azure AD.

## <a name="view-resource-audit-history"></a>Visualizzare la cronologia dei controlli delle risorse

Il controllo delle risorse offre una visualizzazione di tutte le attività associate ai ruoli di Azure AD.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Controllo risorse**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo delle risorse con filtri](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Visualizzare Controllo personale

Controllo personale consente di visualizzare l'attività del ruolo personale.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare la risorsa per cui si desidera visualizzare la cronologia di controllo.

1. Selezionare **Il mio audit**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo per l'utente corrente](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Versione precedente](#tab/previous)

## <a name="view-audit-history"></a>Visualizzare la cronologia di controllo

Seguire questi passaggi per visualizzare la cronologia di controllo per i ruoli di Azure AD.

1. Accedere al [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo [Amministratore dei ruoli con privilegi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Ruoli di Azure AD**.

1. Selezionare **Cronologia controllo ruoli directory**.

    A seconda della cronologia di controllo, viene visualizzato un istogramma insieme alle attivazioni totali, al numero massimo di attivazioni al giorno e alle attivazioni medie al giorno.

    [![Nuova versione dei ruoli di Azure ADAzure AD roles new version](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Visualizzare la cronologia di controllo dei ruoli della directory")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    Nella parte inferiore della pagina viene visualizzata una tabella con informazioni su ogni azione nella cronologia di controllo disponibile. Le colonne hanno i seguenti significati:

    | Colonna | Descrizione |
    | --- | --- |
    | Tempo | Quando si è verificata l'azione. |
    | Richiedente | Utente che ha richiesto l'attivazione o la modifica del ruolo. Se il valore è **Sistema di Azure,** controllare la cronologia di controllo di Azure per altre informazioni. |
    | Azione | Azioni eseguite dal richiedente. Le azioni possono includere Assign, Unassign, Activate, Deactivate o AddedOutsidePIM. |
    | Membro | Utente che sta attivando o assegne a un ruolo. |
    | Ruolo | Ruolo assegnato o attivato dall'utente. |
    | Ragionamento | Testo immesso nel campo del motivo durante l'attivazione. |
    | Scadenza | Quando un ruolo attivato scade. Si applica solo alle assegnazioni di ruolo idonee. |

1. Per ordinare la cronologia di controllo, fare clic sui pulsanti **Ora,** **Azione**e **Ruolo.**

## <a name="filter-audit-history"></a>Filtrare la cronologia dei controlli

1. Nella parte superiore della pagina della cronologia di controllo fare clic sul pulsante **Filtro.**

    Viene visualizzato il riquadro **Aggiorna parametri grafico.**

1. In **Intervallo**di tempo selezionare un intervallo di tempo.

1. In **Ruoli**selezionare le caselle di controllo per indicare i ruoli che si desidera visualizzare.

    ![Riquadro Aggiorna parametri grafico](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Selezionare **Fatto** per visualizzare la cronologia di controllo filtrata.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Ottenere motivo, approvatore e numero di ticket per gli eventi di approvazioneGet reason, approver, and ticket number for approval events

1. Accedere al [portale](https://aad.portal.azure.com) di Azure con autorizzazioni per il ruolo di amministratore del ruolo con privilegi e aprire Azure AD.
1. Selezionare **Registri di controllo**.
1. Utilizzare il filtro **Servizio** per visualizzare solo gli eventi di controllo per il servizio Gestione identità con privilegi. Nella pagina **Registri di controllo** è possibile:

    - Vedere il motivo di un evento di controllo nella colonna **Motivo stato.**
    - Vedere l'approvatore nella colonna **Iniziato da (attore)** per l'evento "Aggiungi membro alla richiesta di ruolo approvato".

    [![Nuova versione dei ruoli di Azure ADAzure AD roles new version](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrare il log di controllo per il servizio PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Selezionare un evento del registro di controllo per visualizzare il numero di ticket nella scheda **Attività** del riquadro **Dettagli.**
  
    [![Nuova versione dei ruoli di Azure ADAzure AD roles new version](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Controllare il numero del biglietto per l'evento di audit")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. È possibile visualizzare il richiedente (persona che attiva il ruolo) nella scheda **Destinazioni** del riquadro **Dettagli** per un evento di controllo. Esistono due tipi di destinazione per i ruoli di Azure AD:There are two target types for Azure AD roles:

    - Il ruolo (**Tipo** - Ruolo)
    - Il richiedente (**Tipo** e Utente)

In genere, l'evento del registro di controllo immediatamente sopra l'evento di approvazione è un evento per "Aggiungi membro al ruolo completato" in cui l'attore **avviato** da è il richiedente. Nella maggior parte dei casi, non è necessario trovare il richiedente nella richiesta di approvazione dal punto di vista del controllo.

---

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare la cronologia attività e di controllo per i ruoli delle risorse di Azure in Gestione identità con privilegiView activity and audit history for Azure resource roles in Privileged Identity Management](azure-pim-resource-rbac.md)
