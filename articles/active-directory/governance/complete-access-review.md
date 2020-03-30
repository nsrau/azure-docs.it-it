---
title: Completare una verifica di accesso di gruppi & applicazioni - Azure ADComplete an access review of groups & applications - Azure AD
description: Informazioni su come completare una verifica di accesso dei membri del gruppo o dell'accesso alle applicazioni nelle verifiche di accesso ad Azure Active Directory.Learn how to complete an access review of group members or application access in Azure Active Directory access reviews.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266234f2872cfe99509d564c9460bfba4a0e2bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75932536"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Completare una verifica di accesso di gruppi e applicazioni nelle verifiche di accesso di Azure ADComplete an access review of groups and applications in Azure AD access reviews

In qualità di amministratore, si crea una [verifica di accesso di gruppi o applicazioni](create-access-review.md) e i revisori [eseguono la verifica di accesso.](perform-access-review.md) In questo articolo viene descritto come visualizzare i risultati della verifica di accesso e applicare i risultati.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Prerequisiti

- Azure AD Premium P2
- Amministratore globale, Amministratore utente, Amministratore sicurezza o Lettore sicurezza

Per altre informazioni, vedere [Requisiti relativi alle licenze](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Visualizzare una verifica di accesso

È possibile tenere traccia dello stato di avanzamento man mano che i revisori completano le recensioni.

1. Accedere al portale di Azure e aprire la [pagina Governance identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Nel menu a sinistra, fai clic su **Revisioni di accessi.**

1. Nell'elenco fare clic su una verifica di accesso.

    Per visualizzare una serie di verifiche di accesso, passare alla verifica di accesso e trovare le occorrenze imminenti in Revisioni pianificate.

    Nella pagina **Panoramica** è possibile visualizzare lo stato di avanzamento. Nessun diritto di accesso viene modificato nella directory fino al completamento della verifica.

    ![Stato di avanzamento delle revisioni di accesso](./media/complete-access-review/overview-progress.png)

1. Se si desidera interrompere una verifica di accesso prima che abbia raggiunto la data di fine pianificata, fare clic sul pulsante **Interrompi.**

    Quando si interrompe una revisione, i revisori non saranno più in grado di fornire risposte. Non è possibile riavviare una verifica dopo che è stata interrotta.

1. Se non sei più interessato alla verifica dell'accesso, puoi eliminarla facendo clic sul pulsante **Elimina.**

## <a name="apply-the-changes"></a>Applicare le modifiche.

Se **l'applicazione automatica dei risultati alla risorsa** è stata abilitata e in base alle selezioni effettuate in Al **completamento delle impostazioni,** l'applicazione automatica verrà eseguita dopo la data di fine della revisione o quando si interrompe manualmente la revisione.

Se **l'applicazione automatica** dei risultati alla risorsa non è stata abilitata per la revisione, fare clic su **Applica** per applicare manualmente le modifiche. Se l'accesso di un utente è stato negato durante la revisione, quando si fa clic su **Applica**, Azure AD rimuove l'appartenenza o l'assegnazione dell'applicazione.

![Applicare le modifiche della revisione di accessoApply access review changes](./media/complete-access-review/apply-changes.png)

Lo stato della revisione cambierà da **Completato** a Stati intermedi come **Applicazione** e infine allo stato **Risultato applicato**. È possibile che, dopo alcuni minuti, eventuali utenti rifiutati vengano rimossi dall'appartenenza al gruppo o dall'assegnazione dell'applicazione.

Se per una verifica si configura l'applicazione automatica o si seleziona **Applica**, questa operazione non avrà alcun effetto su un gruppo che ha origine in una directory locale o in un gruppo dinamico. Per modificare un gruppo che ha origine in locale, scaricare i risultati e applicare queste modifiche alla rappresentazione del gruppo in questa directory.

## <a name="retrieve-the-results"></a>Recuperare i risultati

Per visualizzare i risultati di una verifica di accesso una tantera, fare clic sulla pagina **Risultati.** Per visualizzare solo l'accesso di un utente, nella casella Cerca digitare il nome visualizzato o il nome dell'entità utente di un utente il cui accesso è stato esaminato.

![Recuperare i risultati per una verifica di accessoRetrieve results for an access review](./media/complete-access-review/retrieve-results.png)

Per visualizzare lo stato di avanzamento di una verifica di accesso attiva ricorrente, fare clic sulla pagina **Risultati.**

Per visualizzare i risultati di un'istanza completata di una verifica di accesso ricorrente, fare clic su **Cronologia revisioni**, quindi selezionare l'istanza specifica dall'elenco delle istanze di verifica di accesso completate, in base alla data di inizio e di fine dell'istanza. I risultati di questa istanza possono essere ottenuti dalla pagina **Risultati.**

Per recuperare tutti i risultati di una verifica di accesso, fare clic sul pulsante **Download.** Il file CSV risultante può essere visualizzato in Excel o in altri programmi aperti in file CSV con codifica UTF-8.

## <a name="remove-users-from-an-access-review"></a>Rimuovere utenti da una verifica di accesso

 Per impostazione predefinita, un utente eliminato rimarrà comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario.  Dopo 30 giorni l'utente verrà eliminato definitivamente.  Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](../fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo.  Dopo l'eliminazione definitiva di un utente, i dati riguardanti tale utente verranno rimossi dalle verifiche di accesso attive.  Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso utente con le verifiche di accesso di Azure AD](manage-user-access-with-access-reviews.md)
- [Gestire l'accesso guest con le verifiche di accesso di Azure AD](manage-guest-access-with-access-reviews.md)
- [Creare una verifica di accesso di gruppi o applicazioniCreate an access review of groups or applications](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
