---
title: Visualizzare le attività e la cronologia di controllo per i ruoli delle risorse di Azure in PIM-Azure Active Directory | Microsoft Docs
description: Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40e1ed018f66d4f5eefd02d587504cf64969f47b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804042"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in PIM

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), è possibile visualizzare la cronologia di attività, attivazioni e controlli per i ruoli delle risorse di Azure all'interno dell'organizzazione, tra cui sottoscrizioni, gruppi di risorse e anche macchine virtuali. Ogni risorsa nel portale di Azure che sfrutta la funzionalità Controllo degli accessi in base al ruolo di Azure può usare tutte le funzionalità di sicurezza e gestione del ciclo di vita in PIM.

## <a name="view-activity-and-activations"></a>Visualizzare l'attività e le attivazioni

Se è necessario verificare quali azioni sono state effettuate da un utente specifico su diverse risorse, è possibile visualizzare l'attività di Risorse di Azure associata a un periodo di attivazione specifico.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

1. Fare clic sulla risorsa per cui si vogliono visualizzare l'attività e le attivazioni.

1. Fare clic su **Ruoli** o **Membri**.

1. Fare clic su un utente.

    Si apre una visualizzazione grafica delle azioni dell'utente in Risorse di Azure divise per data. Mostra inoltre le attivazioni di ruolo recenti nell’arco dello stesso periodo di tempo.

    ![Dettagli dell'utente con riepilogo delle attività delle risorse e attivazioni dei ruoli](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Fare clic su un'attivazione del ruolo specifica per visualizzare i dettagli e l'attività di Risorse di Azure corrispondente effettuata nel periodo in cui l'utente è risultato attivo.

    ![Attivazione del ruolo selezionata e dettagli attività visualizzati per data](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Esportare le assegnazioni di ruolo con gli elementi figlio

Potrebbe essere previsto un requisito di conformità in base al quale è necessario fornire un elenco completo di assegnazione di ruolo ai revisori. PIM consente di eseguire query delle assegnazioni di ruolo in una risorsa specifica, incluse le assegnazioni di ruolo per tutte le risorse figlio. In precedenza, era difficile per gli amministratori ottenere un elenco completo delle assegnazioni di ruolo per una sottoscrizione. Per questo era necessario esportare le assegnazioni di ruolo per ogni risorsa specifica. Con PIM è possibile cercare tutte le assegnazioni di ruolo attive e idonee di una sottoscrizione, incluse quelle per tutti i gruppi di risorse e tutte le risorse.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

1. Fare clic sulla risorsa di cui esportare le assegnazioni di ruolo, ad esempio una sottoscrizione.

1. Fare clic su **Membri**.

1. Fare clic su **Esporta** per aprire il riquadro Esporta l'appartenenza.

    ![Esporta riquadro appartenenza per esportare tutti i membri](media/azure-pim-resource-rbac/export-membership.png)

1. Fare clic su **Esporta tutti i membri** per esportare tutte le assegnazioni di ruolo in un file CSV.

    ![Assegnazioni di ruolo esportate nel file con estensione CSV come visualizzato in Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Visualizzare la cronologia dei controlli delle risorse

L'opzione Controllo delle risorse fornisce una visualizzazione di tutte le attività del ruolo per una risorsa.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

1. Fare clic sulla risorsa per cui si vuole visualizzare la cronologia dei controlli.

1. Fare clic su **Controllo delle risorse**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo delle risorse con filtri](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Per **Tipo di controllo**, selezionare **Attiva (assegnato + attivato)** .

    ![Elenco di controllo delle risorse filtrato in base al tipo di controllo Activate](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. In **Azione** fare clic su **(attività)** per un utente per visualizzare il dettaglio dell'attività di tale utente nelle risorse di Azure.

    ![Dettagli attività utente per un'azione specifica](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Visualizzare Controllo personale

Controllo personale consente di visualizzare l'attività del ruolo personale.

1. Aprire **Azure AD Privileged Identity Management**.

1. Fare clic su **Risorse di Azure**.

1. Fare clic sulla risorsa per cui si vuole visualizzare la cronologia dei controlli.

1. Fare clic su **Controllo personale**.

1. Filtrare la cronologia usando una data predefinita o un intervallo personalizzato.

    ![Elenco di controllo per l'utente corrente](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare i ruoli delle risorse di Azure in PIM](pim-resource-roles-assign-roles.md)
- [Approvare o rifiutare le richieste per i ruoli delle risorse di Azure in PIM](pim-resource-roles-approval-workflow.md)
- [Visualizzare la cronologia di controllo per i ruoli Azure AD in PIM](pim-how-to-use-audit-log.md)
