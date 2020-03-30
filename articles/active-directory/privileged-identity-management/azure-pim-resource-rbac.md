---
title: Visualizzare il report di controllo per i ruoli delle risorse di Azure in Privileged Identity Management (PIM) - Azure AD Documenti Microsoft
description: Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329611"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Visualizzare la cronologia attività e di controllo per i ruoli delle risorse di Azure in Gestione identità con privilegiView activity and audit history for Azure resource roles in Privileged Identity Management

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile visualizzare la cronologia di attività, attivazioni e controlli per i ruoli delle risorse di Azure all'interno dell'organizzazione, tra cui sottoscrizioni, gruppi di risorse e anche macchine virtuali. Qualsiasi risorsa all'interno del portale di Azure che sfrutta la funzionalità di controllo degli accessi in base al ruolo di Azure può sfruttare le funzionalità di sicurezza e gestione del ciclo di vita in Gestione delle identità con privilegi.

> [!NOTE]
> Se l'organizzazione ha esternalizzato le funzioni di gestione a un provider di servizi che utilizza la gestione delle risorse delegate di [Azure,](../../lighthouse/concepts/azure-delegated-resource-management.md)le assegnazioni di ruolo autorizzate da tale provider di servizi non verranno visualizzate qui.

## <a name="view-activity-and-activations"></a>Visualizzare l'attività e le attivazioni

Se è necessario verificare quali azioni sono state effettuate da un utente specifico su diverse risorse, è possibile visualizzare l'attività di Risorse di Azure associata a un periodo di attivazione specifico.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

1. Selezionare la risorsa per cui si desidera visualizzare l'attività e le attivazioni.

1. Selezionare **Ruoli** o **Membri**.

1. Selezionare un utente.

    Viene visualizzato un riepilogo delle azioni dell'utente nelle risorse di Azure per data. Mostra inoltre le attivazioni di ruolo recenti nell’arco dello stesso periodo di tempo.

    ![Dettagli utente con riepilogo delle attività delle risorse e attivazioni dei ruoliUser details with resource activity summary and role activations](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Selezionare un'attivazione del ruolo specifica per visualizzare i dettagli e l'attività delle risorse di Azure corrispondente che si è verificata mentre l'utente era attivo.

    [![Attivazione dei ruoli selezionata e dettagli dell'attività](media/azure-pim-resource-rbac/export-membership.png "Attivazione dei ruoli selezionata e dettagli dell'attività")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Esportare le assegnazioni di ruolo con gli elementi figlio

Potrebbe essere previsto un requisito di conformità in base al quale è necessario fornire un elenco completo di assegnazione di ruolo ai revisori. Gestione identità con privilegi consente di eseguire query sulle assegnazioni di ruolo in una risorsa specifica, incluse le assegnazioni di ruolo per tutte le risorse figlio. In precedenza risultava difficile per gli amministratori ottenere un elenco completo di assegnazioni di ruolo per una sottoscrizione ed era necessario esportare le assegnazioni di ruolo per ogni risorsa specifica. Utilizzando Gestione identità con privilegi, è possibile eseguire una query per tutte le assegnazioni di ruolo attive e idonee in una sottoscrizione, incluse le assegnazioni di ruolo per tutti i gruppi di risorse e le risorse.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

1. Selezionare la risorsa per cui si desidera esportare le assegnazioni di ruolo, ad esempio una sottoscrizione.

1. Selezionare **Membri**.

1. Selezionare **Esporta** per aprire il riquadro di appartenenza All'esportazione.

    [![Esportare il riquadro delle appartenenze per esportare tutti i membriExport membership pane to export all members](media/azure-pim-resource-rbac/export-membership.png "Esportare la pagina dei membri per esportare tutti i membri")](media/azure-pim-resource-rbac/export-membership.png)

1. Selezionare **Esporta tutti i membri** per esportare tutte le assegnazioni di ruolo in un file CSV.

    ![Assegnazioni di ruolo esportate nel file CSV come display in Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Visualizzare la cronologia dei controlli delle risorse

L'opzione Controllo delle risorse fornisce una visualizzazione di tutte le attività del ruolo per una risorsa.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

1. Selezionare la risorsa per cui si desidera visualizzare la cronologia di controllo.

1. Selezionare **Controllo risorse**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    [![Elenco di controllo delle risorse con filtri](media/azure-pim-resource-rbac/rbac-resource-audit.png "Elenco di controllo delle risorse con filtri")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Per **Tipo di controllo**, selezionare **Attiva (assegnato + attivato)**.

    [![Elenco di controllo delle risorse filtrato in base all'attivazione del tipo di](media/azure-pim-resource-rbac/rbac-audit-activity.png "Elenco di controllo delle risorse filtrato per attivazione")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![controllo Elenco di controllo delle risorse filtrato in base all'attivazione del tipo di controllo](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. In **Azione** fare clic su **(attività)** per un utente per visualizzare il dettaglio dell'attività di tale utente nelle risorse di Azure.

    ![Dettagli dell'attività utente per una determinata azione](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Visualizzare Controllo personale

Controllo personale consente di visualizzare l'attività del ruolo personale.

1. Aprire **Gestione identità con privilegi**di Azure AD .

1. Selezionare **Risorse di Azure**.

1. Selezionare la risorsa per cui si desidera visualizzare la cronologia di controllo.

1. Selezionare **Il mio audit**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    [![Elenco di controllo per l'utente corrente](media/azure-pim-resource-rbac/my-audit-time.png "Elenco di controllo per l'utente corrente")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> L'accesso alla cronologia di controllo richiede un ruolo amministratore globale o amministratore ruolo con privilegi.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Ottenere motivo, approvatore e numero di ticket per gli eventi di approvazioneGet reason, approver, and ticket number for approval events

1. Accedere al [portale](https://aad.portal.azure.com) di Azure con autorizzazioni per il ruolo di amministratore del ruolo con privilegi e aprire Azure AD.
1. Selezionare **Registri di controllo**.
1. Utilizzare il filtro **Servizio** per visualizzare solo gli eventi di controllo per il servizio Gestione identità con privilegi. Nella pagina **Registri di controllo** è possibile:

    - Vedere il motivo di un evento di controllo nella colonna **Motivo stato.**
    - Vedere l'approvatore nella colonna **Iniziato da (attore)** per l'evento "Aggiungi membro alla richiesta di ruolo approvato".

    [![Filtrare il log di controllo per il servizio PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrare il log di controllo per il servizio PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Selezionare un evento del registro di controllo per visualizzare il numero di ticket nella scheda **Attività** del riquadro **Dettagli.**
  
    [![Controllare il numero del biglietto per l'evento di controllo](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Controllare il numero del biglietto per l'evento di audit")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. È possibile visualizzare il richiedente (persona che attiva il ruolo) nella scheda **Destinazioni** del riquadro **Dettagli** per un evento di controllo. Esistono tre tipi di destinazione per i ruoli delle risorse di Azure:There are three target types for Azure resource roles:

    - Il ruolo (**Tipo** - Ruolo)
    - Il richiedente (**Tipo** - Altro)
    - L'approvatore (**Tipo** - Utente)

    [![Controllare il tipo di destinazione](media/azure-pim-resource-rbac/audit-event-target-type.png "Controllare il tipo di destinazione")](media/azure-pim-resource-rbac/audit-event-target-type.png)

In genere, l'evento di log immediatamente sopra l'evento di approvazione è un evento per "Aggiungi membro al ruolo completato" in cui l'attore **Avviato da è** il richiedente. Nella maggior parte dei casi, non è necessario trovare il richiedente nella richiesta di approvazione dal punto di vista del controllo.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare ruoli delle risorse di Azure in Gestione identità con privilegiAssign Azure resource roles in Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in Gestione delle identità con privilegiApprove or deny requests for Azure resource roles in Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Visualizzare la cronologia di controllo per i ruoli di Azure AD in Gestione delle identità con privilegiView audit history for Azure AD roles in Privileged Identity Management](pim-how-to-use-audit-log.md)
