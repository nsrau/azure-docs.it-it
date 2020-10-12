---
title: Visualizzare il report di controllo per i ruoli delle risorse di Azure in Privileged Identity Management (PIM)-Azure AD | Microsoft Docs
description: Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743151"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in Privileged Identity Management

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile visualizzare la cronologia di attività, attivazioni e controlli per i ruoli delle risorse di Azure all'interno dell'organizzazione, tra cui sottoscrizioni, gruppi di risorse e anche macchine virtuali. Tutte le risorse all'interno del portale di Azure che sfruttano le funzionalità di controllo degli accessi in base al ruolo di Azure possono sfruttare le funzionalità di sicurezza e gestione del ciclo di vita di Privileged Identity Management.

> [!NOTE]
> Se l'organizzazione dispone di funzioni di gestione esternalizzate a un provider di servizi che usa la [gestione delle risorse delegate di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), le assegnazioni di ruolo autorizzate dal provider di servizi non verranno visualizzate qui.

## <a name="view-activity-and-activations"></a>Visualizzare l'attività e le attivazioni

Se è necessario verificare quali azioni sono state effettuate da un utente specifico su diverse risorse, è possibile visualizzare l'attività di Risorse di Azure associata a un periodo di attivazione specifico.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **risorse di Azure**.

1. Selezionare la risorsa per cui si desidera visualizzare le attività e le attivazioni.

1. Selezionare **ruoli** o **membri**.

1. Selezionare un utente.

    Viene visualizzato un riepilogo delle azioni dell'utente nelle risorse di Azure in base alla data. Mostra inoltre le attivazioni di ruolo recenti nell’arco dello stesso periodo di tempo.

    ![Dettagli dell'utente con riepilogo delle attività delle risorse e attivazioni dei ruoli](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Selezionare un'attivazione di ruolo specifica per visualizzare i dettagli e l'attività corrispondente delle risorse di Azure che si sono verificati quando l'utente era attivo.

    [![Attivazione del ruolo selezionata e dettagli attività](media/azure-pim-resource-rbac/export-membership.png "Attivazione del ruolo selezionata e dettagli attività")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Esportare le assegnazioni di ruolo con gli elementi figlio

Potrebbe essere previsto un requisito di conformità in base al quale è necessario fornire un elenco completo di assegnazione di ruolo ai revisori. Privileged Identity Management consente di eseguire query sulle assegnazioni di ruolo in una risorsa specifica, incluse le assegnazioni di ruolo per tutte le risorse figlio. In precedenza risultava difficile per gli amministratori ottenere un elenco completo di assegnazioni di ruolo per una sottoscrizione ed era necessario esportare le assegnazioni di ruolo per ogni risorsa specifica. Utilizzando Privileged Identity Management, è possibile eseguire una query per tutte le assegnazioni di ruolo attive e idonee in una sottoscrizione, incluse le assegnazioni di ruolo per tutti i gruppi di risorse e le risorse.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **risorse di Azure**.

1. Selezionare la risorsa per cui si desidera esportare le assegnazioni di ruolo, ad esempio una sottoscrizione.

1. Selezionare **Membri**.

1. Selezionare **Esporta** per aprire il riquadro Esporta appartenenza.

    [![Esporta riquadro appartenenza per esportare tutti i membri](media/azure-pim-resource-rbac/export-membership.png "Esporta pagina di appartenenza per esportare tutti i membri")](media/azure-pim-resource-rbac/export-membership.png)

1. Selezionare **Esporta tutti i membri** per esportare tutte le assegnazioni di ruolo in un file CSV.

    ![Assegnazioni di ruolo esportate nel file CSV come visualizzate in Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Visualizzare la cronologia dei controlli delle risorse

L'opzione Controllo delle risorse fornisce una visualizzazione di tutte le attività del ruolo per una risorsa.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **risorse di Azure**.

1. Selezionare la risorsa per cui si vuole visualizzare la cronologia di controllo.

1. Selezionare **controllo risorse**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    [![Elenco di controllo delle risorse con filtri](media/azure-pim-resource-rbac/rbac-resource-audit.png "Elenco di controllo delle risorse con filtri")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Per **Tipo di controllo**, selezionare **Attiva (assegnato + attivato)**.

    [![Elenco di controllo delle risorse filtrato in base al tipo](media/azure-pim-resource-rbac/rbac-audit-activity.png "Elenco di controllo delle risorse filtrato per attivazione")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ di controllo Activate Elenco di controllo delle risorse filtrato in base al tipo di controllo Activate](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. In **Azione** fare clic su **(attività)** per un utente per visualizzare il dettaglio dell'attività di tale utente nelle risorse di Azure.

    ![Dettagli attività utente per un'azione specifica](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Visualizzare Controllo personale

Controllo personale consente di visualizzare l'attività del ruolo personale.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **risorse di Azure**.

1. Selezionare la risorsa per cui si vuole visualizzare la cronologia di controllo.

1. Selezionare **My audit**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    [![Elenco di controllo per l'utente corrente](media/azure-pim-resource-rbac/my-audit-time.png "Elenco di controllo per l'utente corrente")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Per accedere alla cronologia di controllo è necessario un ruolo amministratore globale o amministratore del ruolo con privilegi.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Ottieni motivo, responsabile approvazione e numero di ticket per gli eventi di approvazione

1. Accedere al [portale di Azure](https://aad.portal.azure.com) con le autorizzazioni del ruolo amministratore del ruolo con privilegi e aprire Azure ad.
1. Selezionare **Log di controllo**.
1. Usare il filtro del **servizio** per visualizzare solo gli eventi di controllo per il servizio Privileged Identity Management. Nella pagina **log di controllo** è possibile:

    - Vedere la causa di un evento di controllo nella colonna **motivo dello stato** .
    - Vedere il responsabile approvazione nella colonna **Iniziato da (attore)** per l'evento "Aggiungi membro a richiesta di ruolo approvato".

    [![Filtrare il log di controllo per il servizio PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrare il log di controllo per il servizio PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Selezionare un evento del registro di controllo per visualizzare il numero del ticket nella scheda **attività** del riquadro **Dettagli** .
  
    [![Controllare il numero di ticket per l'evento di controllo](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Controllare il numero di ticket per l'evento di controllo")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. È possibile visualizzare il richiedente (persona che attiva il ruolo) nella scheda **destinazioni** del riquadro **Dettagli** per un evento di controllo. Sono disponibili tre tipi di destinazione per i ruoli delle risorse di Azure:

    - Role (**Type** = Role)
    - Il richiedente (**tipo** = other)
    - Responsabile approvazione (**tipo** = utente)

    [![Verificare il tipo di destinazione](media/azure-pim-resource-rbac/audit-event-target-type.png "Verificare il tipo di destinazione")](media/azure-pim-resource-rbac/audit-event-target-type.png)

In genere, l'evento log immediatamente sopra l'evento di approvazione è un evento per l'aggiunta di un membro a un ruolo completato in cui il richiedente è stato **avviato da (attore)** . Nella maggior parte dei casi non è necessario trovare il richiedente nella richiesta di approvazione dal punto di vista del controllo.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Approva o rifiuta le richieste per i ruoli delle risorse di Azure in Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Visualizzare la cronologia di controllo per i ruoli di Azure AD in Privileged Identity Management](pim-how-to-use-audit-log.md)
